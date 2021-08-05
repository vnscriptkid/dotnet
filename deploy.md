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
