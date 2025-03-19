---
layout: page
title: Access Data and Geometry
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

An element can also reference Geometry and multiple Elements can be referenced within the ElementGeometry model:

![element_geometry](../../assets/images/element_geometry.png)

Let us keep in mind the theoretical aspect discussed above and continue with practical side, where all of these concepts will be very useful in understanding the SDK. 

## Understand the SDK

The SDK simplifies creating or reading Geometry and Parameters. To be able to work with an exchange, the client-side model has to be created for the given echange:

```cs
public static ElementDataModel Create(IClient client, ExchangeData exchangeData = null)
```

If we don't provide the `echangeData` value, an exchange from scratch will be created. We will leave this for the next chapter where we will cover the [Creating exchanges](./wr_geometry/home/) topic, while now we will concentrate on reading data from an existing exchange.

The [ExchangeData](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.Models/ExchangeData/) class is representation of an exchange from Asset perspective, and at this stage we will use it just for creation of the ElementDataModel, which can be achieved by using the `client` we created in previous chapter:

```cs
public Task<ExchangeData> GetExchangeDataAsync(DataExchangeIdentifier exchangeIdentifier)
```

this retrieves Exchange Data for the specified `exchangeIdentifier`, providing comprehensive data up to the most recent revision.
In [Advanced Topics](./advanced/home/) we will look at the same method, but with signatures that allows specifying revision and create ElementDataModel for data contained between specified exchange versions.

For now, we are interested in getting the most recent version and for that we have to provide a [DataExchangeIdentifier](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.Core.Models/DataExchangeIdentifier/) instance that just holds information on `HubID`, `CollectionID` and `ExchangeID`, as the name suggests, needed to identify an exchange. 

Depending on situation, these values can be already available, but if not, the `urn` of the exchange we can get through:  

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

### Reading Geometry

We can download the geometry of the entire model and for that we can use the following methods of the `client` instance:

```cs
public string DownloadCompleteExchangeAsSTEP(DataExchangeIdentifier exchangeIdentifier, 
    string stepFilePath = null)
```

as the name indicates, to download it in STEP format, or

```cs
public string DownloadCompleteExchangeAsOBJ(string exchangeID, string collectionId, 
    string objFilePath = null)
```
to download it as OBJ file.

In both cases, the returned string will be the system path to our geometry and if we don't specify the `*FilePath`, it will be created for us.

By calling in our code:

```cs
var path = client.DownloadCompleteExchangeAsSTEP(exchangeIdentifier);
```
and the value of `path` will be something like C:\Users\me\AppData\Roaming\MyConnector\Workspace\8b5...7675de\Geometry\a0028e3...515517c\3411ce7e...af215a8d8.stp

which can be visualized in Viewer:

![entire_geometry](../../assets/images/download_all_geometry_as_step.png)

However, as we mentioned in our introduction to Data Exchange, it strives for granular access and this is valid not only for data, but also for geometry.

The [ElementDataModel](https://aps.autodesk.com/en/docs/dx-sdk-beta/v1/sdk-reference/autodesk-dataexchange/Autodesk.DataExchange.DataModels/ElementDataModel/) has a method that allows to download geometry of specific elements:

```cs
public Task<Dictionary<Element, IEnumerable<ElementGeometry>>> GetElementGeometriesByElementsAsync(
    IEnumerable<Element> elements, GeometryOutputOptions geometryOutputOptions = null)

```
In it's essence, you provide a list of elements and get back a collection of local paths. 
If you don't provide `GeometryOutputOptions` param, it will default to STEP format, other option being OBJ.

By now we should be familiar with filtering the elements by properties, so if we are interested in geometry of a specific element, we can filter for it and request it's geometry:

```cs
var geoms = dataModel.GetElementGeometriesByElementsAsync(wallElements.Where(item => item.Name == "139854")).Result;
```

and get back the `path` to where the geometry is located:

![wall_geom_details](../../assets/images/wall_geom_details.png)

and vizualize the results:

![download_wall_geometry](../../assets/images/download_wall_geometry.png)


Reading data and geometry from an exchange is great and already opens the way for interesting workflows. With what we mastered so far we already can create connectors that are based only on reading data and geometry. 

A very simple example of the workflow that hypothetically can be helpful for situations like estimating the cost of a painting or finishing job, can be created by having the following algorithm:

- take an exchange created from a Revit model for needed part of the building, 
- identify the Walls elements in the exchange, 
- read the properties and find the Area property for each wall element, 
- add these values to an excel file, designed to calculate the total area of the walls and includes the price of painting per sqm.

and here you go, you have a very simple connector to Excel that analyze data and this simple workflow is a subject of a sample that will be soon available.

This is nice, yet the main power of the SDK is not only accessing data and geometry from an existing exchange, but creation of exchanges from scratch with power of modifying it further and this is exactly what we will look into in the next chapter.

[Next Step - Creating exchanges](../../create_exchange/home/){: .btn}
