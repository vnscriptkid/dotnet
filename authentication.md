## JWT
* Libs: 
  * System.IdentityModel.Tokens.Jwt
  * Microsoft.AspNetCore.Authentication.JwtBearer

## HowTo: Retrieve `username` from jwt claims
```csharp
var username = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
```

## HowTo: Generate a token
* Code
```csharp
public class TokenService : ITokenService
{
    private readonly SymmetricSecurityKey _key;
    public TokenService(IConfiguration config)
    {
        _key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(config["TokenKey"]));
    }

    public string CreateToken(AppUser user)
    {
        var claims = new List<Claim>
        {
            new Claim(JwtRegisteredClaimNames.NameId, user.Id.ToString()),
            new Claim(JwtRegisteredClaimNames.UniqueName, user.Username),
        };

        var creds = new SigningCredentials(_key, SecurityAlgorithms.HmacSha512);

        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(claims),
            Expires = DateTime.Now.AddDays(7),
            SigningCredentials = creds
        };

        var tokenHandler = new JwtSecurityTokenHandler();

        var token = tokenHandler.CreateToken(tokenDescriptor);

        return tokenHandler.WriteToken(token);
    }
}
```
* Actual token
```js
// header
{
  "alg": "HS512",
  "typ": "JWT"
}
// payload
{
  "nameid": "1",
  "unique_name": "ashley",
  "nbf": 1627644096,
  "exp": 1628248896,
  "iat": 1627644096
}
```
