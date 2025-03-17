---
layout: page
title: About
permalink: /about/
nav_order: 7
---

# About this tutorial

The purpose of this tutorial is to have a detailed overview of the key features of the Data Exchange .NET SDK. It’s OK if you are not familiar with the overall Autodesk Platform Services APIs as the Data Models are bringing a new approach to accessing granular data. But to be familiar with other aspects, especially when you are ready to create a connector, please make sure you are familiar with APS, as many of APS services have complementary features. If you are new to the platform, you can start at the [APS Tutorials page](https://tutorials.autodesk.io).

The Data Model (be it AEC, MFG or M&E) is a new approach for getting granular data. If you are already familiar with Model Derivative data from a Revit file, this data will look very familiar to you. The Data Model approach to accessing data brings a “real-time” aspect to the data, and also direct access to the data you need. Model Derivative is basically a file conversion, and as a result, all the data is produced at once, putting the burden of accessing it granularly on you – the developer. The Data Model approach, instead, will allow you to access specific types of data directly, bringing the promise of granular data to life. Model Derivative data is a “snapshot” of data at a given time or version, while Data Model in general and an exchange in particular, once initially published, will stay synchronized by the Revit design changes, and also includes design versioning.

In this tutorial, we'll explore the features of Data Exchange .NET SDK (currently in public beta) in context of exchanges crated in ACC.
We'll walk you through the prerequisites, initial workflows, advanced features and some interesting cases that can be covered using this SDK.

If you're interested in any of the following cases, then this tutorial is suitable to you:

1. Access the data from exchanges created from subset of Revit designs 
2. Access the geometry from exchanges created from subset of Revit designs 
3. Create exchanges from the scratch and add custom data and geometry to it 

\* Limited to Revit 2024 files hosted on ACC allow-listed hubs
