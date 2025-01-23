# BookStoreAPI - Project Structure

## 📂 Controllers
- **AuthController.cs**: Handles authentication-related requests such as user registration and login.
  
### Example:
```csharp
[Route("api/auth")]
[ApiController]
public class AuthController : ControllerBase
{
    private readonly IUserRepository _userRepository;

    public AuthController(IUserRepository userRepository)
    {
        _userRepository = userRepository;
    }

    [HttpPost("register")]
    public async Task<IActionResult> Register(UserDTO userDto)
    {
        var user = new User { ... };
        await _userRepository.AddUserAsync(user);
        return Ok(new { message = "User registered successfully" });
    }
}
```

## 📂 Models
- **User.cs**: Defines the User entity with properties like `Id`, `FirstName`, `Email`, `Password`, etc.

### Example:
```csharp
public class User
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public string Email { get; set; }
    public string Password { get; set; }
}
```

## 📂 DTOs (Data Transfer Objects)
- **UserDTO.cs**: Used for registering users.
- **LoginDTO.cs**: Used for user login.

### Example:
```csharp
public class LoginDTO
{
    public string Email { get; set; }
    public string Password { get; set; }
}
```

## 📂 Repositories
- **IUserRepository.cs**: Defines methods for accessing user data.
- **UserRepository.cs**: Implements user data access logic using Entity Framework.

### Example:
```csharp
public interface IUserRepository
{
    Task AddUserAsync(User user);
    Task<User> GetUserByEmailAsync(string email);
}
```

## 📂 Data
- **AppDbContext.cs**: Configures the database context and defines the `Users` table.

### Example:
```csharp
public class AppDbContext : DbContext
{
    public DbSet<User> Users { get; set; }
}
```

## **Program.cs**
- Configures services, routing, and application setup.

### Example:
```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddDbContext<AppDbContext>(options => options.UseInMemoryDatabase("BookStoreDB"));
builder.Services.AddScoped<IUserRepository, UserRepository>();
builder.Services.AddControllers();
var app = builder.Build();
app.UseRouting();
app.MapControllers();
app.Run();
```

## Summary
This structured API follows clean architecture principles with clear separation of concerns, making it easy to manage authentication, database interactions, and routing efficiently.

