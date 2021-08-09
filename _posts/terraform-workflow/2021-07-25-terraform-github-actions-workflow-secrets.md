---
layout: post
title:  "Terraform with Github Actions - Secret Management"
date:   2021-07-23 10:10:15 +0700
categories: [terraform, aws, github-actions]
---

# Terraform with Github Actions - Secret Management
Github recently released early access to their [CoPilot](https://copilot.github.com/) - *"Your AI pair programmer"*. The reactions have been mixed, which one could have guessed. I think the tool is awesome, and I can see myself using it in the future,  but I think it is far from ready for being used in daily work yet. In addition to all the positive feedback, some less good things have also emerged. Reports show that the CoPilot has been leaking functional API keys, see more [here](https://www.theregister.com/2021/07/06/github_copilot_autocoder_caught_spilling/). The AI learns from the actual code that people write and open source on Github. If people store sensitive information directly in the source code, the AI will of course learn those as well.     

I recently tried out Terraform with Github Actions for automating infrastructure and deployment. I soon realized that I had a lot of secrets to manage. These were secrets from the application itself and the infrastructure. In this blog post, I explain how I kept those secrets safe while using Terraform and Github Actions fully.   

In a previous [post](https://mile95.github.io/twitter-bot/), I explained how I created a Twitter Bot and deployed it manually as a λ-function in AWS. I will use this Twitter Bot as the POC for this post. The source code referred to in this blog post is available [here](https://github.com/mile95/gbg-field-status-bot).

## The required infrastructure
The infrastructure around an application is almost as important as the application itself. For the Twitter Bot to function at its best, two AWS resources were needed. I needed to create a λ-function and a cloud watch trigger in AWS. The base configuration file `configuration.tf` looked like this: 

```terraform
provider "aws" {
  region     = "eu-north-1"
  access_key = ...
  secret_key = ...
}

resource "aws_lambda_function" "TwitterBot" {
  filename         = "lambda.zip"
  function_name    = "TwitterBot"
  role             = "arn:aws:iam::753907798323:role/terra"
  handler          = "lambda_function.lambda_handler"
  source_code_hash = filebase64sha256("lambda.zip")
  runtime          = "python3.8"
  environment {
    variables = {
      ACCESS_TOKEN_SECRET = ...
      ACCESS_TOKEN        = ...
      CONSUMER_API_KEY    = ...
      CONSUMER_API_SECRET = ...
    }
  }
}

module "lambda-cloudwatch-trigger" {
  source                     = "infrablocks/lambda-cloudwatch-events-trigger/aws"
  region                     = "eu-north-1"
  component                  = "TwitterBot"
  deployment_identifier      = "production"
  lambda_arn                 = aws_lambda_function.TwitterBot.arn
  lambda_function_name       = "TwitterBot"
  lambda_schedule_expression = "cron(0 8,10,13 ? * * *)"
}

```

In the `configuration.tf`, I first specify AWS as the provider, which I authenticate myself towards using the access and secret keys. I then define the `λ-resource`, which will output an `ARN`. This `ARN` is used by the `cloud watch module`, for allowing the cloud watch to trigger this exact λ.  A `module` allows for abstracting away re-usable parts, which we can configure once and use everywhere. Modules also allow us to group multiple configured resources into one more specific resource. For the `lambda-cloudwatch-trigger`, I use a module from `infrablocks/lambda-cloudwatch-events-trigger/aws`, for creating lambda triggers in cloud watch in a smooth way.

Already, there are a few secrets to handle
- AWS credentials
- Twitter API credentials

The above terraform configuration file will be used in the Github Actions workflow so let's put the secrets in [Github Secrets](https://docs.github.com/en/actions/reference/encrypted-secrets), which will encrypt them and keep them safe.

## The Github Actions Workflows

I created two different Github Actions Workflows: 

**Plan**: When **creating** a Pull-Request, this workflow leaves a **comment** on PR with the infrastructure changes that merging this PR would result in.

**Deploy**: When **merging** a Pull-Request, the corresponding infrastructure changes will be applied and deployed.

Both of the workflows makes use of the `terraform cli`, more specific the following commands:
  
* **terraform init**: Installs modules, in this case: `infrablocks/lambda-cloudwatch-events-trigger/aws`.
* **terraform plan**: Creates the execution plan that shows what changes will be made to the current infrastructure when the configuration is applied.
* **terraform apply**: Executes the plan! *(Only for the deploy workflow)*'

It is possible to give value to variables defined in the terraform configuration file `configuration.tf` as inputs to the terraform CLI commands, using the `-var`flag. For instance, 

```
$ terraform plan -var var1=val1
```

This allows for using the values stored in Github Secrets as input to terraform configuration when needed. 

You define variables in the `configuration.tf` like below. 

*sensitive = true makes sure that the values are not shown in logs etc. * 

```terraform
variable "AWS_ACCESS_KEY" {
  description = "AWS_ACCESS_KEY"
  type        = string
  sensitive   = true
}

variable "AWS_SECRET_KEY" {
  description = "AWS_SECRET_KEY"
  type        = string
  sensitive   = true
}
```

These variables can now be set with values from Github secrets. For instance, by running the terraform commands like below in a workflow. 

```
$ terraform plan -var AWS_ACCESS_KEY="${{secrets.AWS_ACCESS_KEY}}" -var AWS_SECRET_KEY="${{secrets.AWS_SECRET_KEY}}"
```

### Workflow walkthrough

The basics of the **Plan** workflow looks like this: 

```yml
name: Create terraform plan

on: [pull_request]

jobs:
  Plan-Lambda-Function:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout repository content
        uses: actions/checkout@v2
      
      - name: Create lambda.zip
        run: |
          python -m pip install --upgrade pip
          pip install -r requirements.txt -t .
          zip -r lambda.zip * -x "bin/*" requirements.txt setup.cfg

      - name: Setup Terraform
        uses: hashicorp/setup-terraform@v1
        with:
          terraform_version: 0.14.6

      - name: Terraform Init
        id: init
        run: terraform init
        continue-on-error: true

      - name: Terraform Plan
        id: plan
        run: terraform plan -no-color -var AWS_ACCESS_KEY="${{secrets.AWS_ACCESS_KEY}}" -var AWS_SECRET_KEY="${{secrets.AWS_SECRET_KEY}}"
        continue-on-error: true

      - name: Comment on PR
        uses: actions/github-script@0.9.0
        if: github.event_name == 'pull_request'
        env:
          plan: "${{ steps.plan.outputs.stdout }}"
        with:
          script: |
            const CODE_BLOCK = '```';
            const plan_result = '${{ steps.plan.outcome }}' === 'failure' ? ':x:' : ':heavy_check_mark:';

            const output = `
            ### ${ plan_result } Terraform Plan 📖
            <details><summary>Logs</summary>

            ${ CODE_BLOCK }terraform
            ${ process.env.plan }
            ${ CODE_BLOCK }
            </details>`;

            github.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: output
            })
