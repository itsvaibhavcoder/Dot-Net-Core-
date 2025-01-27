# AutoMapper in Entity Framework Core

AutoMapper is a popular **object-to-object mapping library** that helps in mapping **entities** (models) to **DTOs** (Data Transfer Objects) in **Entity Framework Core**. It eliminates the need for manual property assignments and reduces boilerplate code.

## Why Use AutoMapper?
- **Reduces repetitive code** for object conversion.
- **Automatically maps properties** with the same names.
- Helps in **decoupling** the domain model from DTOs.
- Increases **maintainability** and **readability** of the code.

## How AutoMapper Works in EF Core
AutoMapper automatically maps properties **from one object to another** based on matching **property names**.

### Example Scenario
- **Entity Model:** `Book`
- **DTO (Data Transfer Object):** `BookDTO`
- **Controller that returns `BookDTO` instead of `Book`**

## 1️⃣ Install AutoMapper
First, install the required **NuGet packages**:

```sh
dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
```

## 2️⃣ Define the Entity and DTO
### Entity Model (`Book.cs`)
```csharp
public class Book
{
    public int Id { get; set; }
    public string Title { get; set; }
    public string Author { get; set; }
    public decimal Price { get; set; }
}
```

### DTO (`BookDTO.cs`)
```csharp
public class BookDTO
{
    public string Title { get; set; }
    public string Author { get; set; }
}
```
Here, we are **excluding** the `Id` and `Price` properties from the DTO.

## 3️⃣ Create a Mapping Profile
AutoMapper requires a **mapping profile** to define the object-to-object mapping.

### Mapping Profile (`MappingProfile.cs`)
```csharp
using AutoMapper;
public class MappingProfile : Profile
{
    public MappingProfile()
    {
        // Map Book → BookDTO
        CreateMap<Book, BookDTO>();
        
        // Map BookDTO → Book (if needed)
        CreateMap<BookDTO, Book>();
    }
}
```

## 4️⃣ Register AutoMapper in `Program.cs`
We need to **register AutoMapper** in the **dependency injection container**.

### Modify `Program.cs`
```csharp
var builder = WebApplication.CreateBuilder(args);

// Register AutoMapper
builder.Services.AddAutoMapper(typeof(MappingProfile));

var app = builder.Build();
```

## 5️⃣ Use AutoMapper in Controller
Now, we can **inject AutoMapper** into the controller and use it to convert `Book` entities to `BookDTO`.

### Controller (`BookController.cs`)
```csharp
[Route("api/[controller]")]
[ApiController]
public class BookController : ControllerBase
{
    private readonly BookStoreContext _context;
    private readonly IMapper _mapper;

    public BookController(BookStoreContext context, IMapper mapper)
    {
        _context = context;
        _mapper = mapper;
    }

    [HttpGet]
    public async Task<IActionResult> GetBooks()
    {
        var books = await _context.Books.ToListAsync();
        
        // Map Book → BookDTO
        var bookDTOs = _mapper.Map<List<BookDTO>>(books);
        
        return Ok(bookDTOs);
    }
}
```

## 6️⃣ Explanation of Key Parts
| Code | Explanation |
|------|------------|
| `CreateMap<Book, BookDTO>()` | Defines a mapping from `Book` to `BookDTO`. |
| `_mapper.Map<List<BookDTO>>(books)` | Converts the list of `Book` entities into a list of `BookDTO` objects. |
| `builder.Services.AddAutoMapper(typeof(MappingProfile))` | Registers AutoMapper in `Program.cs`. |
| `IMapper _mapper` | Injects AutoMapper into the controller. |

## 7️⃣ Benefits of AutoMapper in EF Core
✅ Reduces **boilerplate code** for property assignments.  
✅ Automatically maps **matching property names**.  
✅ Helps in **separating** database models from API response models (DTOs).  
✅ Improves **maintainability** and **code readability**.  

## Final Thoughts
AutoMapper **simplifies data mapping** between **entities** and **DTOs** in **Entity Framework Core**. It helps in **cleaner, more maintainable** code while **avoiding manual property assignments**.

