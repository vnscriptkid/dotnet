## :star: Store utc time in server
* Default value for entity's field
```csharp
public DateTime MessageSent { get; set; } = DateTime.UtcNow;
```
* Somewhere, when updating latest time
```csharp
message.DateRead = DateTime.UtcNow;
```

## Server may not return in standard utc format with `z` at the end
* Do it manually with mapper
```csharp
CreateMap<DateTime, DateTime>().ConvertUsing(d => DateTime.SpecifyKind(d, DateTimeKind.Utc));
```
