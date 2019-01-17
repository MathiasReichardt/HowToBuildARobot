|[HOME](../README.md)|[Part 1](../part1/part1.md)|

# Part 2: Linking to another resource

## How do I document my API
If the client only knows the entry point URL, how do we document the API? We need an alternative way. Commonly a documentation is a list of URLs with additional information like example JSONs, what HTTP method to use and maybe even when you are allowed to call that route. Doing so has several drawbacks:

- The clients are hardcoding the URLs and methods, so if the API needs to change it there will be a breaking change: all clients must determine which URLs or methods have changed and adapt.

- The clients need to figure out if it is allowed to use a certain URL (e.g. get a specific resource or create a new one). This is business logic and may also change over time. So this logic is part of the API. and if the API is not providing a specific link you are not allowed to use it (the API should respond with a error code 401 or 403).

### API map 
One way to resolve this issue is using API maps to communicate the topology of an API. basically this is a graph where the entry point is the initial state. A client the moves along the edges, which represent the relations. So if a specific resource is to be accessed the client must choose a path to walk.