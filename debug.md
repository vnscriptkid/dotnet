## Debug context tracking
https://docs.microsoft.com/en-us/ef/core/change-tracking/change-detection
```csharp
Console.WriteLine(_context.ChangeTracker.DebugView.LongView);
_context.ChangeTracker.DetectChanges();
Console.WriteLine(_context.ChangeTracker.DebugView.LongView);
```
