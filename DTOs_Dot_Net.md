# Data Transfer Objects (DTOs) in .NET Core 3.1

## **What are DTOs?**
DTOs (Data Transfer Objects) are objects used to transfer data between different layers in an application. They serve as intermediaries between the database layer and the presentation layer, ensuring encapsulation and security.

## **Why Use DTOs?**
### ✅ Security
- Prevents exposing internal domain models.
- Reduces the risk of overposting attacks.

### ✅ Performance
- Limits the data sent over the network.
- Improves API response times by transferring only required fields.

### ✅ Maintainability
- Separates API contracts from database models.
- Allows easy modifications without breaking the domain layer.

---

## **Implementing DTOs in .NET Core 3.1**

### **1. Creating DTO Classes**

#### **BookDTO.cs** (Used for API responses)
```csharp
namespace BookStoreAPI.Models.DTOs
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

#### **BookCreateDTO.cs** (Used for book creation requests)
```csharp
namespace BookStoreAPI.Models.DTOs
{
    public class BookCreateDTO
    {
        public string Title { get; set; }
        public string Author { get; set; }
        public decimal Price { get; set; }
    }
}
```

#### **BookUpdateDTO.cs** (Used for book update requests)
```csharp
namespace BookStoreAPI.Models.DTOs
{
    public class BookUpdateDTO
    {
        public string Title { get; set; }
        public string Author { get; set; }
        public decimal Price { get; set; }
    }
}
```

---

### **2. Updating Repository to Use DTOs**
Modify the `IBookRepository.cs` interface:
```csharp
public interface IBookRepository
{
    Task<IEnumerable<BookDTO>> GetBooks();
    Task<BookDTO> GetBookById(int id);
    Task<BookDTO> AddBook(BookCreateDTO bookDto);
    Task<BookDTO> UpdateBook(int id, BookUpdateDTO bookDto);
    Task<bool> DeleteBook(int id);
}
```

---

### **3. Implementing DTO Logic in Repository**
Modify `BookRepository.cs`:
```csharp
public async Task<IEnumerable<BookDTO>> GetBooks()
{
    return await _context.Books
        .Select(b => new BookDTO
        {
            Id = b.Id,
            Title = b.Title,
            Author = b.Author,
            Price = b.Price
        }).ToListAsync();
}
```

---

### **4. Updating Controller to Use DTOs**
Modify `BooksController.cs`:
```csharp
[HttpPost]
public async Task<ActionResult<BookDTO>> AddBook(BookCreateDTO bookDto)
{
    var newBook = await _bookRepository.AddBook(bookDto);
    return CreatedAtAction(nameof(GetBookById), new { id = newBook.Id }, newBook);
}
```

---

### **5. Testing with Postman**
Test API endpoints:
- **GET** `/api/books`
- **GET** `/api/books/{id}`
- **POST** `/api/books`
```json
{
  "title": "Book Name",
  "author": "Author Name",
  "price": 19.99
}
```
- **PUT** `/api/books/{id}`
- **DELETE** `/api/books/{id}`

---

## **Conclusion**
Using DTOs ensures:
✅ **Security** – Protects internal models.  
✅ **Performance** – Optimized data transfer.  
✅ **Maintainability** – Clean API contracts.  

