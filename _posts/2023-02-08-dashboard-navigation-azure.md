---
title: "Improve Navigation in Azure Using a Dashboard"
date: "2023-02-08T10:48:19+02:00"
tags: [azure]
author: Fredrik Mile
---

I previously found navigation in Azure Portal quite annoying. The go-to was often to search for the specific resource/instance that you wanted to navigate to, which was unusable when you quickly needed to switch between different resources/instances. Probably, there exist many solutions to this problem, but a custom-made [dashboard](https://learn.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards) became my solution. These dashboards are made for monitoring but can easily be transformed for navigation purposes.  

In a recent project, we had four different environments `dev`, `test`, `acc`, and `production`. All of the environments had the same setup of resources but in their own resource group and followed a set naming convention. I used the below dashboard for navigation between resources and environments.

![Screenshot 2023-02-08 at 08 40 03](https://user-images.githubusercontent.com/8545435/217475585-0bf425aa-471f-48f3-81a9-feeabedb8b82.png)

*I've removed the name of all resources, but the name acts like links to the specific resources*

A dashboard like this creates a great overview and simplifies navigation between environments and resources. The tiles used to create the above dashboard are

1. Markdown tile for the headers (`dev02`, `test`,  `acc`, and `prod`)
2. Resources (grouped by resource groups)

**The final tip** is to use the dashboard as the start page in Azure so that when you need to navigate to a resource, you visit the home page and select the resource from the dashboard. Go to `Settings -> Appearance + start views` and select dashboard as the startup page.
