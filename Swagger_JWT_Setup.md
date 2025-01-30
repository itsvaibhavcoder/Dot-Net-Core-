# Enabling JWT Authentication in Swagger for ASP.NET Core MVC

## **1️⃣ Install Swagger Package**
Run the following command to install Swagger in your project:

```sh
dotnet add package Swashbuckle.AspNetCore
```

---

## **2️⃣ Configure Swagger in `Startup.cs`**
Modify the `ConfigureServices` method to add JWT authentication support.

### **Modify `ConfigureServices`**
```csharp
public void ConfigureServices(IServiceCollection services)
{
    services.AddControllers();

    // Register Swagger
    services.AddSwaggerGen(c =>
    {
        c.SwaggerDoc("v1", new Microsoft.OpenApi.Models.OpenApiInfo
        {
            Title = "BookStore API",
            Version = "v1"
        });

        // Add JWT Authentication to Swagger
        c.AddSecurityDefinition("Bearer", new Microsoft.OpenApi.Models.OpenApiSecurityScheme
        {
            Description = "Enter 'Bearer' [space] and then your valid token in the text input below.",
            Name = "Authorization",
            In = Microsoft.OpenApi.Models.ParameterLocation.Header,
            Type = Microsoft.OpenApi.Models.SecuritySchemeType.ApiKey,
            Scheme = "Bearer"
        });

        c.AddSecurityRequirement(new Microsoft.OpenApi.Models.OpenApiSecurityRequirement
        {
            {
                new Microsoft.OpenApi.Models.OpenApiSecurityScheme
                {
                    Reference = new Microsoft.OpenApi.Models.OpenApiReference
                    {
                        Type = Microsoft.OpenApi.Models.ReferenceType.SecurityScheme,
                        Id = "Bearer"
                    }
                },
                new string[] {}
            }
        });
    });

    // Add Authentication
    services.AddAuthentication("Bearer")
        .AddJwtBearer("Bearer", options =>
        {
            options.RequireHttpsMetadata = false;
            options.SaveToken = true;
            options.TokenValidationParameters = new Microsoft.IdentityModel.Tokens.TokenValidationParameters
            {
                ValidateIssuerSigningKey = true,
                IssuerSigningKey = new Microsoft.IdentityModel.Tokens.SymmetricSecurityKey(
                    System.Text.Encoding.UTF8.GetBytes("YourSuperSecretKey@123")), // Use the same key as in token generation
                ValidateIssuer = false,
                ValidateAudience = false
            };
        });
}
```

---

## **3️⃣ Enable Swagger in `Configure` Method**
Modify the `Configure` method in `Startup.cs`:

### **Modify `Configure`**
```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();
    app.UseRouting();

    app.UseAuthentication();  // Ensure authentication middleware is added before authorization
    app.UseAuthorization();

    // Enable Swagger
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "BookStore API V1");
        c.RoutePrefix = string.Empty; // Access Swagger at the root URL (e.g., https://localhost:5001/)
    });

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

---

## **4️⃣ Run & Test in Swagger UI**
1. **Run your API**
   ```sh
   dotnet run
   ```
2. **Open Swagger UI**
   - Visit: **`https://localhost:5001/swagger`**
   - You should see a **lock icon 🔒 or "Authorize" button** in the top right.

3. **Authorize with Token**
   - Click **"Authorize"** (🔑).
   - Enter `Bearer <your_token_here>` (e.g., `Bearer eyJhbGciOiJIUzI1...`).
   - Click **"Authorize"** and then close the window.

4. **Make Authenticated Requests**
   - Now, when calling a **protected endpoint** (e.g., `GET /api/cart`), the token will be automatically included.

---

## **🎯 Summary**
✔ **Configured Swagger to accept JWT tokens.**  
✔ **Added JWT authentication middleware.**  
✔ **Enabled authentication for Swagger UI.**  
✔ **Tested using the "Authorize" button in Swagger.** 🚀

Let me know if you need any more help! 🔥
