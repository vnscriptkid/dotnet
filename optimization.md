## Optimize queries
https://www.thereformedprogrammer.net/building-efficient-database-queries-using-entity-framework-core-and-automapper/

## Select what it is needed
#### Avoid `select *`
```csharp
var user = await _unitOfWork.UserRepository.GetUserByUsernameAsync(User.GetUsername());

// down here, only user.Gender is used
```
#### Refactor
```csharp
var username = await _context.Users
                .Include(u => u.Photos)
                .SingleOrDefaultAsync(u => u.UserName == username);
```
