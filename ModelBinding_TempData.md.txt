# Model Binding and TempData in ASP.NET MVC

## **1. Model Binding**
**Model Binding** in ASP.NET MVC is a feature that maps HTTP request data (from forms, query strings, or route values) to action method parameters or model properties automatically.

### **Basic Concept**
- When a request is made, ASP.NET extracts values from the request and assigns them to method parameters or model properties.
- This eliminates the need for manual data extraction using `Request.QueryString` or `Request.Form`.

### **Example:**
#### **Model Class (User.cs)**
```csharp
public class User
{
    public string Name { get; set; }
    public int Age { get; set; }
}
```

#### **Controller:**
```csharp
public ActionResult Submit(User user)
{
    return View(user);
}
```
#### **View (Submit.cshtml)**
```html
<form action="/Home/Submit" method="post">
    <input type="text" name="Name" placeholder="Enter Name">
    <input type="number" name="Age" placeholder="Enter Age">
    <button type="submit">Submit</button>
</form>
```
- When the form is submitted, ASP.NET automatically binds the form data to the `User` model.

---

## **2. TempData**
**TempData** is a dictionary object in ASP.NET MVC used to store temporary data between two consecutive requests. It is useful for passing data from one action to another.

### **Basic Concept**
- Data stored in `TempData` persists **only until the next request**.
- It is stored in `Session`.
- Once accessed, it is marked for deletion.

### **Example:**
#### **Setting TempData in Controller**
```csharp
public ActionResult SetTempData()
{
    TempData["Message"] = "Hello, this is TempData!";
    return RedirectToAction("GetTempData");
}
```
#### **Retrieving TempData**
```csharp
public ActionResult GetTempData()
{
    string message = TempData["Message"] as string;
    return View((object)message);
}
```
#### **View (GetTempData.cshtml)**
```html
<h2>@Model</h2>
```
- When the user is redirected, `TempData` holds the data across requests.

---

## **3. Advanced Model Binding**
Model Binding can handle:

### **1. Complex Objects**
ASP.NET can bind nested objects.
```csharp
public class Address
{
    public string City { get; set; }
}

public class User
{
    public string Name { get; set; }
    public Address Address { get; set; }
}
```
- The form should include `name="Address.City"` to bind properly.

### **2. Custom Model Binders**
You can create a **Custom Model Binder** by implementing `IModelBinder`.

---

## **4. Advanced TempData Usage**
- **Keep()**: Prevents deletion after reading.
  ```csharp
  var message = TempData["Message"];
  TempData.Keep("Message");
  ```
- **Peek()**: Reads without deletion.
  ```csharp
  string msg = TempData.Peek("Message") as string;
  ```
