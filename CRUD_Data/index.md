---
layout: page
title: Create, Read, Update and Delete Data
nav_order: 4
has_children: false
permalink: /crud_data/home/
---

## Understanding the underlaying Data Model

![data_model_overview](../../assets/images/data_model_overview.png)

The AEC, Manufacture, and M&E Data Models aim to replace file-driven designs due to issues like versioning and sharing. Instead, in each Data Model, a design is a graph that updates with changes, tracks older states, and allows granular access.

![data_model](../../assets/images/data_model.gif)

The Data Exchange service, as sharing and communication facilitator, is using the same approach and an exchange is a graph based structure, respresented in the SDK by **ExchangeDataModel**. 

The SDK ensures that changes to the ElementDataModel are translated to corresponding delta operations for modifying the data exchange and updates the ElementDataModel with incoming changes made to an exchange. 

Same time, it handles all the internal API operations such as pagination, schema lookups and other resolutions to simplify access to data refrenced by the exchange.

![exchange_data_model](../../assets/images/exchange_data_model.png)

We will not work directly with an **ExchangeDataModel**. The SDK provides a convenient client-side data model [ElementDataModel](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/ElementDataModel/), which abstracts the underlying client-side “ExchangeDataModel”.

An [Element](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/Element/) is the unit of the ElementDataModel, representing an instance or an entity within an exchange. It includes components holding geometry, parameter, and other information. Creating a new Element of the same type involves referencing existing components with modified properties.

![element_data_model](../../assets/images/element_data_model.gif)

An Element refers to [Parameters](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/Parameter/), which can be either a list of built-in supported parameters or custom parameters. These parameters are well-defined, language-neutral data types stored in a Schema repository. 

![prameters](../../assets/images/parameters.png)

An element can also reference Geometry, but we will cover that in next chapter and for now we will concentrate on Parameters.

## Understand the SDK

The SDK simplifies creating or reading Parameters and as described above, to be able to work with an exchange, the client-side model has to be created for the given echange:

```cs
public static ElementDataModel Create(IClient client, ExchangeData exchangeData = null)
```

In this case we don't provide the `echangeData` value, an exchange from scratch will be created. We will leave this for the [Advanced Topics](./advanced/home/) and now concentrate on reading data from an existing exchange.

The [ExchangeData](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.Models/ExchangeData/) class is representation of an exchange from Asset perspective, and at this stage we will use it just for creation of the ElementDataModel, which can be achieved by using the `client` we created in previous chapter:

```cs
public Task<ExchangeData> GetExchangeDataAsync(DataExchangeIdentifier exchangeIdentifier)
```

this retrieves Exchange Data for the specified `exchangeIdentifier`, providing comprehensive data up to the most recent revision.
In [Advanced Topics](./advanced/home/) we will look at the same method, but with signatures that allows specifying revision and create ElementDataModel for data contained between specified exchange versions.

For now, we are interested in getting the most recent version and for that we have to provide a [DataExchangeIdentifier](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.Core.Models/DataExchangeIdentifier/) instance that just holds information on `HubID`, `CollectionID` and `ExchangeID`, as the name suggests, needed to identify an exchange. 

Depending on situation, these values can be already available, but if not, the `urn` of the exchange that we get through:  

- **Data Management API** when looking at folder content using a REST call:

  ![item_dm](../../assets/images/item_dm.png)

- **Data Exchange GraphQL API** when looking at the folder content through Data Exchange Model Explorer:

  ![item_dx](../../assets/images/item_dx.png)

- even through **ACC** when looking at the Viewable of an exchange:

![item_acc](../../assets/images/item_acc.png)

no matter how we get the `exchange urn`, this is enough to further use it and retrieve all the information needed for `exchangeIdentifier`.

Having the `exchange urn` we will make use again of "allmighty" `client` that we created in the previous chapter, using the following method:

```cs
public Task<ExchangeDetails> GetExchangeDetailsAsync(string exchangeUrn)
```

that will extract for us the needed information.

## SDK in Action

Armed with all this knowledge, we can proceed with the code using the following steps:

1.  Having the `exchange urn` get the exchange details:

      ```cs
      var exchangeDetails = client.GetExchangeDetailsAsync("urn:adsk.wipprod:dm.lineage:fYwPXS8cTYKQF2WUd8J0Ig").Result;
      ```

      The `exchangeDetails` will contain a lot of interesting information, but we are interested only in `HubID`, `CollectionID` and `ExchangeID`:

      ![exchange_details](../../assets/images/exchange_details.png)

      Note that the `HubId` is ***null*** here and this is a known issue and the temporary workaround is pretty simple. Since in `exchangeDetails` we have `ProjectUrn`, we can use again the "allmighty" `client` to get the `hubId`:

      ```cs
      var hubId = client.GetHubIdAsync(exchangeDetails.ProjectUrn).Result;
      ```

2.  Having `HubID`, `CollectionID` and `ExchangeID`, create an `exchangeIdentifier`:

      ```cs
      var exchangeIdentifier = new DataExchangeIdentifier()
      {
          ExchangeId = exchangeDetails.ExchangeID,
          CollectionId = exchangeDetails.CollectionID,
          HubId = hubId,
      };
  ```

3. Having an `exchangeIdentifier`, we can now use it to get the `exchangeData`:

    ```cs
    var exchangeData = client.GetExchangeDataAsync(exchangeIdentifier).Result;
    ```

    ![exchange_data](../../assets/images/exchange_data.png)

    The `ExchangeData` is the Asset Graph representation of an exchange - a graph structure containing nodes and relationships between nodes and can be used as is, by using methods to navigate the graph, but this could be daunting and if you are curious, you can use [one of our samples](https://fdx-graph-explorer.autodesk.io/) to see the graph with all the nodes and edges. 

    This is why we want use the `ElementDataModel`, that abstracts this graph and gives us the necessary tools to intuitively retrieve, update, create and even delete the needed data, while all the necessary graph changes will be done "under the hood".

4. Having an `exchangeData`, we finally can create an `ElementDataModel` from it:

    ```cs
    ElementDataModel dataModel = ElementDataModel.Create(client, exchangeData);
    ```

    and right away we notice that it contains data in a more intuitive form:

    ![data_model_debug](../../assets/images/data_model_debug.png)

### Reading Data

At this point it is matter of exploring the methods that [ElementDataModel](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/ElementDataModel/) exposes, depending on what we would like to achieve, or since we know now the structure and see where the Elements are, we can intuitively acces them directly and filter for the needed ones, using the Element specific field name and values:

```cs
IEnumerable<Element> wallElements = dataModel.Elements.Where(item => item.Category == "Walls");
```

In this case we selected all the Walls and we can look at what methods an [Element](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/Element/) exposes or just inspect the content of an Element instance and find where the needed information is stored: 

![element_content](../../assets/images/element_content.png)

once the needed information is identified, retrieving it is trivial:

```cs
foreach (Element wallElement in wallElements)
{
    Console.WriteLine($"-----------{ wallElement.Name }-------------");
    foreach (var parameter in wallElement.InstanceParameters)
    {
        Console.WriteLine($"name: {parameter.Name}, value: {parameter.Value}, type: {parameter.Description}");
    }

}
```

>At this stage, everything should be operating without errors. If there are any issues, please refer to the [BootstrapConnector](https://github.com/autodesk-platform-services/BootstrapDXConnector/tree/read_data) for reference on how it should look at this step.


Once everything is fine we can proceed further with Data Exchange .Net SDK exploration.

[Next Step - Read and Write Geometry](../../wr_geometry/home/){: .btn}
