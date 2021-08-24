# Dependency Injection

## 3 approaches
https://stackoverflow.com/questions/38138100/addtransient-addscoped-and-addsingleton-services-differences
* Singleton: object stays for the lifetime of app
* Scoped: lifetime of object is lifetime of each request (used most of the time)
* Transient: object is created when it's requested

## When to use each
- If you're having memory problems and have configured __Transient__ registrations for your IoC container, try using __Scoped__ registrations instead.
- make sure __objects__ constructed by your container are being __disposed properly__

## How it effects objects created
- DI approaches decide when object is created, how long is it's lifetime, when it is destroyed

## Benefits of using interface
- Easy for testing by mocking

## Use case 1
```csharp
// Startup.cs
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<ITokenService, TokenService>();
}   
```
 ## Use case of singleton service: Online tracker
 ```csharp
 // Service class
 public class PresenceTracker
    {
        private static readonly Dictionary<string, List<string>> OnlineUsers
            = new Dictionary<string, List<string>>();

        public Task UserConnected(string username, string connectionId)
        {
            lock (OnlineUsers)
            {
                // add user to dict
            }

            return Task.CompletedTask;
        }

        public Task UserDisconnected(string username, string connectionId)
        {
            lock (OnlineUsers)
            {
                // remove user from dict
            }

            return Task.CompletedTask;
        }

        public Task<string[]> GetOnlineUsers()
        {
            lock (OnlineUsers)
            {
                // get online users
            }

            // return online users
        }
    }

// Register that service
services.AddSingleton<PresenceTracker>();
 ```

## Interview
https://www.youtube.com/watch?v=yVKTvtevM0w
