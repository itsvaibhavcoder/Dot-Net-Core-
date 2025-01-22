# Dependency Injection in .NET Core 3.1

## 1. What is Dependency Injection?
Dependency Injection (DI) is a design pattern used to achieve Inversion of Control (IoC) by injecting dependencies into a class rather than allowing the class to create them. This helps in:
- Reducing tight coupling between classes.
- Enhancing testability and maintainability.
- Managing object lifetimes effectively.

## 2. Dependency Injection in .NET Core 3.1
.NET Core 3.1 has built-in support for Dependency Injection without requiring third-party libraries. It follows a convention where dependencies are registered in the `Startup.cs` file and injected wherever needed.

### Key Components
1. **Service Registration** - Registering dependencies in the `IServiceCollection`.
2. **Service Resolution** - The DI container resolves dependencies automatically.
3. **Lifetime Management** - Controlling the lifecycle of registered services.

## 3. Types of Dependency Injection
.NET Core 3.1 supports three types of Dependency Injection:
- **Constructor Injection** (Recommended)
- **Method Injection**
- **Property Injection**

### Constructor Injection Example
```csharp
public interface IMessageService
{
    void SendMessage(string message);
}

public class EmailService : IMessageService
{
    public void SendMessage(string message)
    {
        Console.WriteLine($"Email sent: {message}");
    }
}

public class NotificationService
{
    private readonly IMessageService _messageService;

    public NotificationService(IMessageService messageService)
    {
        _messageService = messageService;
    }

    public void Notify(string message)
    {
        _messageService.SendMessage(message);
    }
}
```

## 4. Registering Services in `Startup.cs`
To enable DI, register dependencies in the `ConfigureServices` method:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddScoped<IMessageService, EmailService>();
    services.AddScoped<NotificationService>();
}
```
Here, `IMessageService` is mapped to `EmailService`, and `NotificationService` is also registered.

## 5. Injecting Services in Controllers
In ASP.NET Core MVC, you can inject services into controllers:
```csharp
public class HomeController : Controller
{
    private readonly NotificationService _notificationService;

    public HomeController(NotificationService notificationService)
    {
        _notificationService = notificationService;
    }

    public IActionResult Index()
    {
        _notificationService.Notify("Hello, Dependency Injection!");
        return View();
    }
}
```

## 6. Service Lifetimes
- **Transient:** Created each time it is requested.
  ```csharp
  services.AddTransient<IMessageService, EmailService>();
  ```
- **Scoped:** Created once per request.
  ```csharp
  services.AddScoped<IMessageService, EmailService>();
  ```
- **Singleton:** Created once and shared across the application.
  ```csharp
  services.AddSingleton<IMessageService, EmailService>();
  ```

## 7. Summary
- Dependency Injection in .NET Core 3.1 helps manage dependencies efficiently.
- Services are registered in `Startup.cs` and injected using constructors.
- Different lifetimes (`Transient`, `Scoped`, `Singleton`) help control service instances.

