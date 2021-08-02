## Behind the scene
- WebSockets
- Server-Sent Events
- Long Polling

## Create new endpoints
```csharp
public class PresenceHub : Hub
{
    public override async Task OnConnectedAsync()
    {
        await Clients.Others.SendAsync("UserIsOnline", Context.User.GetUsername());
    }

    public override async Task OnDisconnectedAsync(Exception exception)
    {
        await Clients.Others.SendAsync("UserIsOffline", Context.User.GetUsername());

        await base.OnDisconnectedAsync(exception);
    }
}
```

## Authenticate requests
* Get Token from query param
```csharp
options.Events = new JwtBearerEvents
{
    OnMessageReceived = context =>
    {
        var accessToken = context.Request.Query["access_token"];

        var path = context.HttpContext.Request.Path;

        if (!string.IsNullOrEmpty(accessToken) && path.StartsWithSegments("/hubs"))
        {
            context.Token = accessToken;
        }

        return Task.CompletedTask;
    }
};
```

* Allow cors with credentials
```csharp
app.UseCors(
  x => x.AllowAnyHeader()
      .AllowAnyMethod()
      .AllowCredentials()
      .WithOrigins("https://localhost:4200"));
```      
