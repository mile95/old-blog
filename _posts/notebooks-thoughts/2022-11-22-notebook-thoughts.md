---
title: "My thoughts of (python) Notebooks"
date: 2022-11-22T10:48:19+02:00
tags: [data analysis, python]
author: Fredrik Mile
---

I've seen projects where notebooks creates great value, but I've also seen projects where they create a mess.
The difference between does projects has been the way these notebooks has been structured and used.

I'm reffering to [jupyter notebooks](https://jupyter.org/) and [databricks notebooks](https://docs.databricks.com/notebooks/index.html), which are both great tools created for data analysis.
Notebooks are a mix of live code, visualizations, and markdown which is most often run and developed using a web-based interactive computational environment, like Jupyter.

So, when should notebooks be used to great the most value?

## When to use notebooks

There's a reason why notebooks are popular.
They are easy to use and get started with and they do some stuff really well.

### Exploration

Notebooks are made for interactive development since they allow for cell based execution of code.
This quality makes notebooks a perfect match for **exploration**.
For instance if you need to explore or play around with a dataset or an API, notebooks really makes this is easy and enjoyable due to the cell based execution and the short feedback loop.

### Reports

Notebooks are mostly common in data science projects and since notebooks are a mix of text, visualitions and code they work great as a report tool.

If the task is to do some analysis on a dataset and present a report to the customer, notebooks are the the perfect match. Especially since the notebook can be exported (with code, visualitaions, and text) as pdf, making the report aviable for everyone.
The code in the notebook exists only for the report and is not planned to be used anywhere else, the notebook is a isolated delivery.

## When to NOT use notebooks

Most often, code written in notebooks can be extracted to plain python scripts or modules.
When working with plain python code it's way easier to setup and maintain the structure the project compared to having multiple isolated notebooks. 

My suggestion is to **not use notebooks in production**, and there's a few reasons for that.

1. Code in notebooks is not easy testable. 
1. Code in notebooks is not reusable. 
It not possible to import code from one notebook to another one without running the notebook which can cause execution of unwanted code.
1. The order of execution is not defined and clear. Running cells in arbitrary order can cause unexpected behavoiur.


## My rules 

Use notebooks for exploration and reports only.
When the exploration is finished, extract the usable code and move it into standard python modules and start building a python project.
I always aim for running all code locally and not be dependent on specific IDE's or other development environment.
This creates freedom and adaptability.

Depending on the nature of the project you can be forced to use notebooks in production.
For instance if you are using [Azure DataFactory](https://azure.microsoft.com/products/data-factory/) and [Azure Databricks](https://azure.microsoft.com/en-us/products/databricks/), my advice is then to view and treat notebooks as the entrypoint (main method). 

