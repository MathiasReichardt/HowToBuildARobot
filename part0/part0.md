[HOME](README.md)
# Part 0: Setup

We will need some basic setup to get things going. It is not the topic of this tutorial to explain how to use ASP.NET Core. For that please refer to other sources.

Your find the project [RoboPlant](https://github.com/MathiasReichardt/RoboPlant)

## Remarks
So all the initial setup can be seen in `Startup.cs`:

- Ensure to use only lowercase URLs which is a best practice.
- Tweak the formatters:
    - remove all default formatters because we want only to return JSON for now
    - add a tweaked JSON formatter which will not serialize `NULL` and `default` values to reduce the size of the JSON. This is better to read for us humans and will save some bandwidth.
- generously allow CORS so we are able to use the generic `HypermediaUi` and basically allow other web clients to use the API.
- Add `HypermediaExtensions` for later use
- Add a basic `GlobalExceptionFilter` so we ensure that all exceptions are formatted nicely as `ProblemJson`
- Add a "404" response so if some route is accessed which does not exist we will provide a proper `ProblemJson`

[Part 1: Enter the RoboPlant](part1/part1.md)