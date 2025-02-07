# **LINQ to Entities Queries in Entity Framework**

## **1. What is LINQ to Entities?**
LINQ to Entities is a part of **Entity Framework (EF)** that allows developers to query databases using **Language-Integrated Query (LINQ)**. It enables writing **strongly-typed, readable, and efficient queries** using C# or VB.NET instead of raw SQL.

### **How It Works?**
1. A **LINQ query** is written using C# or VB.NET.
2. The Entity Framework translates it into an **SQL query**.
3. The SQL query executes on the **database server**.
4. The results are mapped back into **C# objects**.

---

## **2. Writing LINQ to Entities Queries**
LINQ queries in Entity Framework can be written using two styles:
1. **Query Syntax** (Similar to SQL)
2. **Method Syntax** (Using lambda expressions)

### **2.1 Query Syntax Example**
```csharp
var employees = from e in context.Employees
                where e.Salary > 50000
                select e;
```

### **2.2 Method Syntax Example**
```csharp
var employees = context.Employees
                       .Where(e => e.Salary > 50000)
                       .ToList();
```

---

## **3. Common LINQ to Entities Queries**

### **3.1 Retrieving Data (SELECT Query)**
#### **Example 1: Fetch All Records**
```csharp
var employees = context.Employees.ToList();
```
#### **Example 2: Fetch Specific Columns**
```csharp
var employeeNames = context.Employees
                           .Select(e => new { e.Name, e.Salary })
                           .ToList();
```

### **3.2 Filtering Data (WHERE Clause)**
#### **Example 1: Filter Employees by Department**
```csharp
var employees = context.Employees
                       .Where(e => e.Department == "IT")
                       .ToList();
```
#### **Example 2: Filter Using Multiple Conditions**
```csharp
var employees = context.Employees
                       .Where(e => e.Department == "IT" && e.Salary > 50000)
                       .ToList();
```

### **3.3 Sorting Data (ORDER BY Clause)**
#### **Example 1: Sorting in Ascending Order**
```csharp
var employees = context.Employees
                       .OrderBy(e => e.Name)
                       .ToList();
```
#### **Example 2: Sorting in Descending Order**
```csharp
var employees = context.Employees
                       .OrderByDescending(e => e.Salary)
                       .ToList();
```

### **3.4 Grouping Data (GROUP BY Clause)**
#### **Example 1: Group Employees by Department**
```csharp
var employeesByDept = context.Employees
                             .GroupBy(e => e.Department)
                             .Select(g => new { Department = g.Key, Count = g.Count() })
                             .ToList();
```

### **3.5 Joining Tables (INNER JOIN, LEFT JOIN)**
#### **Example 1: Inner Join Employees with Departments**
```csharp
var employeesWithDepartments = context.Employees
    .Join(context.Departments,
          e => e.DepartmentId,
          d => d.Id,
          (e, d) => new { e.Name, d.DepartmentName })
    .ToList();
```

#### **Example 2: Left Join**
```csharp
var employeesWithDepartments = from e in context.Employees
                               join d in context.Departments
                               on e.DepartmentId equals d.Id into deptGroup
                               from d in deptGroup.DefaultIfEmpty()
                               select new { e.Name, Department = d != null ? d.DepartmentName : "No Department" };
```

### **3.6 Aggregation Queries (SUM, COUNT, AVG, MIN, MAX)**
#### **Example 1: Count Employees in a Department**
```csharp
var employeeCount = context.Employees.Count(e => e.Department == "IT");
```
#### **Example 2: Calculate Average Salary**
```csharp
var avgSalary = context.Employees.Average(e => e.Salary);
```
#### **Example 3: Find Maximum Salary**
```csharp
var maxSalary = context.Employees.Max(e => e.Salary);
```

### **3.7 Fetching First/Last Element**
#### **Example 1: Fetch First Employee**
```csharp
var firstEmployee = context.Employees.FirstOrDefault();
```
#### **Example 2: Fetch Last Employee**
```csharp
var lastEmployee = context.Employees.OrderByDescending(e => e.Id).FirstOrDefault();
```

---

## **4. Updating Data in LINQ to Entities**
### **4.1 Updating an Existing Record**
```csharp
var employee = context.Employees.FirstOrDefault(e => e.Id == 1);
if (employee != null)
{
    employee.Salary = 60000;
    context.SaveChanges();
}
```

---

## **5. Deleting Data in LINQ to Entities**
### **5.1 Deleting a Record**
```csharp
var employee = context.Employees.FirstOrDefault(e => e.Id == 1);
if (employee != null)
{
    context.Employees.Remove(employee);
    context.SaveChanges();
}
```

---

## **6. Executing Raw SQL Queries**
### **Example: Raw SQL Query Execution**
```csharp
var employees = context.Employees.FromSqlRaw("SELECT * FROM Employees WHERE Salary > 50000").ToList();
```

---

## **7. Performance Optimization Tips**
1. **Use `AsNoTracking()`** for read-only queries to improve performance.
   ```csharp
   var employees = context.Employees.AsNoTracking().ToList();
   ```
2. **Use `.ToList()` Only When Necessary** to avoid unnecessary memory consumption.
3. **Optimize Joins and Projections** to fetch only required data.
4. **Use `FirstOrDefault()` Instead of `Where()` and `ToList()` When Fetching a Single Record.**

---

## **8. Summary**
- **LINQ to Entities** allows querying databases using **C# LINQ syntax**.
- It supports **query syntax** and **method syntax**.
- **Common operations include**: filtering, sorting, grouping, joining, aggregations, updates, and deletions.
- **Performance optimizations** like `AsNoTracking()` can improve query execution.
