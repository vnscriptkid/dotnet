# Data Transfer Object
https://martinfowler.com/bliki/LocalDTO.html

## Problems solved
- Validation
- Prevent circular dependencies when eager-loading
- Restrict what data to be sent
  - Don't expose user's password fields
- Select desired fields while querying database

## What is automapping
- Map an object to another object
- How it happens?
  - `Username` 👉 `Username`
  - `GetAge()` 👉 `Age`

## Tricks
- Make a field available but not send back to client
```csharp
[JsonIgnore]
public bool RecipientDeleted { get; set; }
```

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

## Automapper Usecase 2
* __Problem__: By default, Entity Framework select all fields while querying database (`SELECT * FROM`)
* __Solution__: Chain on `.Select()` API offered by EF to selectively retrieve desired fields __manually__
* __Alternative__: `ProjectTo` a __DTO__
```csharp
public async Task<MemberDto> GetMemberByUsernameAsync(string Username)
{
    return await _context.Users
        .Where(u => u.Username == Username)
        .ProjectTo<MemberDto>(_mapper.ConfigurationProvider)
        .SingleOrDefaultAsync();
}
```
#### ⚠️☠️ Gotchas !!! Having something like this in source class might prevent `projectTo` functioning correctly
```csharp
public int getAge()
{
    return DateOfBirth.CalculateAge();
}
```
👉✔️ Move this logic into `AutoMapperProfiles`
```csharp
public AutoMapperProfiles()
{
    CreateMap<AppUser, MemberDto>()
        .ForMember(
            dest => dest.Age,
            opt => opt.MapFrom(src => src.DateOfBirth.CalculateAge())
        );
}        
```

## DTO Usecase: Partial update endpoint
#### Restrict certain fields to be updated
```csharp
public class MemberUpdateDto
{
    public string Introduction { get; set; }
    public string LookingFor { get; set; }
    public string Interests { get; set; }
}
```
#### 🔌 Plug in controller param to filter user's input
```csharp
[HttpPut]
public async Task<ActionResult> UpdateUser(MemberUpdateDto memberUpdateDto)
```

#### Populate model object with updated data (⚠️Must register mapping config)
```csharp
_mapper.Map(memberUpdateDto, user);
```

#### Register mapping config for above code
```csharp
CreateMap<MemberUpdateDto, AppUser>();
```
