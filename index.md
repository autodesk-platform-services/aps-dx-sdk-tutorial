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

By the end, we will have a [connector to Excell](https://github.com/autodesk-platform-services/BootstrapDXConnector), that will allow reading, updating, writing and deleting properties from an exchange:

![final result](./assets/images/result.gif)

We can start with a [BootstrapConnector](https://github.com/autodesk-platform-services/BootstrapDXConnector/tree/start) that contains the minimum code required to check if all the settings are valid for a connection to Data Exchange.


This tutorial is divided into 5 main sections:

1. [Prerequisites and SDK Access](./prerequisites/home/)

2. [Connecting to Data Exchange](./connection/home/)

3. [Create, Read, Update and Delete Data](./crud_data/home/)

4. [Read and Write Geometry](./wr_geometry/home/)

5. [Exploring Advanced Topics](./advanced/home/)

We'll start by ensuring that everyone addresses the prerequisites.

[Next Step - Prerequisites and SDK Access](./prerequisites/home/){: .btn}
