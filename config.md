## HOWTO: Load config from `appsettings`
#### Put config in `appsettings.json`
```json
{
  "CloudinarySettings": {
    "CloudName": "vnscriptkid",
    "ApiKey": "264291632115263",
    "ApiSecret": "UCLDl22QDMU7nBlxFbuJpDxS1BU"
  }
}
```
#### Make a class to hold config in `Helpers/CloudinarySettings.cs`
```csharp
public class CloudinarySettings
{
    public string CloudName { get; set; }
    public string ApiKey { get; set; }
    public string ApiSecret { get; set; }
}
```
#### Register a mapping between the piece of config with the class in `Extensions/ApplicationServiceExtensions.cs`
```csharp
services.Configure<CloudinarySettings>(config.GetSection("CloudinarySettings"));
```

#### Inject config to anywhere
```csharp
public PhotoService(IOptions<CloudinarySettings> config)
```
