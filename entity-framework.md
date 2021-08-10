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

* Drop database
```console
dotnet ef database drop
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


## ManyToMany relationship (Self-Referencing)
#### User Stories 😴
* User can like many other users
* User can be liked by many other users

#### Code
* Craete new `Like` entity that represents `Likes` table
```csharp
public class UserLike
{
    public AppUser SourceUser { get; set; }
    public int SourceUserId { get; set; }
    public AppUser LikedUser { get; set; }
    public int LikedUserId { get; set; }
}
```

* Define API in `AppUser` entity to query likes
```csharp
public class AppUser
{
    // ...
    public ICollection<UserLike> LikedByUsers { get; set; }
    public ICollection<UserLike> LikedUsers { get; set; }
}
```

* Build the relationship between 2 entities
```csharp
// This method is called by the framework when your context is first created to build the model and its mappings in memory
public class DataContext : DbContext
    {
        public DataContext(DbContextOptions options) : base(options) { }

        public DbSet<UserLike> Likes { get; set; }

        protected override void OnModelCreating(ModelBuilder builder)
        {
            base.OnModelCreating(builder);

            builder.Entity<UserLike>()
                .HasKey(k => new { k.SourceUserId, k.LikedUserId });

            builder.Entity<UserLike>()
                .HasOne(s => s.SourceUser)
                .WithMany(l => l.LikedUsers)
                .HasForeignKey(s => s.SourceUserId)
                .OnDelete(DeleteBehavior.Cascade);

            builder.Entity<UserLike>()
                .HasOne(s => s.LikedUser)
                .WithMany(l => l.LikedByUsers)
                .HasForeignKey(s => s.LikedUserId)
                .OnDelete(DeleteBehavior.Cascade);
        }
    }
```

## ManyToMany (by convention)
- User stories
    - 1 User joins many Activities
    - 1 Activity has many Users
```csharp
public class AppUser : IdentityUser
{
    // ...
    public ICollection<Activity> Activities { get; set; }
}

public class Activity
{
    // ...
    public ICollection<AppUser> Attendees { get; set; }
}
```
- Entity framwork creates a join table behind the scene:
```csharp
migrationBuilder.CreateTable(
    name: "ActivityAppUser",
    columns: table => new
    {
        ActivitiesId = table.Column<Guid>(type: "TEXT", nullable: false),
        AttendeesId = table.Column<string>(type: "TEXT", nullable: false)
    },
    // ...
```
```
