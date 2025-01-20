# GET vs POST in .NET Core 3

## **Comparison Table**
| Feature      | GET | POST |
|-------------|-----|------|
| **Purpose** | Retrieve data from the server | Send data to the server (e.g., create or update resources) |
| **Data in Request** | Sent as a **query string** in the URL (`?key=value`) | Sent in the **request body** |
| **Security** | Less secure (query strings can be stored in browser history) | More secure (data is in the request body) |
| **Caching** | Can be cached | Usually not cached |
| **Idempotency** | Idempotent (same request gives the same response) | Not idempotent (same request can create multiple records) |
| **Request Size** | Limited by URL length (~2048 characters in some browsers) | Can send large data (like JSON, files, or binary data) |
| **Used for** | Fetching data (e.g., retrieving user details) | Creating, updating, or deleting resources |

---

## **Implementation in .NET Core 3**
In **ASP.NET Core 3**, you define `GET` and `POST` methods inside a controller.

### **1. GET Method**
This method retrieves data from the server. You use `[HttpGet]` in the controller.

#### **Example: Fetching Users**
```csharp
[Route("api/[controller]")]
[ApiController]
public class UsersController : ControllerBase
{
    [HttpGet]
    public IActionResult GetUsers()
    {
        var users = new List<string> { "John", "Alice", "Bob" };
        return Ok(users); // Returns 200 OK response with user list
    }
    
    [HttpGet("{id}")]
    public IActionResult GetUserById(int id)
    {
        var user = new { Id = id, Name = "John Doe" };
        return Ok(user);
    }
}
```
**Usage:**
- `GET /api/users` → Returns all users.
- `GET /api/users/1` → Returns user with ID `1`.

---

### **2. POST Method**
This method sends data to the server to create a new resource. You use `[HttpPost]` in the controller.

#### **Example: Creating a User**
```csharp
[HttpPost]
public IActionResult CreateUser([FromBody] UserModel user)
{
    if (user == null)
    {
        return BadRequest("Invalid data");
    }
    return CreatedAtAction(nameof(GetUserById), new { id = user.Id }, user);
}
```
**Usage:**
- `POST /api/users` with JSON body:
```json
{
    "id": 1,
    "name": "John Doe"
}
```
- Response: `201 Created`

---

## **Summary**
- Use **GET** for retrieving data.
- Use **POST** for sending data (creating or updating).
- **GET** is safer for read operations, while **POST** is better for data modifications.
- **GET** parameters are sent in the URL, while **POST** sends data in the body.

---
Let me know if you need more details! 🚀
