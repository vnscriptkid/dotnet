## Checklist
- In `Startup.cs`, config serving static files (look for files in `wwwroot/`)
```csharp
app.UseAuthentication();
app.UseAuthorization();

// added code
app.UseDefaultFiles();
app.UseStaticFiles();
// end

app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    endpoints.MapHub<PresenceHub>("hubs/presence");
    endpoints.MapHub<MessageHub>("hubs/message");
});
```

- Add fallback endpoint to api
```csharp
// Controllers/FallbackController.cs
public class FallbackController : Controller
{
    public ActionResult Index()
    {
        return PhysicalFile(
            Path.Combine(Directory.GetCurrentDirectory(), "wwwroot", "index.html"),
            "text/HTML"
        );
    }
}

// Startup.cs
app.UseEndpoints(endpoints =>
{
    endpoints.MapControllers();
    // put this one at last
    endpoints.MapFallbackToController("Index", "Fallback");
});
```

- Switch from sqlite to postgres
```console
docker run --name dev -e POSTGRES_USER=appuser -e POSTGRES_PASSWORD=Pa$$w0rd -p 5432:5432 -d postgres:latest
```

- Remove all Migration files in `Data\Migrations`
- Install `Npgsql.EntityFrameworkCore.PostgreSQL`

- Register `Npgsql`
```csharp
services.AddDbContext<DataContext>(options =>
{
    options.UseNpgsql(config.GetConnectionString("DefaultConnection"));
});
```
- Update connection string in `appsettings`
```json
"ConnectionStrings": {
    "DefaultConnection": "Server=localhost; Port=5433; User Id=appuser; Password=Pa$$w0rd; Database=datingapp"
  },
```
- Generate migrations file for postgres
```console
dotnet ef migrations add PostgresInitial -o Data\Migrations
```
