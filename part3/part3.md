[HOME](../README.md) [Part 2](../part2/part2.md)

# Part 3: Communicating Errors

Things will go wrong, some expected some unexpected. We should do our best to foresee error cases and make them first class citizens in our application. 

## Error classes

Lets find some coarse schema for errors:

### Programming errors and unexpected cases

In here we find null references, stack overflows and so on. These errors will not be handled because, if we would they would not be unexpected. These are catched in a very basic manner so our application does not shut down if possible. Also these kind is difficult to recover because again: we did not anticipate them.

### Environment errors

These are things we know of but are mostly out of our control.  E.g. Network availability, other services are down, disk space, entity version collisions and out dated data on the client.

### Business logic violations

Some conditions we enforce for our business logic might be not fulfilled and we therefor refuse to execute an action or refuse to create a business object. These changes may originate from invalid changes to persisted data (bugs, also think of migrations), user input.

## Ways to treat errors

For programming errors we must fish in the dark. RoboPlant features a `GlobalExceptionFilter` which will produce a decent error response if an unhandled exception occurs.

The expected cases of environmental errors and business logic violations tempt us in using (custom) exception. But if errors are first class citizens we should not resort to control flow by exception.

Reasons:

- Bad code readability
- Unclear exception cases: what exceptions must be handled?
- Abstractions: IRepository does not state anything about an implementation throwing a implementation specific exception (e.g. SqlException)
- Bad extensibility: If using custom exceptions you need to find all places where this exception might be seen.

### Result types

One way to tackle this problem is using Result types. A concept borrowed from functional languages like F#. The core idea is that a function returns a result object derived from a common base type. When processing the result every possible derived class needs to be handled.

Using some template function to access all derived types this ensures that if the result type is extended, because a new case emerged, all usages of the result will produce an compiler error.

## Accessing the Production HTO and handling errors

We previously accessed the production HTO in a very naïve manner. We assumed everything goes well. So we now extend our application for some error cases.

When building the Production HTO we call the `ProductionCommandHandler.GetAllProductionLines()` to retrieve the available `ProductionLine` representations. The command handler then uses a repository through an interface `IProductionLineRepository`. Previously the method just returned `Task<ICollection<ProductionLine>>`. We now extend the signature so we return a result object `Task<GetAllResult<ICollection<ProductionLine>>>`.

The `GetAllResult` is an abstract base class which is implemented by `GetAllResult.Success` `GetAllResult.NotReachable` `GetAllResult.Error`. Each of them has the option to have case dependent properties which are guaranteed  to be filled. E.g. `GetAllResult.Success` contains the result of type `ICollection<ProductionLine>` but `GetAllResult.Error` does not. This frees us from properties which are sometimes filled and sometimes not and we have to know and test when and if a property is available. Also from the name of the classes it is quite clear what happened and we can decide how to proceeded. Note that the result is on the same abstraction level as the interface `IProductionLineRepository`. There is no implementation dependent cases like an `SqlException` or `DbUpdateException`.

This is crucial so the implementation details do not leak into the layer using the interface, in the end I do not (want to) know what exceptions to expect and handle them. This is the responsibility of the implementation, it must handle all (error) cases and respond with a result type. On the other had the layer using the interface will not catch any exceptions. If an exceptions is not handled this is a bug in the interface implementation. This is a case of those unexpected errors. If we start adding try catches every where, "just in case", our code will degrade and we loose the benefits of result types.

### The GetAllResult

This is the result class for the GetAllResult.

```csharp
namespace RoboPlant.Application.Persistence.Results
{
    public abstract class GetAllResult<TResult>
    {
        public sealed class Success : GetAllResult<TResult>
        {
            public TResult Result { get; }

            public Success(TResult result)
            {
                Result = result;
            }
        }

        public sealed class NotReachable : GetAllResult<TResult>
        {
            public NotReachable()
            {
            }
        }

        public sealed class Error : GetAllResult<TResult>
        {
            public Exception Exception { get; }

            public Error(Exception exception)
            {
                Exception = exception;
            }
        }

    }
}
```

It has a generic parameter `TResult` so it can be reused in our future repositories. When accessing our implementation `ProductionLineRepository.GetAll` we now return instances of `GetAllResult` results: `new GetAllResult<ICollection<ProductionLine>>.Success(resultList)`

### Application layer

For the simple use case of accessing `Production` we just pass the result to the caller (the REST controller). Because the result class is located in the same layer as the repository interface we do not introduce a code dependency to the repository implementation, which would violate the [Dependency Rule](http://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)].

Of course the application layer should create a new result class if there is a new case to be handled or it is required add another abstraction.

### Handling the result cases

Finally our REST controller receives a result base class `GetAllResult<ICollection<ProductionLine>>.Success(resultList)`.

#### The Match function for result class

To avoid casting in the REST controller and to get a compiler error when the result class is extended we add a `Match` function to the result class:

```csharp
public void Match(
            Action<Success> success,
            Action<NotReachable> notReachable,
            Action<Error> error)
            => this.TypeMatch(success, notReachable, error);
```

The match function expects an `Action` for all derived result class cases with a typed input. The `Actions` are then passed to `TypeMatch` which basically contains a type switch case statement and calls the proper delegate. There are more implementations for `Match` so we can sumerize:

- `void Match(...)`: Call `Action` for all cases.
- `TMatchResult Match<TMatchResult>(...)`: Call `Func` for all cases and return a common return type.
- `Task<TMatchResult> Match<TMatchResult>(...)`: Call a `Func` for all cases and return a common return a `Task` with return type.

#### Creating the response

We now can use the match function to create responses for all cases. If for some reason the result class changes we get a nice compiler warning. Also we can rely on the properties of the concrete result classes. E.g. only the `GetAllResult.Success` class contains a `Result` property and we do not have to know which properties we can use.

```csharp
[HttpGetHypermediaObject(typeof(ProductionHto))]
public async Task<ActionResult> GetProductionLines()
{
    var getAllResult = await CommandHandler.GetAllProductionLines();

    return getAllResult.Match(
        success => Ok(new ProductionHto(success.Result)),
        notReachable => this.Problem(ProblemFactory.ServiceUnavailable()),
        error => this.Problem(ProblemFactory.Exception(error.Exception)));
}
```




