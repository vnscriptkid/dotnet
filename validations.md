## Respond in same format for validation errors
* Old code
```csharp
if (await _userManager.Users.AnyAsync(u => u.UserName == registerDto.Username.ToLower()))
{
    return BadRequest("Username already exists");
}
```

* Refactor
```csharp
if (await _userManager.Users.AnyAsync(u => u.UserName == registerDto.Username.ToLower()))
{
    ModelState.AddModelError("username", "Username has been taken");
    return ValidationProblem();
}
```

## Use default validation handling
- Annotate Entity
```csharp
public class Activity
{
    [Required]
    public string Title { get; set; }
    // ...
}
```
- Let `ApiController` handle everything else
https://docs.microsoft.com/en-us/aspnet/core/web-api/?view=aspnetcore-5.0#automatic-http-400-responses
```csharp
[ApiController]
[Route("api/[controller]")]
public class BaseApiController : ControllerBase {}
```
