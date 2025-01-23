# Repository Pattern in .NET Core

The **Repository Pattern** in **.NET Core** is used to abstract database access logic and provide a centralized way to perform CRUD operations. It helps in **decoupling** the data layer from the business logic, making the application **more maintainable, testable, and scalable**.

## 🔹 Benefits of Repository Pattern
1. **Encapsulation** - Hides direct database access from business logic.
2. **Code Reusability** - Provides a generic way to interact with data.
3. **Unit Testing** - Easier to mock repositories in unit tests.
4. **Separation of Concerns** - Keeps data access logic separate from the application logic.

---

## 🔹 How to Implement Repository Pattern in .NET Core?

### 1️⃣ Define an Entity Model
Create a model class representing a database table.

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Department { get; set; }
}
```

### 2️⃣ Create a Generic Repository Interface
Define common CRUD operations in an interface.

```csharp
using System.Collections.Generic;

public interface IRepository<T> where T : class
{
    IEnumerable<T> GetAll();
    T GetById(int id);
    void Add(T entity);
    void Update(T entity);
    void Delete(int id);
}
```

### 3️⃣ Implement Generic Repository
Implement the repository interface using **Entity Framework Core**.

```csharp
using Microsoft.EntityFrameworkCore;
using System.Collections.Generic;
using System.Linq;

public class Repository<T> : IRepository<T> where T : class
{
    private readonly AppDbContext _context;
    private readonly DbSet<T> _dbSet;

    public Repository(AppDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public IEnumerable<T> GetAll() => _dbSet.ToList();

    public T GetById(int id) => _dbSet.Find(id);

    public void Add(T entity)
    {
        _dbSet.Add(entity);
        _context.SaveChanges();
    }

    public void Update(T entity)
    {
        _dbSet.Update(entity);
        _context.SaveChanges();
    }

    public void Delete(int id)
    {
        var entity = _dbSet.Find(id);
        if (entity != null)
        {
            _dbSet.Remove(entity);
            _context.SaveChanges();
        }
    }
}
```

### 4️⃣ Register Repository in Dependency Injection
Modify `Program.cs` to register the repository.

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer("Your_Connection_String"));

builder.Services.AddScoped(typeof(IRepository<>), typeof(Repository<>));

var app = builder.Build();
```

### 5️⃣ Use Repository in Service or Controller
Inject the repository into a service or controller.

```csharp
public class EmployeeService
{
    private readonly IRepository<Employee> _employeeRepository;

    public EmployeeService(IRepository<Employee> employeeRepository)
    {
        _employeeRepository = employeeRepository;
    }

    public List<Employee> GetEmployees()
    {
        return _employeeRepository.GetAll().ToList();
    }

    public void AddEmployee(Employee employee)
    {
        _employeeRepository.Add(employee);
    }
}
```

---

## 🔹 Conclusion
The **Repository Pattern** provides a structured way to handle data access in **.NET Core**, improving maintainability and testability. By using a **generic repository**, we can implement a **clean, reusable** data access layer that works with any entity.

Would you like an example for a **specific database operation**? 🚀
