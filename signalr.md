## Behind the scene
- WebSockets
- Server-Sent Events
- Long Polling

## Basics
- Socket endpoint
```csharp
[Authorize]
public class MessageHub : Hub {
    // ...
}
```
- Client registers on a socket endpoint
```csharp
this.hubConnection = new HubConnectionBuilder()
      .withUrl(this.hubUrl, { accessTokenFactory: () => user.token })
      .withAutomaticReconnect()
      .build()

    this.hubConnection
      .start()
      .catch(error => console.error(error));
```      
- `OnConnectedAsync` method of MessageHub is called
```csharp
public override async Task OnConnectedAsync()
{
    // Inside here: You can access to: connected user, connection id
    // Context.User
    // Context.ConnectionId
}
```

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
* Register service
```csharp
services.AddSignalR();
```
* Get Token from query param
```csharp
 services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        // ...
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

## HowTo: Send message to specific connections
```csharp
await _presenceHub.Clients.Clients(connections).SendAsync("NewMessageReceived",
                        new { username = sender.UserName, knownAs = sender.KnownAs });
```

## HowTo: Access one hub inside another hub using `IHubContext`
```csharp
public MessageHub(
    IHubContext<PresenceHub> presenceHub,
)
{ }
```
