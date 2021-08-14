## HowTo: Get `currentUser` in MappingProfiles
* Passing second arg in `ProjectTo`
```csharp
var profile = await _context.Users
    .ProjectTo<Profile>(
        _mapper.ConfigurationProvider,
        new { currentUsername = _userAccessor.GetUsername() }
    )
    .SingleOrDefaultAsync(u => u.Username == request.Username);
```
* Have access on the fly
```csharp
string currentUsername = null;

CreateMap<AppUser, Profiles.Profile>()
    .ForMember(d => d.Username, e => e.MapFrom(a => a.UserName))
    .ForMember(d => d.Image, e => e.MapFrom(a => a.Photos.FirstOrDefault(p => p.IsMain).Url))
    .ForMember(d => d.FollowersCount, e => e.MapFrom(a => a.Followers.Count))
    .ForMember(d => d.FollowingCount, e => e.MapFrom(a => a.Followings.Count))
    .ForMember(d => d.Following, e =>
        e.MapFrom(a => a.Followers.Any(f => f.Observer.UserName == currentUsername)));
```













