# Enterprise Backend Application using .NET Core 3.1 MVC

## Prerequisites
- **.NET Core 3.1 SDK** ([Download](https://dotnet.microsoft.com/en-us/download/dotnet/3.1))
- **Visual Studio 2019 or later**
- **SQL Server** and **SQL Server Management Studio (SSMS)**
- **Entity Framework Core**

## Step 1: Create an ASP.NET Core 3.1 MVC Application
1. Open **Visual Studio** and select **Create a new project**.
2. Choose **ASP.NET Core Web Application** and click **Next**.
3. Name your project and click **Create**.
4. Select **.NET Core 3.1** and choose **Web Application (Model-View-Controller)**.

## Step 2: Configure Project Structure
```
/EnterpriseApp
 ├── Controllers/
 ├── Models/
 ├── Views/
 ├── Data/
 ├── Services/
 ├── Repositories/
 ├── Middleware/
 ├── Startup.cs
 ├── appsettings.json
```

## Step 3: Configure the Database (Entity Framework Core)
### Install Entity Framework Core Packages
```powershell
Install-Package Microsoft.EntityFrameworkCore.SqlServer
Install-Package Microsoft.EntityFrameworkCore.Tools
```

### Configure Database Context
Create `Data/AppDbContext.cs`:
```csharp
public class AppDbContext : DbContext
{
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options) { }
    public DbSet<Employee> Employees { get; set; }
}
```

### Define Model (`Models/Employee.cs`)
```csharp
public class Employee
{
    [Key]
    public int Id { get; set; }
    
    [Required]
    public string Name { get; set; }
    
    [Required]
    public string Position { get; set; }
    
    [Required]
    public decimal Salary { get; set; }
}
```

### Configure Connection String in `appsettings.json`
```json
"ConnectionStrings": {
  "DefaultConnection": "Server=YOUR_SERVER;Database=EnterpriseDB;Trusted_Connection=True;"
}
```

### Register DbContext in `Startup.cs`
```csharp
services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(Configuration.GetConnectionString("DefaultConnection")));
```

### Run Database Migrations
```powershell
Add-Migration InitialCreate
Update-Database
```

## Step 4: Implement Repository Pattern
### Define `IRepository<T>`
```csharp
public interface IRepository<T> where T : class
{
    Task<IEnumerable<T>> GetAll();
    Task<T> GetById(int id);
    Task Add(T entity);
    Task Update(T entity);
    Task Delete(int id);
}
```

### Implement `EmployeeRepository.cs`
```csharp
public class EmployeeRepository : IRepository<Employee>
{
    private readonly AppDbContext _context;
    public EmployeeRepository(AppDbContext context) { _context = context; }

    public async Task<IEnumerable<Employee>> GetAll() => await _context.Employees.ToListAsync();
    public async Task<Employee> GetById(int id) => await _context.Employees.FindAsync(id);
    public async Task Add(Employee entity) { await _context.Employees.AddAsync(entity); await _context.SaveChangesAsync(); }
    public async Task Update(Employee entity) { _context.Employees.Update(entity); await _context.SaveChangesAsync(); }
    public async Task Delete(int id) { var entity = await _context.Employees.FindAsync(id); if (entity != null) { _context.Employees.Remove(entity); await _context.SaveChangesAsync(); } }
}
```

### Register Repository in `Startup.cs`
```csharp
services.AddScoped<IRepository<Employee>, EmployeeRepository>();
```

## Step 5: Implement Controller (`Controllers/EmployeesController.cs`)
```csharp
public class EmployeesController : Controller
{
    private readonly IRepository<Employee> _repository;
    public EmployeesController(IRepository<Employee> repository) { _repository = repository; }

    public async Task<IActionResult> Index() => View(await _repository.GetAll());
    public async Task<IActionResult> Details(int id) => View(await _repository.GetById(id));
    public IActionResult Create() => View();
    
    [HttpPost]
    public async Task<IActionResult> Create(Employee employee)
    {
        if (ModelState.IsValid) { await _repository.Add(employee); return RedirectToAction(nameof(Index)); }
        return View(employee);
    }
    
    public async Task<IActionResult> Edit(int id) => View(await _repository.GetById(id));
    
    [HttpPost]
    public async Task<IActionResult> Edit(Employee employee)
    {
        if (ModelState.IsValid) { await _repository.Update(employee); return RedirectToAction(nameof(Index)); }
        return View(employee);
    }
    
    public async Task<IActionResult> Delete(int id) { await _repository.Delete(id); return RedirectToAction(nameof(Index)); }
}
```

## Step 6: Run and Test the Application
1. Run the application using **Ctrl + F5** in Visual Studio.
2. Navigate to `https://localhost:5001/Employees` to manage employees.

## Conclusion
- **MVC Architecture**
- **Repository Pattern**
- **Entity Framework Core for Database Management**
- **Separation of Concerns for Scalability**

This is a strong foundation for building an enterprise-grade **ASP.NET Core 3.1 MVC** backend application. 🚀
