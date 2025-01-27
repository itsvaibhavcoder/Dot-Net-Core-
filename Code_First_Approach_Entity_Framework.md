# Code-First Approach in Entity Framework

The **Code-First Approach** in Entity Framework (EF) allows developers to define the database schema using C# or VB.NET classes instead of designing it in a database first. The EF framework then generates the database tables based on these classes.

## Why Use Code-First?
- **Control Over Database Schema**: Developers can define models directly in code.
- **Version Control**: Since the schema is in the code, it can be tracked using Git or other version control systems.
- **Easier Maintenance**: Changes to the schema can be handled through migrations without manually altering the database.

---

## Steps in Code-First Approach

### 1. Install Entity Framework
Before using EF Code-First, ensure you have the necessary NuGet package installed:

```sh
Install-Package Microsoft.EntityFrameworkCore.SqlServer
```

### 2. Create a Model (POCO Class)
A **Plain Old CLR Object (POCO)** class represents a table in the database.

```csharp
public class Student
{
    public int Id { get; set; }  // Primary Key
    public string Name { get; set; }
    public int Age { get; set; }
}
```

> **Note**: EF automatically detects `Id` as the primary key.

---

### 3. Create a DbContext Class
The `DbContext` class is used to interact with the database.

```csharp
using Microsoft.EntityFrameworkCore;

public class SchoolContext : DbContext
{
    public DbSet<Student> Students { get; set; }  // Table

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=.;Database=SchoolDB;Trusted_Connection=True;");
    }
}
```

---

### 4. Enable Migrations
To create the database based on our models, we use EF Migrations.

#### Step 1: Enable Migrations
Run the following command in the **Package Manager Console (PMC)**:

```sh
Add-Migration InitialCreate
```
This creates a **Migrations folder** with files defining the schema.

#### Step 2: Apply Migrations to the Database
```sh
Update-Database
```
This creates the actual database in SQL Server.

---

### 5. CRUD Operations Using Code-First

#### Create a New Student Record
```csharp
using (var context = new SchoolContext())
{
    var student = new Student { Name = "John Doe", Age = 20 };
    context.Students.Add(student);
    context.SaveChanges();
}
```

#### Read Data
```csharp
using (var context = new SchoolContext())
{
    var students = context.Students.ToList();
    foreach (var student in students)
    {
        Console.WriteLine($"ID: {student.Id}, Name: {student.Name}, Age: {student.Age}");
    }
}
```

#### Update Data
```csharp
using (var context = new SchoolContext())
{
    var student = context.Students.FirstOrDefault(s => s.Id == 1);
    if (student != null)
    {
        student.Age = 21;
        context.SaveChanges();
    }
}
```

#### Delete Data
```csharp
using (var context = new SchoolContext())
{
    var student = context.Students.FirstOrDefault(s => s.Id == 1);
    if (student != null)
    {
        context.Students.Remove(student);
        context.SaveChanges();
    }
}
```

---

## Handling Database Changes (Migrations)
When you modify the model, you need to update the database.

### Step 1: Modify the Model
Let's say we add a new column `Email` to the `Student` class:

```csharp
public class Student
{
    public int Id { get; set; }
    public string Name { get; set; }
    public int Age { get; set; }
    public string Email { get; set; }  // New field
}
```

### Step 2: Create a New Migration
```sh
Add-Migration AddEmailToStudent
```

### Step 3: Apply Changes
```sh
Update-Database
```
This updates the database without losing existing data.

---

## Advantages of Code-First Approach
1. **No Need for a Pre-Existing Database**: The database is generated from the code.
2. **Migrations Handle Schema Changes**: Allows easy updates without manually modifying the database.
3. **Strongly Typed Models**: Ensures type safety and better maintainability.
4. **Integration with ORM Features**: Supports relationships, validation, and lazy loading.

---

## When to Use Code-First Approach?
- When developing new applications from scratch.
- When you prefer managing the database schema via code.
- When working in agile development where schema changes frequently.

---

## When Not to Use Code-First?
- If you're working with an existing database (`Database-First` might be better).
- If the DBA (Database Administrator) prefers managing the schema manually.

---

## Conclusion
The **Code-First Approach in Entity Framework** provides a flexible way to define and manage database structures using C# classes. It simplifies database versioning using migrations and supports easy CRUD operations.
