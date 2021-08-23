## Flow
<img height="300px" src="https://user-images.githubusercontent.com/28957748/130363212-25715f96-dbff-49f0-bbab-91d262b47bd7.png"/>

## Server side
```csharp
_httpClient = new HttpClient
{
    BaseAddress = new System.Uri("https://graph.facebook.com")
};
// ...

[HttpPost("fbLogin")]
public async Task<ActionResult<UserDto>> FacebookLogin(string accessToken)
{
    var fbVerifyKeys = _config["Facebook:AppId"] + "|" + _config["Facebook:AppSecret"];

    var verifyToken = await _httpClient
        .GetAsync($"debug_token?input_token={accessToken}&access_token={fbVerifyKeys}");

    if (!verifyToken.IsSuccessStatusCode) return Unauthorized();

    var fbUrl = $"me?access_token={accessToken}&fields=name,email,picture.width(100).height(100)";

    var response = await _httpClient.GetAsync(fbUrl);

    if (!response.IsSuccessStatusCode) return Unauthorized();

    var content = await response.Content.ReadAsStringAsync();

    var fbInfo = JsonConvert.DeserializeObject<dynamic>(content);

    var username = (string)fbInfo.id;

    var user = await _userManager.Users.Include(u => u.Photos)
                    .FirstOrDefaultAsync(u => u.UserName == username);

    if (user != null) return CreateUserDto(user);

    user = new AppUser
    {
        DisplayName = (string)fbInfo.name,
        Email = (string)fbInfo.email,
        UserName = (string)fbInfo.id,
        Photos = new List<Photo>
        {
            new Photo
            {
                Id = "fb_" + (string) fbInfo.id,
                Url = (string) fbInfo.picture.data.url,
                IsMain = true
            }
        }
    };

    var result = await _userManager.CreateAsync(user);

    if (!result.Succeeded) return BadRequest("Problem creating user using facebook account");

    return CreateUserDto(user);
}
```
