## Optimize queries
https://www.thereformedprogrammer.net/building-efficient-database-queries-using-entity-framework-core-and-automapper/

## :one: Select what it is needed
### :star: Use case 1
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

### :star: Use case 2
#### Avoid `select *`
```csharp
return await _context.Activities.FindAsync(request.Id);
```

#### Use `queryable extensions`
```csharp
return await _context.Activities
                        .ProjectTo<ActivityDto>(_mapper.ConfigurationProvider) // while querying, only only fields listed in `ActivityDto`
                        .FirstOrDefaultAsync(a => a.Id == request.Id);
```                    
