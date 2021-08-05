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
    // put this one as last route
    endpoints.MapFallbackToController("Index", "Fallback");
});
```
