[HOME](README.md)
# Part 1: Enter the RoboPlant
To begin using an API we kneed to know where to find it. For that the API provides the **entry point URL**. When using the API we will start by requesting the entry point document and follow provided links to other areas of the API from there. If you can not navigate to some specific area it is not reachable for clients.

| Rule: Know only one URL |
|------|
| The only URL your API clients need to know is the entry point URL. |

## Adding the entry point controller
First we add a new controller which will be responsible to deliver the entry point document:

`EntryPoint.cs`:
```csharp
using Microsoft.AspNetCore.Mvc;
using WebApi.HypermediaExtensions.WebApi.AttributedRoutes;

namespace RoboPlant.Server.REST.EntryPoint
{
    [Route("api/[controller]")]
    [ApiController]
    public class EntryPoint : Controller
    {
        [HttpGetHypermediaObject(typeof(EntryPointHto))]
        public ActionResult GetEntryPoint()
        {
            return Ok(new EntryPointHto());
        }
    }
}
```
So when calling `http://<host>/api/entrypoint` the controller will deliver a object called `EntryPointHto`. 

### The HttpGetHypermediaObject attribute
The `HttpGetHypermediaObject` is derived from `HttpGet` and additionally marks the route, that it is the route which is responsible for `EntryPointHto` resources. This is required by the framework so links to other resources can be generated. It is still possible to return other objects, like ProblemJson as we will see later, but this is not relevant for constructing links.

## Create the entry point HTO
To describe the entry point resource we will create a new class called: `EntryPointHto`. 

`EntryPointHto.cs`:
```csharp
using WebApi.HypermediaExtensions.Hypermedia;
using WebApi.HypermediaExtensions.Hypermedia.Attributes;

namespace RoboPlant.Server.REST.EntryPoint
{
    [HypermediaObject(Title = "Entry to the RoboPlant REST API", Classes = new[] { "EntryPoint" })]
    public class EntryPointHto : HypermediaObject
    {
    }
}
```
As a side note: I like to put the HTO classes next to the controllers because it is grouping by context (and not by type).

### What is a HTO
HTO is short for *Hypermedia Transfer Object* which is a play on the acronym name DTO *Data Transfer Object*. It contains all the data that is required (by the formatter) to build a valid hypermedia response.

## GET `/api/entrypoint`
Calling our brand new API will return the EntryPoint document formatted as Siren. The formatting is done by the WebApi.HypermediaExtensions library:

```json
{
    "class": [
        "EntryPoint"
    ],
    "title": "Entry to the RoboPlant REST API",
    "properties": {},
    "entities": [],
    "actions": [],
    "links": [
        {
            "rel": [
                "self"
            ],
            "href": "http://localhost:5000/api/entrypoint"
        }
    ]
}
```

We notice that the formatter for `HypermediaObject` was able to create a proper Siren, complete with `class` and `title`. We also see the link generation feature in action. There is a link called `self` which points to our entry point. It is best practice for all Siren documents to contain a `self` link, so e.g. you are able to refresh a resource.

## Conclusion
We completed our entry point which we will extend in the following parts.

[Part 2: Linking to another resource](part2/part2.md)