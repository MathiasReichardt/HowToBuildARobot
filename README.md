# Introduction

REST is a difficult topic. A lot of different opinions and misunderstandings. So I will add my own opinionated view and try to explain my reasons on the way.
In this tutorial I will build a nice little REST API step by step.

Our domain will be a robot factory. It has several blueprints for robots and is capable of producing them also. But let's not spoil the surprise.

## Architectural choices

### HATEOAS, not a choice

REST demands that a RESTful API is navigatable. This means that basically you only know one URL and that's the entry to the API. Ever other URL is under the control of the server and will be provided. So no URL building by the client.
See Roy Fielding's comment on this:
[REST APIs must be hypertext-driven](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)

### Actions, beyond CRUD

An API based on CRUD operations (create, read, update, delete ) provide a interface to data. Most of the time they are an interface to a database over HTTP. This is fine for several use cases.

This tutorial will aim for a different kind of API where we access the domain logic through REST. This enables us to use the API with different clients without duplicating domain logic code.

### Siren as hypermedia format, because Actions

I will use the [Siren](https://github.com/kevinswiber/siren) hypermedia format. It supports Actions which are very useful to access available domain operations. Also Siren is quite clear and straight forward.

### ProblemJson, there will be errors

It is very useful to have a fixed format for error reporting so clients will know what objects to expect if something goes wrong. See [RFC7807 - Problem Details for HTTP APIs](https://tools.ietf.org/html/rfc7807)

### Clean Architecture and DDD

The server application will be build following principles of Clean Architecture and Domain Driven Design. Both are very useful ideas and worth a lot of reading on their own.

Great reads:

- [The Clean Code Blog by Robert C. Martin](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Clean Architecture: Standing on the shoulders of giants](https://herbertograca.com/2017/09/28/clean-architecture-standing-on-the-shoulders-of-giants/)
- [DDD, Hexagonal, Onion, Clean, CQRS, … How I put it all together](https://herbertograca.com/2017/11/16/explicit-architecture-01-ddd-hexagonal-onion-clean-cqrs-how-i-put-it-all-together/)

## Technical choices

- C# ASP.NET Core for the server implementation.
- [Web API Hypermedia Extensions](https://github.com/bluehands/WebApiHypermediaExtensions) I am the maintainer of this project and it will do a lot of work for me. Basically it is a Siren formatter which also builds links so I don't have to.
- Postman: super useful tool to talk to a HTTP API
- [HypermediaUi](https://github.com/MathiasReichardt/HypermediaUi) I am also the maintainer of this project. It is a generic Siren browser and convenient during development.
- HTTP as the underling protocol, REST does not exclude other protocols at all.

## Building a robot factory

During the tutorial we will build a ASP.NET Core application called RoboPlant. You can find the sources here: [RoboPlant](https://github.com/MathiasReichardt/RoboPlant)

- [Part 0: Setup](part0/part0.md)
- [Part 1: Enter the RoboPlant](part1/part1.md)
- [Part 2: Linking to another resource](part2/part2.md)
- [Part 3: Communicating Errors](part3/part3.md)
