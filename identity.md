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
* Install : Microsoft.AspNetCore.Identity.EntityFrameworkCore
* In `DataContext`, plug these in:
```csharp
public class DataContext : IdentityDbContext<AppUser, AppRole, int,
    IdentityUserClaim<int>, AppUserRole, IdentityUserLogin<int>,
    IdentityRoleClaim<int>, IdentityUserToken<int>>
{
  protected override void OnModelCreating(ModelBuilder builder)
  {
      base.OnModelCreating(builder);

      // Each User can have many entries in the UserRole join table
      builder.Entity<AppUser>()
          .HasMany(e => e.UserRoles)
          .WithOne(e => e.User)
          .HasForeignKey(ur => ur.UserId)
          .IsRequired();

      // Each Role can have many entries in the UserRole join table
      builder.Entity<AppRole>()
          .HasMany(e => e.UserRoles)
          .WithOne(e => e.Role)
          .HasForeignKey(ur => ur.RoleId)
          .IsRequired();

      // ...
  }
}
```
