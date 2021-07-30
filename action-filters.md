## What
* before and after the action method execution
```csharp
{
    public class AsyncActionFilterExample : IAsyncActionFilter
    {
        public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
        {
            // execute any code before the action executes
            var result = await next();
            // execute any code after the action executes
        }
    }
}
```

## 3 types of scopes:
* :one: Global
* :two: Action
* :three: Controller

## Order of invocation
![image](https://user-images.githubusercontent.com/28957748/127641618-055f73f1-749d-40d0-8c45-caa8ef9a093a.png)

## Use Case 1: Update LastActive field every time user send req
#### Implement the filter
```csharp
public class LogUserActivity : IAsyncActionFilter
{
    private readonly IUserRepository _userRepo;

    public LogUserActivity(IUserRepository userRepo)
    {
        _userRepo = userRepo;
    }

    public async Task OnActionExecutionAsync(ActionExecutingContext context, ActionExecutionDelegate next)
    {
        var resultContext = await next();

        if (!resultContext.HttpContext.User.Identity.IsAuthenticated) return;

        var userId = resultContext.HttpContext.User.GetUserId();

        var user = await _userRepo.GetUserByIdAsync(userId);

        user.LastActive = DateTime.Now;

        await _userRepo.SaveAllAsync();
    }
}
```
#### Register as a service in `ApplicationServiceExtensions`
```csharp
services.AddScoped<LogUserActivity>();
```

#### Use it for all controllers
```csharp
namespace API.Controllers
{
    [ServiceFilter(typeof(LogUserActivity))]
    [ApiController]
    [Route("api/[controller]")]
    public class BaseApiController : ControllerBase
    {

    }
}
```
