## Cloudinary
* Install package: CloudinaryDotNet
* Add these to settings file
```json
"Cloudinary": {
  "CloudName": "",
  "ApiKey": "",
  "ApiSecret": ""
}
```
* Create a class holding settings
```csharp
public class CloudinarySettings
{
    public string CloudName { get; set; }
    public string ApiKey { get; set; }
    public string ApiSecret { get; set; }
}
```
* Register the mapping
```csharp
services.Configure<CloudinarySettings>(config.GetSection("Cloudinary"));
```
