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

One way to tackle this problem is using Result types. A concept borrowed from functional languages like F#.
