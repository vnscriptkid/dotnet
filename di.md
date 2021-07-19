# Dependency Injection

## 3 approaches
https://stackoverflow.com/questions/38138100/addtransient-addscoped-and-addsingleton-services-differences
* Singleton: object stays for the lifetime of app
* Scoped: lifetime of object is lifetime of each request (used most of the time)
* Transient: object is created when it's requested

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
