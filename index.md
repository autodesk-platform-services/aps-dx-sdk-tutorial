---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: home
title: Home
permalink: /
nav_order: 1
---
![](./assets/images/sdk_logo.png)

# Data Exchange .Net SDK Tutorial

## Introduction

The Autodesk Data Exchange SDK (.NET) is a client-side component designed to build quick integrations into Data Exchange services, simply called connector.
A connector enables end-users to easily create and consume exchanges that are specific to their applications.

In this tutorial, we'll cover the first steps in your journey with the Data Exchange .NET SDK in a quick and simple way, by starting building a simple connector and incremently adding features to it. 

By the end of this tutorial, we will have a [connector to Excell](https://github.com/autodesk-platform-services/BootstrapDXConnector), that will:

- take an exchange created from a Revit model, 
- identify the Walls elements, 
- read the properties and find the Area property for each wall element, 
- add these values to an excel file, designed to calculate the total area of the walls.

A very simple workflow that hypothetically can be helpful for situations like estimating the cost of a painting or finishing job:

![final result](./assets/images/result.gif)

We can start with a [BootstrapConnector](https://github.com/autodesk-platform-services/BootstrapDXConnector/tree/start) that contains the minimum code required to check if all the settings are valid for a connection to Data Exchange.


This tutorial is divided into 5 main sections:

1. [Prerequisites and SDK Access](./prerequisites/home/)

2. [Connecting to Data Exchange](./connection/home/)

3. [Access Data and Geometry](./crud_data/home/)

4. [Creating exchanges](./create_exchange/home/)

5. [Exploring Advanced Topics](./advanced/home/)

We'll start by ensuring that everyone addresses the prerequisites.

[Next Step - Prerequisites and SDK Access](./prerequisites/home/){: .btn}
