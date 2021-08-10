## Usecase 1: You must be creator in order to edit it
* Unauthorized code
```csharp
[HttpPut("{id}")]
public async Task<ActionResult> UpdateActivity(Guid id, Activity activity)
{
    activity.Id = id;
    return Ok(await Mediator.Send(new Edit.Command { Activity = activity }));
}
```

* Create a requirement
```csharp
protected override Task HandleRequirementAsync(AuthorizationHandlerContext context, IsHostRequirement requirement)
{
    var userId = context.User.FindFirstValue(ClaimTypes.NameIdentifier);

    if (userId == null) return Task.CompletedTask;

    var activityId = Guid.Parse(_httpContextAccessor.HttpContext.Request.RouteValues
        .SingleOrDefault(x => x.Key == "id").Value?.ToString());

    var attendee = _dbContext.ActivityAttendees.FindAsync(userId, activityId).Result;

    if (attendee == null) return Task.CompletedTask;

    if (attendee.isHost) context.Succeed(requirement);  

    return Task.CompletedTask;
}
```
* Register the requirement
```csharp

```
* Authorize endpoint
```csharp

```
