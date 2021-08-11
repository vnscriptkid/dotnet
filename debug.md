## Debug context tracking
```csharp
Console.WriteLine(_context.ChangeTracker.DebugView.LongView);
_context.ChangeTracker.DetectChanges();
Console.WriteLine(_context.ChangeTracker.DebugView.LongView);
```
