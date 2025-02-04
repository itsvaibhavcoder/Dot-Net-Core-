# Implementing a Single Generic Repository in .NET

To consolidate multiple repositories into a single repository in your .NET application while following the Repository Pattern, you can implement a **Generic Repository Pattern**. This approach reduces code duplication and makes it easier to manage data access logic.

## Steps to Implement a Single Generic Repository

### 1. Create a Generic Repository Interface
Define a generic interface with common CRUD operations that can be applied to any entity.

```csharp
public interface IRepository<T> where T : class
{
    Task<IEnumerable<T>> GetAllAsync();
    Task<T> GetByIdAsync(int id);
    Task AddAsync(T entity);
    void Update(T entity);
    void Delete(T entity);
    Task SaveChangesAsync();
}
```

### 2. Implement the Generic Repository
Create a class that implements the `IRepository<T>` interface. This class will handle all the entities using the Entity Framework's `DbContext`.

```csharp
public class Repository<T> : IRepository<T> where T : class
{
    private readonly DbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(DbContext context)
    {
        _context = context;
        _dbSet = _context.Set<T>();
    }

    public async Task<IEnumerable<T>> GetAllAsync()
    {
        return await _dbSet.ToListAsync();
    }

    public async Task<T> GetByIdAsync(int id)
    {
        return await _dbSet.FindAsync(id);
    }

    public async Task AddAsync(T entity)
    {
        await _dbSet.AddAsync(entity);
    }

    public void Update(T entity)
    {
        _dbSet.Update(entity);
    }

    public void Delete(T entity)
    {
        _dbSet.Remove(entity);
    }

    public async Task SaveChangesAsync()
    {
        await _context.SaveChangesAsync();
    }
}
```

### 3. Register the Generic Repository in Dependency Injection (DI) Container
Configure the DI container in `Startup.cs` or `Program.cs` to use the generic repository.

```csharp
services.AddScoped(typeof(IRepository<>), typeof(Repository<>));
```

### 4. Use the Generic Repository in Services or Controllers
Now you can inject the generic repository into your services or controllers and specify the entity type you want to work with.

```csharp
public class BookService
{
    private readonly IRepository<Book> _bookRepository;

    public BookService(IRepository<Book> bookRepository)
    {
        _bookRepository = bookRepository;
    }

    public async Task<IEnumerable<Book>> GetAllBooksAsync()
    {
        return await _bookRepository.GetAllAsync();
    }

    public async Task AddBookAsync(Book book)
    {
        await _bookRepository.AddAsync(book);
        await _bookRepository.SaveChangesAsync();
    }
}
```

Similarly, you can inject `IRepository<Cart>`, `IRepository<Wishlist>`, or `IRepository<User>` in respective services.

---

## Advantages of Using a Generic Repository
1. **Code Reusability**: One repository class handles all entities.
2. **Maintainability**: Easier to maintain and update a single repository.
3. **Consistency**: Enforces consistent data access patterns across the application.

---

