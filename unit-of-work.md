## Idea
- Maintains a list of objects => avoid inconsistent reads
- coordinates the writing out of changes
- 1 request === 1 transaction
- Work of writting to DB is of UnitOfWork, no longer doing something like `await _context.SaveChangesAsync();` in Repository layer
- Pros of having only one instance of `dataContext`, injecting to different repositories:

## Traditional approach
```csharp
// in some random controller
repoX.getSomething()
repoX.addSomething();
repoY.deleteSomething();
repoZ.UpdateSomething();
```
- Problems: 
    - Each repo has it's own `_dataContext`, considered as separate transactions

## Code
* Move repos into `UnitOfWork`
```csharp
public class UnitOfWork : IUnitOfWork
{
    private readonly DataContext _dataContext;
    private readonly IMapper _mapper;

    public UnitOfWork(DataContext dataContext, IMapper mapper)
    {
        _mapper = mapper;
        _dataContext = dataContext;
    }

    public IUserRepository UserRepository => new UserRepository(_dataContext, _mapper);

    public IMessageRepository MessageRepository => new MessageRepository(_dataContext, _mapper);

    public ILikesRepository LikesRepository => new LikesRepository(_dataContext);
    
    public async Task<bool> Complete()
    {
        return await _dataContext.SaveChangesAsync() > 0;
    }

    // keep track of what you've changed
    public bool HasChanges()
    {
        return _dataContext.ChangeTracker.HasChanges();
    }
}  
```


