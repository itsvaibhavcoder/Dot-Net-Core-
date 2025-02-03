# Using AutoMapper with the Repository Pattern in ASP.NET Core

**AutoMapper** is a library that helps in mapping objects from one type to another, which is particularly useful when you want to separate your **data models** (entities) from your **view models** (DTOs). When combined with the **Repository Pattern**, AutoMapper simplifies data transformation between entities and DTOs, making your code cleaner and more maintainable.

---

## How AutoMapper Works with the Repository Pattern

1. **Entity**: Represents the data model that maps to the database (e.g., `Book`).  
2. **DTO (Data Transfer Object)**: Represents the data sent to or received from the API (e.g., `BookDTO`).  
3. **Repository**: Contains methods to interact with the database (CRUD operations).  
4. **Service/Controller**: Uses the repository to get entities, then maps them to DTOs using AutoMapper.

---

## Step-by-Step Implementation

### 1. Install AutoMapper NuGet Package

```bash
 dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection
```

---

### 2. Create DTO Classes

**BookDTO.cs**

```csharp
namespace BookStore.DTOs
{
    public class BookDTO
    {
        public int Id { get; set; }
        public string Title { get; set; }
        public string Author { get; set; }
        public decimal Price { get; set; }
    }
}
```

---

### 3. Configure AutoMapper Profile

Create a profile to define the mapping between the **Book** entity and **BookDTO**.

**MappingProfile.cs**

```csharp
using AutoMapper;
using BookStore.Models;
using BookStore.DTOs;

public class MappingProfile : Profile
{
    public MappingProfile()
    {
        CreateMap<Book, BookDTO>();       // Entity to DTO
        CreateMap<BookDTO, Book>();       // DTO to Entity (for Create/Update)
    }
}
```

---

### 4. Register AutoMapper in `Startup.cs` or `Program.cs`

**Program.cs**

```csharp
using AutoMapper;

var builder = WebApplication.CreateBuilder(args);

// Register AutoMapper
builder.Services.AddAutoMapper(typeof(Program));

// Add repository and other services
builder.Services.AddScoped<IBookRepository, BookRepository>();

var app = builder.Build();
app.Run();
```

---

### 5. Modify the Repository

The repository will return **entities** (`Book`), and AutoMapper will be used in the controller to map them to **DTOs**.

**IBookRepository.cs**

```csharp
using BookStore.Models;
using System.Collections.Generic;
using System.Threading.Tasks;

public interface IBookRepository
{
    Task<IEnumerable<Book>> GetBooks();
    Task<Book> GetBookById(int id);
    Task<Book> AddBook(Book book);
    Task<Book> UpdateBook(Book book);
    Task<bool> DeleteBook(int id);
}
```

---

### 6. Use AutoMapper in the Controller

Inject `IMapper` into the controller and use it to map **entities** to **DTOs**.

**BooksController.cs**

```csharp
using AutoMapper;
using Microsoft.AspNetCore.Mvc;
using BookStore.Models;
using BookStore.DTOs;
using BookStore.Repositories;
using System.Collections.Generic;
using System.Threading.Tasks;

[Route("api/[controller]")]
[ApiController]
public class BooksController : ControllerBase
{
    private readonly IBookRepository _bookRepository;
    private readonly IMapper _mapper;

    public BooksController(IBookRepository bookRepository, IMapper mapper)
    {
        _bookRepository = bookRepository;
        _mapper = mapper;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<BookDTO>>> GetBooks()
    {
        var books = await _bookRepository.GetBooks();
        var booksDto = _mapper.Map<IEnumerable<BookDTO>>(books);  // Map Book to BookDTO
        return Ok(booksDto);
    }

    [HttpPost]
    public async Task<ActionResult<BookDTO>> AddBook([FromBody] BookDTO bookDto)
    {
        var book = _mapper.Map<Book>(bookDto);  // Map BookDTO to Book
        var newBook = await _bookRepository.AddBook(book);
        var newBookDto = _mapper.Map<BookDTO>(newBook);  // Map back to DTO for response

        return CreatedAtAction(nameof(GetBooks), new { id = newBookDto.Id }, newBookDto);
    }
}
```

---

## Advantages of Using AutoMapper with Repository Pattern

1. **Separation of Concerns**: Keeps your entity models and DTOs separate, making the code easier to maintain.
2. **Reduces Boilerplate Code**: Eliminates repetitive mapping logic.
3. **Consistency**: Ensures consistent mapping across the entire application.
4. **Scalability**: Easy to add new mappings as your application grows.

---

Let me know if you need more help setting this up in your project! 🚀

