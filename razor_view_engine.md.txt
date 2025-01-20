# Razor View Engine and HTML Helper Classes in ASP.NET Core

## **1. Razor View Engine**
Razor is a view engine used in ASP.NET Core MVC to generate dynamic HTML content. It allows mixing C# code with HTML using a simple and expressive syntax.

### **Key Features of Razor View Engine**
- **Lightweight and fast**: Optimized for speed and easy integration with .NET Core.
- **Clean syntax**: Avoids bulky C# code blocks inside HTML, making it readable.
- **Server-side execution**: Code runs on the server before rendering the final HTML.
- **Supports C# logic**: Allows writing C# expressions, loops, and conditions directly in the view.

---

## **2. Razor Syntax**
Razor uses the `@` symbol to differentiate C# code from HTML.

### **Example: Displaying Data in a View**
```razor
@model List<string>

<h2>User List</h2>
<ul>
    @foreach (var user in Model)
    {
        <li>@user</li>
    }
</ul>
```
- `@model List<string>` → Defines the model type passed to the view.
- `@foreach (var user in Model)` → Iterates through the model list.
- `@user` → Displays each item dynamically.

### **Example: Conditional Statement**
```razor
@if (User.IsAuthenticated)
{
    <p>Welcome, @User.Identity.Name!</p>
}
else
{
    <p>Please log in.</p>
}
```

---

## **3. HTML Helper Classes in ASP.NET Core**
HTML Helper classes simplify the process of generating HTML elements in Razor views.

### **Types of HTML Helpers**
1. **Standard HTML Helpers** (Generate simple HTML elements)
2. **Strongly Typed HTML Helpers** (Bind form fields to model properties)
3. **Templated HTML Helpers** (Auto-generate UI based on model metadata)

---

## **4. Standard HTML Helpers**
These are extension methods in Razor that generate standard HTML elements.

### **Example: Generating an Input Box**
```razor
@Html.TextBox("username", "Enter your name", new { @class = "form-control" })
```
Generates:
```html
<input type="text" name="username" value="Enter your name" class="form-control" />
```

### **Other Standard Helpers**
```razor
@Html.TextBox("email") 
@Html.Password("password") 
@Html.CheckBox("isChecked", true) 
@Html.RadioButton("gender", "Male") 
@Html.DropDownList("country", new SelectList(new[] { "India", "USA", "UK" })) 
```

---

## **5. Strongly Typed HTML Helpers**
These helpers bind HTML form fields to model properties.

### **Example: Using `Html.TextBoxFor()`**
```razor
@model UserModel

@Html.TextBoxFor(m => m.Username, new { @class = "form-control" })
```
Generates:
```html
<input type="text" name="Username" class="form-control" />
```

### **Other Strongly Typed Helpers**
```razor
@Html.TextBoxFor(m => m.Email) 
@Html.PasswordFor(m => m.Password) 
@Html.CheckBoxFor(m => m.RememberMe) 
@Html.DropDownListFor(m => m.Country, new SelectList(new[] { "India", "USA", "UK" })) 
```

---

## **6. Templated HTML Helpers**
These helpers generate UI elements based on model metadata.

### **Example: Using `Html.EditorFor()`**
```razor
@Html.EditorFor(m => m.Username)
```

### **Example: Using `Html.DisplayFor()`**
```razor
@Html.DisplayFor(m => m.Email)
```

---

## **7. Custom HTML Helpers**
You can create custom HTML helpers using extension methods.

### **Example: Creating a Custom Helper**
```csharp
public static class CustomHtmlHelpers
{
    public static IHtmlContent CustomLabel(this IHtmlHelper helper, string text)
    {
        return new HtmlString($"<label style='color:blue'>{text}</label>");
    }
}
```

### **Using Custom Helper in View**
```razor
@Html.CustomLabel("Username")
```
Renders:
```html
<label style='color:blue'>Username</label>
```

---

## **Conclusion**
- **Razor View Engine** helps mix HTML with C# code efficiently.
- **HTML Helpers** simplify form creation and user input binding.
- **Strongly Typed Helpers** are recommended for model binding.
- **Custom Helpers** allow adding reusable components.

Let me know if you need more details! 🚀
