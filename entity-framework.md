## Packages
* Microsoft.EntityFrameworkCore
* Microsoft.EntityFrameworkCore.Sqlite (db provider for dev)
* Microsoft.EntityFrameworkCore.Design

## dotnet-ef
```console
dotnet tool install --global dotnet-ef --version 5.0.8
```

## Migrations
```console
dotnet ef migrations add InitialCreate -o Data/Migrations
dotnet ef database update
```

* Delete latest migration
```console
dotnet ef migrations remove
```

## Fully defined relationships
* Contraints on deletion: `CASCADE`
* Not allow foreign key to be null

```csharp
[Table("Photos")]
public class Photo
{
    // ...
    public AppUser user { get; set; }
    public int AppUserId { get; set; }
}
```

## Repository Pattern
- Put a layer of abtraction over ORM lib (persistence layer)

## Context
* Adding a new entity to the context
```csharp
var blog = new Blog { Name = "ADO.NET Blog" };
context.Blogs.Add(blog); // context.Entry(blog).State = EntityState.Added;
context.SaveChanges();
```

## Eager Loading
```csharp
// Data/UserRepository.cs
public async Task<IEnumerable<AppUser>> GetAllUsersAsync()
{
    return await _context.Users
        .Include(u => u.Photos)
        .ToListAsync();
}
```
* ⚠️ `A possible object cycle was detected`
```csharp
public class AppUser
{
    public int Id { get; set; }
    public string Username { get; set; }
    public ICollection<Photo> Photos { get; set; } // load associated photos
}

public class Photo
{
    public int Id { get; set; }
    public string Url { get; set; }
    public AppUser user { get; set; } // load associated user
    public int AppUserId { get; set; }
}
```
* ✔️ DTO comes to rescue

## Optimizations
#### :one: AsNoTracking()
https://blog.staticvoid.co.nz/2012/entity_framework_and_asnotracking/
- Use this tuning option on all queries for entities you don’t want to save back to the database
