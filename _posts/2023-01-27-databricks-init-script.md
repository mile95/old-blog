---
title: "Install Requirements to Azure Databricks Cluster from requirements.txt"
date: 2023-01-27T10:48:19+02:00
tags: [python, azure, databricks]
author: Fredrik Mile
---

Azure Databricks provides an easy way to install Python libraries for use in your notebooks by using the `Libraries` tab in the user interface, see the image below. However, this method has the drawback of not being version controlled and requiring manual installation on each cluster. To solve this problem, Azure Databricks offers the use of `init scripts` to automate the installation of Python libraries and provide version control. An init script is a script that runs on every node in the cluster when it is created. It can be used to install custom packages, configure settings, and perform other tasks that are necessary to prepare the cluster for use. To use an init script, you can create a script file and upload it to a storage location that is accessible to your Databricks cluster. Then, configure the cluster to run the init script when it starts. This way you can ensure that the requirements for your notebooks are consistently met across all of your clusters.

![Screenshot 2023-01-27 at 16 48 40](https://user-images.githubusercontent.com/8545435/215132455-04642bf0-c1e5-4c5d-b013-958881c84d0f.jpg)

## Init Scripts

An init script is a powerful tool that can be used to configure and customize a Databricks cluster at startup. One of the most common use cases for init scripts is installing Python libraries on the cluster. This allows you to ensure that the same set of libraries are available on all nodes in the cluster and eliminates the need for manual installation. In this demo, we will be using a `requirements.txt` file that lists the Python libraries that need to be installed. The init script will use this file to install the libraries on the cluster, ensuring that all nodes have the same set of libraries available. 

## Deploying the init script and requirements.txt to the cluster

To run the init script, we need to deploy the `requirements.txt` and `init.sh` files to our cluster. One way to do this is by using Azure DevOps Pipelines for CI/CD. The following pipeline step can be added to the deployment pipeline to upload the files to the cluster:

```yaml
- script: |
	pip install databricks-cli
	databricks fs cp --overwrite $(Pipeline.Workspace)/databricks-artifact/requirements.txt dbfs:/requirements.txt

	databricks fs cp --overwrite $(Pipeline.Workspace)/databricks-artifact/init.sh dbfs:/init.sh

displayName: Upload requirements.txt and init.sh to cluster
```

This step installs the databricks-cli, a command-line interface that simplifies uploading files to the cluster. The script then copies the `requirements.txt` and `init.sh` files from the build artifacts to the cluster. The files are copied to the root of the dbfs (Databricks File System) which is mounted into the Databricks workspace and available on Databricks clusters.

It's important to note that, to use `databricks-cli`, the following three environment variables need to be set: DATABRICKS_HOST, DATABRICKS_TOKEN, and DATABRICKS_CLUSTER_ID. These values should never be kept in plain text, it's recommended to use Azure Key Vault or Azure Pipeline Variables to store the secrets.

## Writing the Init script

When running locally, we are used to installing our requirements using `pip install -r requirements.txt`. This same process forms the foundation for our `init script`. However, there are a few modifications that need to be made in order for it to work properly within the Databricks environment.

The `init.sh` script used in this demo looks like:

```bash
#!/bin/bash

/databricks/python/bin/pip install -r /dbfs/requirements.txt
```

The main difference here is that we need to specify the path to the `pip` binary, since we are running the script on a Databricks cluster.

## Configuring the cluster to run the init script

The final step is configuring the cluster to run the init script. This is a one-time setup that can be done through the Databricks UI. To do this, navigate to `compute -> <your cluster> -> Edit -> Advanced Options -> Init Scripts -> Add dbfs:/init.sh`.

Once this is done, the cluster will automatically install all the libraries defined in `requirements.txt` during future cluster starts.

## (Extra) Debugging

Most certainly the `init script` or the configuration will be incorrect for the first few times resulting in the cluster won't start.
To find out what is wrong you will need to debug the `init script`.

Here follows a few tips on how to debug the `init script`

- Enable storing of logs from runs of the `init script`.  The toggle is next to the Init Script tab under Advanced Options. Set the cluster log path `/cluster-logs`
- Use `dbutils` to find the logs. 
	- `dbutils.fs.ls("dbfs:/cluster-logs/<some-id>/init_scripts/<another-id>/<id>_init.sh.stderr.log")`
- Use plain python to read the files
	- `with open(<above_path>) as f: ...`
- Use `dbutils` to update the `init script` like the example below

```python
dbutils.fs.put("/init.sh","""
#!/bin/bash
ls
echo "hello"
/databricks/python/bin/pip install -r /dbfs/requirements.txt""", True)
```

## Other resources

- [CI/CD guide for Databricks](https://learn.microsoft.com/en-us/azure/databricks/dev-tools/ci-cd/ci-cd-azure-devops)
- [dbfs](https://learn.microsoft.com/en-us/azure/databricks/dbfs/)
