## Idea
- Maintains a list of objects
- coordinates the writing out of changes
- Work of writting to DB is of UnitOfWork, no longer doing something like `await _context.SaveChangesAsync();` in Repository layer

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
