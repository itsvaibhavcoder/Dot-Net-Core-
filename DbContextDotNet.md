# Database Context in .NET Core

In **.NET Core**, a **DbContext** (short for **Database Context**) is the primary class in **Entity Framework Core (EF Core)** for interacting with a database. It serves as a bridge between your **C# application** and the **database**, allowing you to query and save data using **Entity Framework**.

## 🔹 Key Responsibilities of DbContext
1. **Database Connection Management**  
   - Maintains the connection to the database using a connection string.

2. **Querying Data**  
   - Provides methods to query the database using **LINQ (Language Integrated Query)**.

3. **Tracking Changes**  
   - Keeps track of changes made to entities and ensures only modified data is updated.

4. **Saving Changes**  
   - Handles inserting, updating, and deleting data in the database.

5. **Transaction Handling**  
   - Supports transactions to ensure data consistency.

---

## 🔹 How to Use DbContext in .NET Core?

### 1️⃣ Create a Model Class
Define a C# class that represents a database table.

```csharp
public class Employee
{
    public int Id { get; set; }
    public string Name { get; set; }
    public string Department { get; set; }
}
```

### 2️⃣ Create a DbContext Class
Extend `DbContext` and define a `DbSet` property for each table.

```csharp
using Microsoft.EntityFrameworkCore;

public class AppDbContext : DbContext
{
    public DbSet<Employee> Employees { get; set; }

    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
}
```

### 3️⃣ Register DbContext in Dependency Injection
Modify `Program.cs` or `Startup.cs` to add DbContext.

```csharp
using Microsoft.EntityFrameworkCore;
using Microsoft.Extensions.DependencyInjection;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer("Your_Connection_String"));

var app = builder.Build();
```

### 4️⃣ Perform Database Operations
Inject `AppDbContext` in a service or controller.

```csharp
public class EmployeeService
{
    private readonly AppDbContext _context;

    public EmployeeService(AppDbContext context)
    {
        _context = context;
    }

    public List<Employee> GetEmployees()
    {
        return _context.Employees.ToList();
    }

    public void AddEmployee(Employee employee)
    {
        _context.Employees.Add(employee);
        _context.SaveChanges();
    }
}
```

---

## 🔹 Common Methods in DbContext

| Method               | Description                     |
|----------------------|--------------------------------|
| `SaveChanges()`      | Saves all changes to the database |
| `Add(entity)`       | Adds a new record              |
| `Remove(entity)`    | Deletes a record              |
| `Update(entity)`    | Updates a record              |
| `Find(id)`          | Finds a record by primary key |
| `ToList()`          | Retrieves all records as a list |
| `FirstOrDefault()`  | Retrieves the first matching record |

---

## 🔹 Conclusion
The **DbContext** in .NET Core acts as an **interface** between your application and the database, allowing smooth data operations using **Entity Framework Core**. It manages database connections, performs CRUD operations, and ensures efficient transaction handling.

Would you like a **code example** for a specific operation? 🚀
