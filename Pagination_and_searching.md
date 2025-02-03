# Implementing Pagination and Search in GetBooks API

## 1. Modify the GetBooks Method to Accept Query Parameters

```csharp
[HttpGet]
public async Task<ActionResult<IEnumerable<Book>>> GetBooks(
    [FromQuery] string searchTerm = "",
    [FromQuery] int pageNumber = 1,
    [FromQuery] int pageSize = 10)
{
    if (pageNumber <= 0 || pageSize <= 0)
    {
        return BadRequest(new { message = "Page number and page size must be greater than zero." });
    }

    var books = await _bookRepository.GetBooks(searchTerm, pageNumber, pageSize);

    if (books == null || !books.Any())
    {
        throw new ArgumentException("No books found.");  // This triggers 400 BadRequest via middleware
    }

    return Ok(books);
}
```

---

## 2. Update Repository Method to Support Search and Pagination

**In `IBookRepository.cs`:**

```csharp
Task<IEnumerable<Book>> GetBooks(string searchTerm, int pageNumber, int pageSize);
```

**In `BookRepository.cs`:**

```csharp
public async Task<IEnumerable<Book>> GetBooks(string searchTerm, int pageNumber, int pageSize)
{
    var query = _context.Books.AsQueryable();

    // Apply search filter if searchTerm is provided
    if (!string.IsNullOrEmpty(searchTerm))
    {
        query = query.Where(b => b.Title.Contains(searchTerm) || b.Author.Contains(searchTerm));
    }

    // Get total count for pagination metadata (if needed)
    int totalItems = await query.CountAsync();

    // Apply pagination
    var books = await query
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();

    return books;
}
```

---

## 3. (Optional) Return Pagination Metadata

**Create a new model `PaginatedResponse.cs`:**

```csharp
public class PaginatedResponse<T>
{
    public IEnumerable<T> Items { get; set; }
    public int TotalItems { get; set; }
    public int PageNumber { get; set; }
    public int PageSize { get; set; }
    public int TotalPages => (int)Math.Ceiling((double)TotalItems / PageSize);

    public PaginatedResponse(IEnumerable<T> items, int totalItems, int pageNumber, int pageSize)
    {
        Items = items;
        TotalItems = totalItems;
        PageNumber = pageNumber;
        PageSize = pageSize;
    }
}
```

---

## 4. Return Paginated Response in the Controller

```csharp
[HttpGet]
public async Task<ActionResult<PaginatedResponse<Book>>> GetBooks(
    [FromQuery] string searchTerm = "",
    [FromQuery] int pageNumber = 1,
    [FromQuery] int pageSize = 10)
{
    if (pageNumber <= 0 || pageSize <= 0)
    {
        return BadRequest(new { message = "Page number and page size must be greater than zero." });
    }

    var books = await _bookRepository.GetBooks(searchTerm, pageNumber, pageSize);

    if (books == null || !books.Any())
    {
        throw new ArgumentException("No books found.");
    }

    // Get total count for metadata
    var totalItems = await _bookRepository.GetBooksCount(searchTerm);

    var response = new PaginatedResponse<Book>(books, totalItems, pageNumber, pageSize);

    return Ok(response);
}
```

**In `IBookRepository.cs`:**

```csharp
Task<int> GetBooksCount(string searchTerm);
```

**In `BookRepository.cs`:**

```csharp
public async Task<int> GetBooksCount(string searchTerm)
{
    var query = _context.Books.AsQueryable();

    if (!string.IsNullOrEmpty(searchTerm))
    {
        query = query.Where(b => b.Title.Contains(searchTerm) || b.Author.Contains(searchTerm));
    }

    return await query.CountAsync();
}
```

---

## 5. Example API Requests

1. **Get First Page of Books (Default 10 items per page):**

   ```
   GET /api/books
   ```

2. **Get Second Page with 5 Books per Page:**

   ```
   GET /api/books?pageNumber=2&pageSize=5
   ```

3. **Search for Books with "Harry" in Title or Author:**

   ```
   GET /api/books?searchTerm=Harry
   ```

4. **Search and Paginate Together:**

   ```
   GET /api/books?searchTerm=Harry&pageNumber=1&pageSize=3
   ```

---

## 6. Example Response with Pagination Metadata

```json
{
    "items": [
        {
            "id": 1,
            "title": "Harry Potter and the Sorcerer's Stone",
            "author": "J.K. Rowling"
        },
        {
            "id": 2,
            "title": "Harry Potter and the Chamber of Secrets",
            "author": "J.K. Rowling"
        }
    ],
    "totalItems": 5,
    "pageNumber": 1,
    "pageSize": 2,
    "totalPages": 3
}
```

---

## Summary of Changes

1. **Query Parameters** were added to handle `searchTerm`, `pageNumber`, and `pageSize`.
2. **Repository** methods were updated to support filtering and pagination.
3. **PaginatedResponse** class was created to return both data and pagination metadata.
4. The **controller** logic now checks for empty results and returns appropriate responses.

