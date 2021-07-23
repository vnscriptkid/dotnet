## JWT
* Libs: 
  * System.IdentityModel.Tokens.Jwt
  * Microsoft.AspNetCore.Authentication.JwtBearer

## HowTo: Retrieve `username` from jwt claims
```csharp
var username = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
```
