## Checklist
- In `Startup.cs`, config serving static files (look for files in `wwwroot/`)
```csharp
app.UseAuthentication();
app.UseAuthorization();

// added code
app.UseDefaultFiles(); // Look for `index.html` in wwwroot/
app.UseStaticFiles(); // Serve static files in wwwroot/
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
- Deploy to Heroku
```console
heroku login
heroku git:remote -a datingapp-dotnet-angular
heroku buildpacks:set https://github.com/jincod/dotnetcore-buildpack#preview
```
- Add addon `heroku-postgres` 
- Set env vars in heroku:
```json
{
  "CloudinarySettings": {
    "CloudName": "vnscriptkid",
  }
}
```
```
CloudinarySettings:CloudName => vnscriptkid
```
```console
heroku config:set ASPNETCORE_ENVIRONMENT=Production
heroku config:set TokenKey=somethingsecret
```

- Push code to heroku
```console
git push heroku master
```

## ⭐ [HEROKU] Take postgres info from env vars
```csharp
services.AddDbContext<DataContext>(options =>
{
    var env = Environment.GetEnvironmentVariable("ASPNETCORE_ENVIRONMENT");

    string connStr;

    // Depending on if in development or production, use either Heroku-provided
    // connection string, or development connection string from env var.
    if (env == "Development")
    {
        // Use connection string from file.
        connStr = config.GetConnectionString("DefaultConnection");
    }
    else
    {
        // Use connection string provided at runtime by Heroku.
        var connUrl = Environment.GetEnvironmentVariable("DATABASE_URL");

        // Parse connection URL to connection string for Npgsql
        connUrl = connUrl.Replace("postgres://", string.Empty);
        var pgUserPass = connUrl.Split("@")[0];
        var pgHostPortDb = connUrl.Split("@")[1];
        var pgHostPort = pgHostPortDb.Split("/")[0];
        var pgDb = pgHostPortDb.Split("/")[1];
        var pgUser = pgUserPass.Split(":")[0];
        var pgPass = pgUserPass.Split(":")[1];
        var pgHost = pgHostPort.Split(":")[0];
        var pgPort = pgHostPort.Split(":")[1];

        connStr = $"Server={pgHost};Port={pgPort};User Id={pgUser};Password={pgPass};Database={pgDb}; SSL Mode=Require; Trust Server Certificate=true";
    }

    // Whether the connection string came from the local development configuration file
    // or from the environment variable from Heroku, use it to set up your DbContext.
    options.UseNpgsql(connStr);
});
```
