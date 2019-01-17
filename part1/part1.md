# Part 1: Enter the RoboPlant
To begin using an API we kneed to know where to find it. For that the API provides the **entry point URL**. When using the API we will start by requesting the entry point document and follow provided links to other areas of the API from there. If you can not navigate to some specific area it is not reachable for clients.

| Rule: Know only one URL |
|------|
| The only URL your API clients need to know is the entry point URL. |

## Adding the entry point

## How do I document my API
This means we need an alternative way documenting our API. Commonly a documentation is a list of URLs with additional information like example JSONs, what HTTP method to use and maybe even when you are allowed to call that route. Doing so has several drawbacks:

- The clients are constructed hardcoding the URLs and methods, so if I need or want to change it there will be breaking changes. All clients must determine which URLs or methods have changed and adapt.

- The clients need to figure out if it is allowed to use a certain URL (e.g. get a specific resource or create a new one). This is business logic and may also change over time. So this logic is part of the API. So if the API is not providing a specific link you are not allowed to use it.

### API map 
One way to resolve this issue is using API maps to communicate the topology of an API.