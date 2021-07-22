# Data Transfer Object
https://martinfowler.com/bliki/LocalDTO.html

## Problems solved
- Validation
- Prevent circular dependencies when eager-loading
- Restrict what data to be sent
  - Don't expose user's password fields

## What is automapping
- Map an object to another object
- How it happens?
  - `Username` 👉 `Username`
  - `GetAge()` 👉 `Age`

## How to automapping
#### :one: Grab the lib: AutoMapper.Extensions.Microsoft.DependencyInjection
#### :two: Create the mapping profiles:
```csharp
// Helpers/AutoMapperProfiles.cs
public class AutoMapperProfiles : Profile
{
    public AutoMapperProfiles()
    {
        CreateMap<AppUser, MemberDto>();
        CreateMap<Photo, PhotoDto>();
    }
}
```
#### :three: Register automapper service with the profile
```csharp
// Extensions/ApplicationServiceExtensions.cs
services.AddAutoMapper(typeof(AutoMapperProfiles).Assembly);
```

#### :four: Do the mapping
```csharp
var users = await _userRepository.GetAllUsersAsync();

var usersToReturn = _mapper.Map<IEnumerable<MemberDto>>(users);

return Ok(usersToReturn);
```

## Do some processing while automapping
```csharp
// Helpers/AutoMapperProfiles.cs
public AutoMapperProfiles()
{
    CreateMap<AppUser, MemberDto>()
        .ForMember(
            dest => dest.PhotoUrl,
            opt => opt.MapFrom(src => src.Photos.FirstOrDefault(p => p.IsMain).Url)
        );
}               
```
