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

## Error Handling (in `Application` layer)
* Package: `FluentValidation.AspNetCore`
#### Add `CommandValidator` to validate `Activity` coming from `Command`
```csharp
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
