# **JWT Token Handler Explanation**

This document explains the functionality of the **TokenHandler** class, which is responsible for creating JSON Web Tokens (JWTs) for authenticated users in an ASP.NET Core API.

---

## **1. Namespaces Imported**

```csharp
using Microsoft.IdentityModel.Tokens;
using NZWalks.API.Models.Domain;
using System.IdentityModel.Tokens.Jwt;
using System.Security.Claims;
using System.Text;
```

- **Microsoft.IdentityModel.Tokens**: Provides classes for handling tokens, encryption, and signing credentials.
- **NZWalks.API.Models.Domain**: Refers to the domain models of your project. Likely includes the `User` model.
- **System.IdentityModel.Tokens.Jwt**: Provides classes for creating and handling JWT tokens.
- **System.Security.Claims**: Manages claims-based identities, which are key-value pairs containing user data (e.g., roles, names).
- **System.Text**: Provides functionality for text encoding and manipulation (used here for encoding the secret key).

---

## **2. Namespace and Class Declaration**

```csharp
namespace NZWalks.API.Repositories
{
    public class TokenHandler : ITokenHandler
```

- The **TokenHandler** class is placed under the `NZWalks.API.Repositories` namespace, suggesting it is part of the data access or service layer.
- It **implements the `ITokenHandler` interface**, which likely defines the contract for creating tokens.

---

## **3. Constructor and Dependency Injection**

```csharp
private readonly IConfiguration configuration;

public TokenHandler(IConfiguration configuration)
{
    this.configuration = configuration;
}
```

- The class uses **Dependency Injection** to receive an instance of `IConfiguration`, which allows access to the app's configuration settings (like secrets stored in `appsettings.json`).
- The configuration is stored in a **readonly field** named `configuration`, which will be used to fetch JWT-related settings like the secret key, issuer, and audience.

---

## **4. The `CreateTokenAsync` Method**

This method generates a JWT for a given `User` object.

```csharp
public Task<string> CreateTokenAsync(User user)
```

- **Asynchronous Method**: Although no actual asynchronous operations are used, the method signature returns a `Task<string>`, making it compatible with async workflows.
- **Input**: Takes a `User` object, which likely has properties like `FirstName`, `LastName`, `EmailAddress`, and `Roles`.

---

## **5. Creating Claims**

Claims are key-value pairs embedded in the token that represent user information and roles.

```csharp
var claims = new List<Claim>();
claims.Add(new Claim(ClaimTypes.GivenName, user.FirstName));
claims.Add(new Claim(ClaimTypes.Surname, user.LastName));
claims.Add(new Claim(ClaimTypes.Email, user.EmailAddress));
```

- **ClaimTypes.GivenName**: Stores the user's first name.
- **ClaimTypes.Surname**: Stores the user's last name.
- **ClaimTypes.Email**: Stores the user's email address.

These claims allow you to include user-specific data in the token.

---

## **6. Adding Roles to Claims**

Roles define what permissions a user has in the system.

```csharp
user.Roles.ForEach((role) =>
{
    claims.Add(new Claim(ClaimTypes.Role, role));
});
```

- **user.Roles**: Assumes `Roles` is a list of strings representing the user's roles (e.g., "Admin", "User").
- For each role, a new claim of type `ClaimTypes.Role` is added. This allows role-based authorization checks in your application.

---

## **7. Creating the Security Key and Signing Credentials**

JWTs are signed to ensure they haven't been tampered with.

```csharp
var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(configuration["Jwt:Key"]));
var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);
```

- **SymmetricSecurityKey**: Converts the secret key from the configuration into a byte array and creates a security key.
- **Encoding.UTF8.GetBytes**: Converts the secret key string into bytes (required for cryptographic operations).
- **SigningCredentials**: Uses the key and specifies the **HMAC SHA-256** algorithm to sign the token. This ensures the token's integrity.

> Example of `appsettings.json` configuration:
```json
"Jwt": {
  "Key": "your-secret-key",
  "Issuer": "your-app-name",
  "Audience": "your-app-audience"
}
```

---

## **8. Generating the JWT**

```csharp
var token = new JwtSecurityToken(
    configuration["Jwt:Issuer"],
    configuration["Jwt:Audience"],
    claims,
    expires: DateTime.Now.AddMinutes(15),
    signingCredentials: credentials);
```

- **Issuer**: Identifies who issued the token (your app/service).
- **Audience**: Identifies who the token is intended for (e.g., your API clients).
- **Claims**: The user data (first name, last name, email, roles) added earlier.
- **expires**: Sets the token's expiration time to 15 minutes from the current time.
- **signingCredentials**: Specifies how the token is signed (using the secret key and HMAC SHA-256).

This creates the token with all the necessary information and security settings.

---

## **9. Returning the Token**

```csharp
return Task.FromResult(new JwtSecurityTokenHandler().WriteToken(token));
```

- **JwtSecurityTokenHandler().WriteToken(token)**: Serializes the JWT into a string format, which can be sent to the client.
- **Task.FromResult()**: Wraps the result in a `Task` to make the method asynchronous.

---

## **Summary of JWT Workflow**

1. **User Logs In**: The client sends login credentials to the server.
2. **CreateTokenAsync is Called**: If credentials are valid, `CreateTokenAsync` is invoked to generate a JWT for the user.
3. **Claims are Added**: User details and roles are added to the token.
4. **Token is Signed**: The token is signed using the secret key to ensure its integrity.
5. **Token is Returned**: The serialized token is sent to the client.
6. **Client Uses Token**: The client includes the token in the `Authorization` header for subsequent API requests.
7. **Token is Verified**: The server verifies the token's signature and claims to authorize access.

---

## **Example JWT Structure**

A JWT has three parts:
1. **Header**: Contains the signing algorithm.
2. **Payload**: Contains claims like user data and roles.
3. **Signature**: Ensures the token hasn’t been tampered with.

Example of a JWT:
```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9. // Header
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwicm9sZSI6IkFkbWluIn0. // Payload
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c // Signature
```

---

Let me know if you need further explanation or want to see how to integrate this token handler with login functionality! 🚀

