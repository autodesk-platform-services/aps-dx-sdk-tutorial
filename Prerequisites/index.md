---
layout: page
title: Prerequisites and SDK Access
nav_order: 2
has_children: false
permalink: /prerequisites/home/
---

# Prerequisites

> _In this tutorial we'll be referring to **Autodesk Construction Cloud** as **ACC** for simplicity_

Before starting, ensure you meet a few key prerequisites to follow this tutorial:

- You will need an ACC account from the **AMER or EMEA region**. The Data Exchange works with Revit 2024+ designs hosted on ACC hubs in the **AMER and EMEA regions**, so it is **required**. If you don't have access to an **AMER or EMEA ACC hub**, you can find options for getting a test account on [this blog](https://fieldofviewblog.wordpress.com/2017/08/31/bim-360-acc-account-for-development/).

- You will need to have an APS account and create an APS App (if you don't have already one). From the created APS App we will need the **ClientID** and **ClientSecret**. Please refer to [this part of documentation](https://get-started.aps.autodesk.com/#create-app-credentials) on how to create an App and get the gredentials.

- Once you have the **ClientID**, you will need to provision it on the ACC. Please make sure it is provisioned by following the steps [How to provision your ClientID on ACC](https://get-started.aps.autodesk.com/#provision-access-in-other-products).

- You will need some C# .Net programming knowledge to follow along. You can start from the scratch, but we recommend starting with **BootstrapConnector** [GitHub repo](https://github.com/autodesk-platform-services/BootstrapDXConnector/tree/start) that contains only the code and the refrence to dependencies or with [Zip repo](https://myshare.autodesk.com/:u:/g/personal/denis_grigor_autodesk_com/ERJ29Oq8VkBLhLpROUhSDm0BlvedRhPKu8rs9UHUVsW7VA?e=lykwXA) that contains all the dependencies already attached to the project.


# SDK Access

Data Exchange .Net SDK is still in **Public Beta** and consequently, some features are subject to change. However, you have the chance to shape the SDK in direction you want by submitting feedbacks, feature requests or found bugs. 

To have access to the latest SDK and provide feedback, you have to use the [Feedback portal](https://feedback.autodesk.com/key/DataExchangeConnector). 
Once signed in you will have access to the SDK in form of an archive of Nuget packages.

[Next Step - Connecting to Data Exchange](../../connection/home/){: .btn}
