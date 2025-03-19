---
layout: page
title: Connecting to Data Exchange
nav_order: 3
has_children: false
permalink: /connection/home/
---

# Connecting to Data Exchange

In this section, we will start with the minimum amount of code necesary to start working with Data Exchange and once we have everything set, we will start exploring the SDK.  

## Client-side Data Model

The Data Exchange SDK is a client-side SDK for the Data Exchange service, written in .Net that provides:

- a rich client-side Data Model,

- automatic handling of read and write Deltas,

- local storage for optimizing data operations,

- UI component that integrates with the Autodesk Docs,

- extensibility features that allows developers to customize the SDK as per their requirements (feature in progress).


To be able to connect to Data Exchange service, any connecter needs an instance of **Client** class:

```cs
public Client(SDKOptions sdkOptions)
```

which in turn requires an instannce of **SDKOptions** class, that works as the configurator for the the **Client** instance.

The documentation provides [more details on SDKOptions Class](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange/SDKOptions/), but the minimum amount of code required to create a valid **SDKOptions** instance and initialize the instance of **Client** class with it is the following:

```cs

var options = new SDKOptionsDefaultSetup()
{
    HostApplicationName = "BootstrapConnector",
    ClientId = "<YOUR_CLIENT_ID_HERE>",
    ClientSecret = "YOUR_CLIENT_SECRET_HERE",
    CallBack = "http://localhost:8080/",
    ConnectorName = "BootstrapConnector",
    HostApplicationVersion = "0.3",
    ConnectorVersion = "0.2"

};

var client = new Autodesk.DataExchange.Client(options);

```

It allows for many other options like Authentication, Storage, Hosting, logger etc., but in this case the default ones will be used and with them, the SDK will take care of getting for you a [3-legged token](https://aps.autodesk.com/en/docs/oauth/v2/tutorials/get-3-legged-token/) automatically. 

However, it is required for the **CallBack** specified in **SDKOptions** to match one of the **Callback URL**s that you have specified in your APS App:

![callback](../../assets/images/callback.png)

The SDK will use the value from the **CallBack** to create and endpoint and facilitate the 3-legged authorization, otherwise you should expect the following error:

![callback_error](../../assets/images/callback_error.png)

In this case we used the default AuthProvider (obviously you can create your own one), it is part of the **Client** instance now and can be used right away, to make sure that everything works fine:

```cs
  var account = client.SDKOptions.AuthProvider.GetUserAccountAsync().Result;

  Console.WriteLine($"Connector ready, using the account: {account.FirstName}");

```

and get many other details of the person that authorized the App:

![account](../../assets/images/account.png)

>At this stage, everything should be set up and operating without errors. If there are any issues, please refer to the [BootstrapConnector](https://github.com/autodesk-platform-services/BootstrapDXConnector/tree/start) for reference on how it should look at this step.

Once everything is fine we can proceed further with Data Exchange .Net SDK exploration.

[Next Step - Access Data and Geometry](../../crud_data/home/){: .btn}
