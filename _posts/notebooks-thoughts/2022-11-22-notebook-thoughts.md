---
title: "My thoughts of (python) Notebooks"
date: 2022-11-22T10:48:19+02:00
tags: [data analysis, python]
author: Fredrik Mile
---
I've seen projects where notebooks create great value, but I've also seen projects where they create a mess.
The difference between projects has been how these notebooks have been structured and used.

I'm referring to [jupyter notebooks](https://jupyter.org/) and [databricks notebooks](https://docs.databricks.com/notebooks/index.html), which are both great tools created for data analysis.
Notebooks are a mix of live code, visualizations, and markdown text that you run and developed via a web-based interactive computational environment, like Jupyter.

So, when should notebooks be used to great the most value?

## When to use notebooks

There's a reason why notebooks are popular.
They are easy to use and get started with and they do some stuff well.

### Exploration

Notebooks are made for interactive development since they allow for cell-based execution of code.
This quality makes notebooks a perfect match for an **exploration** task.
For instance, if you need to explore or play around with a dataset or an API, notebooks make this easy and enjoyable due to the cell-based execution and the short feedback loop.

### Reports

Notebooks are often part of a data science project, and since notebooks are a mix of text, visualizations, and code,  they work great as a reporting tool.

If the task is for you to analyze a dataset and present a report to the customer, notebooks are the perfect match. The notebook can be exported (with code, visualizations, and text) as a pdf, making the report available for everyone.
The code in the notebook exists only for the report and should be used anywhere else. The notebook is an isolated delivery.

## When to NOT use notebooks

Most often, code written in notebooks can be moved into plain python scripts or modules.
When working with plain python code,  it's way easier to set up and maintain the project's structure, compared to having multiple isolated notebooks. 

My suggestion is to **not use notebooks in production**, and there are a few reasons for that.

1. Code in notebooks is not easily testable. 
1. Code in notebooks is not reusable. 
It is not possible to import code from one notebook to another one, without running the notebook, which can cause the execution of unwanted code.
1. The order of execution is not defined. Running cells in arbitrary order can cause unexpected behavior.

## My approach and advice 

Use notebooks for exploration and reports only.
When you have finished the exploration, extract the usable code, move it into plain python modules and start building a python project.
I always aim to have the code runnable locally and not be dependent on specific IDEs or other development environments.
This mindset creates freedom and adaptability.

Depending on the nature of the project, you can be forced to use notebooks in production.
For instance, if you are using [Azure DataFactory](https://azure.microsoft.com/products/data-factory/) and [Azure Databricks](https://azure.microsoft.com/en-us/products/databricks/), my advice is then to view and treat notebooks as the entrypoint (main method) of the business logic.
Declare business logic in plain python modules and then import them into the notebooks.

The structure of the notebook should also follow a standard.
I suggest starting the notebook with an `md` cell containing some information about the notebook.
What does it do?
The second cell should contain all imports, and the third should contain all constant definitions.
Like a standard python module.
The fourth (or last) cell should act as the entrypoint or the main method for the business logic.
In the fourth (or last) cell, the imported logic is executed.
This way of structuring your notebooks facilitates extracting business logic from notebooks into plain python modules, which makes the code testable and reusable.

