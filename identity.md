## Features, Pros?
- Secured, tested by Microsoft
- Roles management
- Policy-based authorization

## Policy-based authorization
- What
  - 1 User can have many Roles
  - 1 Role belongs to many Users

- Model in code
```csharp
public class AppUser : IdentityUser<int>
{
    // ...
    public ICollection<AppUserRole> UserRoles { get; set; }
}

public class AppRole : IdentityRole<int>
{
    public ICollection<AppUserRole> UserRoles { get; set; }
}

public class AppUserRole : IdentityUserRole<int>
{
    public AppUser User { get; set; }
    public AppRole Role { get; set; }
}
```
