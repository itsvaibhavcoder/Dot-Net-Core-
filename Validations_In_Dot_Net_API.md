### **Custom Validation in .NET Web APIs**

In ASP.NET Core Web APIs, custom validation allows you to enforce specific business rules that go beyond the built-in validation attributes like `[Required]`, `[StringLength]`, or `[Range]`. You can create custom validation attributes, model-level validation, or use middleware for more complex scenarios.

---

### **1. Custom Validation Using Attributes**

You can create a custom validation attribute by inheriting from the `ValidationAttribute` class.

#### **Steps:**

1. **Create the Custom Attribute**

```csharp
using System.ComponentModel.DataAnnotations;

public class NoSpecialCharactersAttribute : ValidationAttribute
{
    protected override ValidationResult IsValid(object value, ValidationContext validationContext)
    {
        if (value != null)
        {
            var stringValue = value.ToString();
            if (System.Text.RegularExpressions.Regex.IsMatch(stringValue, @"[^a-zA-Z0-9]"))
            {
                return new ValidationResult("Special characters are not allowed.");
            }
        }
        return ValidationResult.Success;
    }
}
```

2. **Apply the Custom Attribute to a Model**

```csharp
public class UserModel
{
    [Required]
    public string FirstName { get; set; }

    [NoSpecialCharacters(ErrorMessage = "Username cannot contain special characters.")]
    public string Username { get; set; }
}
```

3. **Controller Example**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsersController : ControllerBase
{
    [HttpPost]
    public IActionResult CreateUser([FromBody] UserModel user)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        return Ok("User created successfully!");
    }
}
```

---

### **2. Model-Level Validation (IValidatableObject Interface)**

For scenarios where validation involves multiple properties, implement the `IValidatableObject` interface.

#### **Steps:**

1. **Implement the Interface in the Model**

```csharp
using System.ComponentModel.DataAnnotations;

public class ProductModel : IValidatableObject
{
    public string Name { get; set; }
    public decimal Price { get; set; }
    public decimal Discount { get; set; }

    public IEnumerable<ValidationResult> Validate(ValidationContext validationContext)
    {
        if (Discount > Price)
        {
            yield return new ValidationResult("Discount cannot be greater than the price.", new[] { nameof(Discount) });
        }
    }
}
```

2. **Controller Example**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    [HttpPost]
    public IActionResult CreateProduct([FromBody] ProductModel product)
    {
        if (!ModelState.IsValid)
        {
            return BadRequest(ModelState);
        }
        return Ok("Product created successfully!");
    }
}
```

---

### **3. Custom Middleware for Validation**

For more complex validation scenarios (e.g., cross-cutting concerns like token validation), you can create custom middleware.

#### **Steps:**

1. **Create Middleware**

```csharp
public class CustomValidationMiddleware
{
    private readonly RequestDelegate _next;

    public CustomValidationMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        // Example: Check for a specific header
        if (!context.Request.Headers.ContainsKey("X-Custom-Header"))
        {
            context.Response.StatusCode = 400;
            await context.Response.WriteAsync("Missing X-Custom-Header");
            return;
        }

        await _next(context);
    }
}
```

2. **Register Middleware in `Program.cs`**

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseMiddleware<CustomValidationMiddleware>();

app.MapControllers();

app.Run();
```

---

### **4. Fluent Validation (Advanced)**

[FluentValidation](https://fluentvalidation.net/) is a popular library that offers a fluent interface for building complex validation rules.

#### **Steps:**

1. **Install FluentValidation**

```bash
dotnet add package FluentValidation.AspNetCore
```

2. **Create a Validator**

```csharp
using FluentValidation;

public class UserModelValidator : AbstractValidator<UserModel>
{
    public UserModelValidator()
    {
        RuleFor(x => x.FirstName).NotEmpty().WithMessage("First Name is required.");
        RuleFor(x => x.Username).Matches(@"^[a-zA-Z0-9]*$").WithMessage("Username cannot contain special characters.");
    }
}
```

3. **Register FluentValidation in `Program.cs`**

```csharp
builder.Services.AddControllers()
    .AddFluentValidation(fv => fv.RegisterValidatorsFromAssemblyContaining<UserModelValidator>());
```

---

### **Conclusion**

Custom validation in .NET Web APIs is flexible and can be implemented using attributes, model-level validation, middleware, or third-party libraries like FluentValidation. Choose the method that best fits your validation requirements.

