## Respond in same format for validation errors
* Old code
```csharp
if (await _userManager.Users.AnyAsync(u => u.UserName == registerDto.Username.ToLower()))
{
    return BadRequest("Username already exists");
}
```

* Refactor
```csharp
if (await _userManager.Users.AnyAsync(u => u.UserName == registerDto.Username.ToLower()))
{
    ModelState.AddModelError("username", "Username has been taken");
    return ValidationProblem();
}
