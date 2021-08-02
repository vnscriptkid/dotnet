## What
- A thread getting a lock over a resource, means no other threads can access resource while lock is hold.

## Use case: Online tracker
```csharp
public class PresenceTracker
    {
        private static readonly Dictionary<string, List<string>> OnlineUsers
            = new Dictionary<string, List<string>>();

        public Task UserConnected(string username, string connectionId)
        {
            lock (OnlineUsers)
            {
                if (!OnlineUsers.ContainsKey(username))
                {
                    OnlineUsers.Add(username, new List<string>());
                }
                OnlineUsers[username].Add(connectionId);
            }

            return Task.CompletedTask;
        }
```        
