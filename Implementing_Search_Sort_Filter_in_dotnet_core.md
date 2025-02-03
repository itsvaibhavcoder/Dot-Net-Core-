
# Implementing Search, Sort, and Filter in .NET Core Web API Using Entity Framework

This guide explains how to implement searching, sorting, and filtering in a .NET Core Web API using Entity Framework.

## 1. Set Up Your Entity Framework Core Model

First, ensure you have an Entity Framework Core model (usually representing a database table). For example, let’s assume we have a `Product` model.

```csharp
public class Product
{
    public int Id { get; set; }
    public string Name { get; set; }
    public decimal Price { get; set; }
    public string Category { get; set; }
}
```

## 2. Create the DbContext

Create a `DbContext` class to interact with the database.

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }
    
    public AppDbContext(DbContextOptions<AppDbContext> options) : base(options)
    {
    }
}
```

## 3. Create a DTO for Sorting, Filtering, and Searching

To implement search, sort, and filter functionalities, it's common to create a DTO (Data Transfer Object) that defines the user inputs for these actions.

```csharp
public class ProductQueryParameters
{
    public string SearchTerm { get; set; }
    public string SortBy { get; set; }
    public string SortOrder { get; set; } = "asc"; // Default to ascending
    public decimal? MinPrice { get; set; }
    public decimal? MaxPrice { get; set; }
    public string Category { get; set; }
}
```

## 4. Implement Search, Sort, and Filter in the Controller

Now, implement search, sorting, and filtering in your API controller. Below is an example of how you can implement these features.

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductsController : ControllerBase
{
    private readonly AppDbContext _context;

    public ProductsController(AppDbContext context)
    {
        _context = context;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Product>>> GetProducts([FromQuery] ProductQueryParameters queryParams)
    {
        IQueryable<Product> query = _context.Products;

        // Apply searching
        if (!string.IsNullOrEmpty(queryParams.SearchTerm))
        {
            query = query.Where(p => p.Name.Contains(queryParams.SearchTerm) || p.Category.Contains(queryParams.SearchTerm));
        }

        // Apply filtering by price range
        if (queryParams.MinPrice.HasValue)
        {
            query = query.Where(p => p.Price >= queryParams.MinPrice.Value);
        }
        if (queryParams.MaxPrice.HasValue)
        {
            query = query.Where(p => p.Price <= queryParams.MaxPrice.Value);
        }

        // Apply filtering by category
        if (!string.IsNullOrEmpty(queryParams.Category))
        {
            query = query.Where(p => p.Category == queryParams.Category);
        }

        // Apply sorting
        if (!string.IsNullOrEmpty(queryParams.SortBy))
        {
            if (queryParams.SortOrder.ToLower() == "desc")
            {
                query = query.OrderByDescending(p => EF.Property<object>(p, queryParams.SortBy));
            }
            else
            {
                query = query.OrderBy(p => EF.Property<object>(p, queryParams.SortBy));
            }
        }

        return await query.ToListAsync();
    }
}
```

### Explanation of the Controller Logic:
- **Search**: We use the `Contains` method to search within the `Name` and `Category` fields.
- **Filtering**: We filter products by `MinPrice`, `MaxPrice`, and `Category`.
- **Sorting**: We dynamically sort by a column defined in the `SortBy` query parameter, using `EF.Property<object>` to refer to the property. The `SortOrder` can either be `asc` or `desc`.

## 5. Example Request

To search for products with the term "laptop", sort by price, and filter by price range:

```
GET /api/products?searchTerm=laptop&sortBy=Price&sortOrder=asc&minPrice=500&maxPrice=2000
```

## 6. Optional: Paginate the Results

To optimize large datasets, you can paginate the results. Here’s how you can implement pagination in the same controller method:

```csharp
public async Task<ActionResult<IEnumerable<Product>>> GetProducts([FromQuery] ProductQueryParameters queryParams)
{
    IQueryable<Product> query = _context.Products;

    // Apply search, filtering, and sorting (same as above)
    // ...

    // Pagination
    int pageNumber = queryParams.PageNumber ?? 1;
    int pageSize = queryParams.PageSize ?? 10;

    var pagedProducts = await query.Skip((pageNumber - 1) * pageSize).Take(pageSize).ToListAsync();

    return Ok(pagedProducts);
}
```

## 7. Testing and Validation

Test your implementation with various query parameters to ensure the search, sort, and filter features are working correctly.

## 8. Front-End Interaction

On the client side, you can pass these parameters in the query string when making requests to the Web API.