```
In short, this is what's happening:
1. Define that this workflow should be triggered on the creation of pull requests.
2. Checkout the content of the repository so that the Github Action runner gets access to content.
3. Create the zip file containing the λ code.
4. Install the `terraform cli`
5. Run `terraform init`
6. Run `terraform plan`
7. Create a comment on the pull request with the plan.

The comment can look something like in the image below. I made changes to the lambda code in this pull request, hence `aws_lambda_function`resource is marked as changed. 

![github-comment](/assets/img/terraform-workflow/github_workflow_comment.png)

The **Deploy** workflow is very similar, the only difference is the last step. Instead of commenting the plan, it runs `terraform apply`. 

## State handling in Terraform without leaking secrets

The `terraform plan` computes the infrastructure changes. Terraform needs a way to store information about the current state. Terraform stores the state in a file called `tfstate,  and every time you run `terraform apply`, this file gets updated.

My first idea was to include this file in the git repository on Github, but I realized that even though you define variables as *secret* in the `configuration.tf`, they are fully visible in the `tfstate`file. It is recommended to handle the `tfstate` as a secret itself.

One way to solve this issue is to use an external backend for the terraform, for instance, an AWS s3.

To use the AWS s3 bucket as a backend, changes to the workflows and the `configuration.tf` file had to be made. When an s3 bucket was created in AWS, the following was added to the `configuration.tf`

```terraform
terraform {
  backend "s3" {
    bucket = "fmtfstatebucket"
    key    = "tfstate"
    region = "eu-north-1"
  }
}
```

And the `terraform init`command,  needed to be modified to the below in the Workflows. 

```yml
- name: Run Terraform Init
  run: terraform init -backend-config="access_key=${{secrets.AWS_ACCESS_KEY}}" -backend-config="secret_key=${{secrets.AWS_SECRET_KEY}}"
```
## Summary
- Don't store secrets like API keys, passwords, etc directly in the source code.
- Take advantage of Github secrets for encryption of secrets. These secrets can very easily be used by Github workflow.
- Don't check in the `tfstate`, handle it as a secret. 

See [this](https://github.com/mile95/gbg-field-status-bot) for a real example of using terraform in Github Actions while keeping the secrets safe. 

Thanks for reading 👋!