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

## HowTo: Send message to all others except me
📭 Scenario: New online user, notify all other online users about new user
```csharp
await Clients.Others.SendAsync("UserIsOnline", Context.User.GetUsername());
```

## HowTo: Send message to only me
📭 Scenario: New online user, notify only that user about all online users
```csharp
await Clients.Caller.SendAsync("GetOnlineUsers", onlineUsers);
```

## DESIGN: Chat thread using SocketIO
* :one: User Story 1: When user first accesses a chat thread, server sends back all messages belong to that thread
* :two: User Story 2: When both users A & B are seeing their chat thread, A sends B a message, B instantly receives it
* :three: User Story 3: When A sends B a message, message is marked unseen, B then navigates to chat thread, message is marked seen instantly
* :four: User Story 4: When A sends B a message, B is online but not seeing thread, B receives a notification about new message coming, that is linked to thread

## DESIGN: Online tracker using SocketIO
- :one: User Story 1: When user A is online by first device
    - User A receives a list of currently online users
    - Other online users receives info of A comming online
- :two: User Story 2: When user B is online by second device (2 connections at the same time)
    - User A receives a list of currently online users (to his second device)
    - Other online users receive nothing (he's still online)
- :three: User Story 3: When user A comes offline from his single device
    - Other online users receives info of A coming offline
- :four: User Story 4: When user A comes offline from his second device
    - Other online users receives nothing (A is still online with his first device)
