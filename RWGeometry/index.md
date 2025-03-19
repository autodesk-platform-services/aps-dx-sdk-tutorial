---
layout: page
title: Creating exchanges
nav_order: 5
has_children: false
permalink: /create_exchange/home/
---

# Creating exchanges from the scratch






Create exchanges using the following steps:

1.  Create an empty data model:

    ```cs
    ElementDataModel model = ElementDataModel.Create(client);
    ```

    In previous section, when reading an existing exchange, we used the same call, except in this case we don't provide any exchange data, indicating that an empty model should be created:

    ![empty_model](../../assets/images/empty_model.png)

    Note that in `ExchangeData` there just one node and that is the `Root` node.


2.  Add (at least) an element to the `dataModel`:.

      ```cs
    var my_wall = model.AddElement(new ElementProperties { 
                                            ElementId = "SomeID_Here", 
                                            Name = "My_Wall", 
                                            Category = "Walls" });
      ```
    Note that we added only the minimum necessary properties, but we can set also things like `Family`, `Type` and so one, otherwise they will be assigned default values:

    ![my_wall](../../assets/images/my_wall.png)

3. Add geometry to the element. This might look complicated, yet once mastered, it will be streightforward. 

    ```cs
    // specify how the geometry should look like: what color and how transparent it should be
    var redRenderStyle = new RenderStyle("Red", new RGBA(255,0,10,1),0.5) ;
    // create the geometry by specifying the STEP file and the render style
    var wallGeometry = ElementDataModel.CreateGeometry(new GeometryProperties("../../my_wall.stp", redRenderStyle));
    // create a list that will hold all geometries referenced by the element
    var my_wall_geometries = new List<ElementGeometry>();
    // add the geometries to the list
    my_wall_geometries.Add(wallGeometry);
    // insert the list of geometries into the needed element
    model.SetElementGeometryByElement(my_wall, my_wall_geometries);
    ```
    
    Note that list of geometries are not added directly to the `myWall` element, but the ElementDataModel `model` is taking care of it because it builds a graph out of it and now we see in `EchangeData` a count of five instead of just one:

    ![non_empty_model](../../assets/images/non_empty_model.png)


4. Add some properties to the wall element. 

    There can be either `Type parameter` that will be common to all elements of the same `Type`, or there are `Instance parameter` that will be specific only to the element that it belongs to. In this case we will add a build-in instance paramater and we'll cover the custom type parameters later:

    ```cs
    // add properties
    var my_wall_params = (StringParameterDefinition)ParameterDefinition.Create(Autodesk.Parameters.Parameter.DatumText, ParameterDataType.String);
    my_wall_params.Value = "Some wall description";
    my_wall.CreateInstanceParameterAsync(my_wall_params).Wait();
    ```

    and right away we notice that our element now contains an instance parameter:

    ![instance_parameter](../../assets/images/instance_parameter.png)

    At this point our Model is populated with nodes and relationships, but exists only in memory of our computer and it needs a host. We create an exchange an exchange container where we specify where it will be hosted, where it will be placed, how it will be named and so on. 
    
    At least for now, the only hosting option available is ACC, but in future even non-Autodesk ones will be possible:

    ```cs
    // Create exchange container
    var ExchangeCreateRequest = new ExchangeCreateRequestACC()
    {
        Host = client.SDKOptions.HostingProvider,
        Contract = new Autodesk.DataExchange.ContractProvider.ContractProvider(),
        Description = "just testing exchange creation",
        FileName = "my_custom_exchange",
        HubId = hubId,
        ACCFolderURN = exchangeDetails.FolderUrn,
        ProjectId = exchangeDetails.ProjectUrn,
    };

    ```

    Note that for ACCFolder





var myTestExchangeDetails = client.CreateExchangeAsync(ExchangeCreateRequest).Result;

var exchangeIdentifier = new DataExchangeIdentifier()
{
    ExchangeId = myTestExchangeDetails.ExchangeID,
    CollectionId = myTestExchangeDetails.CollectionID,
    HubId = hubId,
};

client.SyncExchangeDataAsync(exchangeIdentifier, model.ExchangeData).Wait();














The SDK simplifies creating or reading Geometry and Parameters.


The creation of the exchanges using Data Exchange GraphQL API can be made through a mutation call that is generic enough for all formats but takes into account some particularities. A very good example is creation of an exchange from Revit file compared to creating it from AEC Data Model. A Revit model will contain views and when creating an exchange you have to specify the View from which you would like to create the exchange. In case of AEC Data Model, you can specify the Categories, Family and Type of components that should be included into the exchange. 

## Reading Geometry


![download_all_geometry_as_step](../../assets/images/download_all_geometry_as_step.png)

![download_wall_geometry](../../assets/images/download_wall_geometry.png)



[Next Step - Exploring Advanced Topics](../../advanced/home/){: .btn}
