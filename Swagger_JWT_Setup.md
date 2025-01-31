# Authentication and Authorization Using JWT in .NET Core 3.1

## Introduction
In modern web applications, authentication and authorization are crucial for security. **Authentication** is verifying who a user is, while **authorization** determines what actions they are allowed to perform.

In .NET Core 3.1, **JWT (JSON Web Token)** is commonly used for secure authentication and authorization. **Swagger** is used for API documentation and testing.

---

## 1. JWT Authentication in .NET Core 3.1
### 1.1 What is JWT?
JWT is a secure token-based authentication mechanism that allows users to authenticate and securely access resources. It consists of three parts:
- **Header** – Algorithm and token type
- **Payload** – User claims
- **Signature** – Ensures the token is untampered

A JWT token looks like:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9
.eyJzdWIiOiJ1c2VyMSIsImp0aSI6IjEyMyIsImV4cCI6MTY5MjEyMzAwMH0
.cSxiMlOtxVVk-T82bLxXzLaE4ZhHhD2PMeLkW6tQ9Mw
```

---

## 2. Implementing JWT Authentication in .NET Core 3.1
### 2.1 Install Required Packages
```powershell
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
```

### 2.2 Configure JWT in `appsettings.json`
```json
{
  "JwtSettings": {
    "Key": "YourSecretKeyHere",
    "Issuer": "YourIssuer",
    "Audience": "YourAudience"
  }
}
```

### 2.3 Configure JWT in `Startup.cs`
```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.IdentityModel.Tokens;
using System.Text;

public void ConfigureServices(IServiceCollection services)
{
    var key = Encoding.ASCII.GetBytes(Configuration["JwtSettings:Key"]);
    
    services.AddAuthentication(x =>
    {
        x.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
        x.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
    })
    .AddJwtBearer(x =>
    {
        x.RequireHttpsMetadata = false;
        x.SaveToken = true;
        x.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuerSigningKey = true,
            IssuerSigningKey = new SymmetricSecurityKey(key),
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidIssuer = Configuration["JwtSettings:Issuer"],
            ValidAudience = Configuration["JwtSettings:Audience"]
        };
    });

    services.AddControllers();
}
```

### 2.4 Generate JWT Token
```csharp
public class TokenService
{
    private readonly IConfiguration _config;
    
    public TokenService(IConfiguration config)
    {
        _config = config;
    }

    public string GenerateToken(string username)
    {
        var tokenHandler = new JwtSecurityTokenHandler();
        var key = Encoding.ASCII.GetBytes(_config["JwtSettings:Key"]);

        var tokenDescriptor = new SecurityTokenDescriptor
        {
            Subject = new ClaimsIdentity(new Claim[]
            {
                new Claim(ClaimTypes.Name, username),
                new Claim(ClaimTypes.Role, "Admin")
            }),
            Expires = DateTime.UtcNow.AddHours(1),
            Issuer = _config["JwtSettings:Issuer"],
            Audience = _config["JwtSettings:Audience"],
            SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(key), SecurityAlgorithms.HmacSha256Signature)
        };

        var token = tokenHandler.CreateToken(tokenDescriptor);
        return tokenHandler.WriteToken(token);
    }
}
```

### 2.5 Login API Endpoint
```csharp
[Route("api/auth")]
[ApiController]
public class AuthController : ControllerBase
{
    private readonly TokenService _tokenService;

    public AuthController(TokenService tokenService)
    {
        _tokenService = tokenService;
    }

    [HttpPost("login")]
    public IActionResult Login([FromBody] UserLoginModel model)
    {
        if (model.Username == "admin" && model.Password == "password")
        {
            var token = _tokenService.GenerateToken(model.Username);
            return Ok(new { Token = token });
        }
        return Unauthorized();
    }
}
```

### 2.6 Protecting API Endpoints
```csharp
[Authorize]
[HttpGet("secure-data")]
public IActionResult SecureData()
{
    return Ok(new { Message = "This is a protected API." });
}
```

---

## 3. Implementing Swagger in .NET Core 3.1
### 3.1 Install Swagger NuGet Package
```powershell
dotnet add package Swashbuckle.AspNetCore
```

### 3.2 Configure Swagger in `Startup.cs`
```csharp
using Microsoft.OpenApi.Models;

public void ConfigureServices(IServiceCollection services)
{
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new OpenApiInfo { Title = "JWT API", Version = "v1" });

        c.AddSecurityDefinition("Bearer", new OpenApiSecurityScheme
        {
            Description = "JWT Authorization header using the Bearer scheme. Example: 'Bearer {token}'",
            Name = "Authorization",
            In = ParameterLocation.Header,
            Type = SecuritySchemeType.ApiKey
        });

        c.AddSecurityRequirement(new OpenApiSecurityRequirement
        {
            {
                new OpenApiSecurityScheme
                {
                    Reference = new OpenApiReference
                    {
                        Type = ReferenceType.SecurityScheme,
                        Id = "Bearer"
                    }
                },
                new string[] {}
            }
        });
    });
    services.AddControllers();
}
```

### 3.3 Enable Swagger in `Configure` Method
```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "JWT API V1");
    });

    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

### 3.4 Testing JWT in Swagger
1. Run the application.
2. Open Swagger UI at `https://localhost:5001/swagger`
3. Click **Authorize** and enter `Bearer <your-token>`
4. Call secured APIs.

---

## Conclusion
- **JWT Authentication** allows users to securely authenticate and access APIs.
- **Authorization** is controlled using roles and claims.
- **Swagger** provides API documentation and testing capabilities.
- **.NET Core 3.1** supports easy integration of JWT and Swagger.
