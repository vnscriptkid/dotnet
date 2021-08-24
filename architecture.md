## Clean architecture
<img height="300px" src="https://user-images.githubusercontent.com/28957748/130484900-6ea34154-6d61-4d00-bc3b-a89bf04130df.png"/>

```js
// folder structure
API // Controllers
Application // Use cases
Domain // Entities
Persistence // DbContext (EntityFramework)
```

## Mediator Pattern
* Package: `MediatR.Extensions.Microsoft.DependencyInjection`

<img height="300px" src="https://user-images.githubusercontent.com/28957748/130484692-356d71ac-6068-4296-a30e-c87fc364d439.png"/>

## CQRS Pattern

## Validation Errors Handling (in `Application` layer)
* Package: `FluentValidation.AspNetCore`
#### :one: Add `CommandValidator` to validate `Activity` coming from `Command`
```csharp
// Application/Activities/Create.cs
public class Command : IRequest
{
    public Activity Activity { get; set; }
}

public class CommandValidator : AbstractValidator<Activity>
{
    public CommandValidator()
    {
        RuleFor(x => x.Title).NotEmpty();
    }
}

public class Handler : IRequestHandler<Command> {}
```

#### :two: Register validations
```csharp
services.AddControllers()
.AddFluentValidation(config =>
{
    config.RegisterValidatorsFromAssemblyContaining<Create>();
});
```

## Handle errors that we're aware of (In `Application` layer)
- Problem: `API` layer should know nothing about `Application` layer (see dependancy photo)
- Solution: 
    - Handle errors in `Application` layer
    - Abstract out result (success of failure) into `Result` object
    - API gets `Result` object back from `Application`, it now looks at a contract with `Application` instead of internal details. 
```csharp
public async Task<Result<Unit>> Handle(Command request, CancellationToken cancellationToken)
{
    var activity = await _context.Activities.FindAsync(request.Id);

    if (activity == null) return null;

    _context.Remove(activity);

    var success = await _context.SaveChangesAsync() > 0;

    if (success) return Result<Unit>.Success(Unit.Value);

    return Result<Unit>.Failure("Failed to delete activity");
}
```

```csharp
// Application/Core/Result.cs
public class Result<T>
{
    public bool IsSuccess { get; set; }
    public T Value { get; set; }
    public string Error { get; set; }
    public static Result<T> Success(T value) => new Result<T> { IsSuccess = true, Value = value };
    public static Result<T> Failure(string error) => new Result<T> { IsSuccess = false, Error = error };
}
```

```csharp
// API/Controller/BaseApiController.cs
protected ActionResult HandleResult<T>(Result<T> result)
{
    if (result == null) return NotFound();
    if (result.IsSuccess && result.Value != null)
        return Ok(result.Value);
    if (result.IsSuccess && result.Value == null)
        return NotFound();

    return BadRequest(result.Error);
}
```
