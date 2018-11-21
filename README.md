# How to build a Robot - a REST tutorial

REST is a difficult topic. A lot of different opinions and misunderstandings. So I will add my own opinionated view and try to explain my reasons on the way. 
So in this tutorial I will build a nice little REST API step by step.

## Architectural choices 

### HATEOAS, not a choice

### This will not be a **CRUD** API.

### Siren as hypermedia format, because Actions
I will use the (Siren)[https://github.com/kevinswiber/siren] hypermedia format. It supports Actions which are very useful.

## Technical choices
- C#
- Asp.Net core
- (Web API Hypermedia Extensions)[https://github.com/bluehands/WebApiHypermediaExtensions]