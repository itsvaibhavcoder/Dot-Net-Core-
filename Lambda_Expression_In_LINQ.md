# **Lambda Expressions in LINQ (Language Integrated Query)**
Lambda expressions in LINQ are a powerful way to define inline functions for query expressions. They allow for concise and readable code when performing filtering, transformations, and aggregations on collections.

---

## **1. What is a Lambda Expression?**
A **lambda expression** is an anonymous function that can contain expressions and statements. It is commonly used in LINQ queries to define the logic for filtering (`Where`), selecting (`Select`), grouping (`GroupBy`), and other operations.

### **Syntax of Lambda Expression**
A lambda expression follows the below syntax:
```csharp
(input parameters) => expression
```
- `=>` is called the **lambda operator**.
- The left side of `=>` contains input parameters.
- The right side contains the expression or logic.

Example:
```csharp
x => x * x  // A lambda expression that squares a number.
```

---

## **2. Using Lambda Expressions in LINQ**
Lambda expressions are widely used in LINQ methods like `Where()`, `Select()`, `OrderBy()`, etc.

### **Example 1: Filtering a List using `Where()`**
```csharp
using System;
using System.Linq;
using System.Collections.Generic;

class Program
{
    static void Main()
    {
        List<int> numbers = new List<int> { 10, 20, 30, 40, 50 };

        // Using LINQ with Lambda to filter numbers greater than 25
        var result = numbers.Where(x => x > 25);

        foreach (var num in result)
        {
            Console.WriteLine(num);
        }
    }
}
```
**Output:**
```
30
40
50
```

---

### **Example 2: Selecting Specific Data using `Select()`**
```csharp
List<string> names = new List<string> { "Alice", "Bob", "Charlie" };

// Convert all names to uppercase using LINQ with Lambda
var upperCaseNames = names.Select(name => name.ToUpper());

foreach (var name in upperCaseNames)
{
    Console.WriteLine(name);
}
```
**Output:**
```
ALICE
BOB
CHARLIE
```

---

### **Example 3: Sorting Data using `OrderBy()` and `OrderByDescending()`**
```csharp
List<int> numbers = new List<int> { 5, 2, 8, 1, 4 };

// Ascending Order
var sortedAscending = numbers.OrderBy(x => x);
Console.WriteLine("Ascending: " + string.Join(", ", sortedAscending));

// Descending Order
var sortedDescending = numbers.OrderByDescending(x => x);
Console.WriteLine("Descending: " + string.Join(", ", sortedDescending));
```
**Output:**
```
Ascending: 1, 2, 4, 5, 8
Descending: 8, 5, 4, 2, 1
```

---

### **Example 4: Grouping Data using `GroupBy()`**
```csharp
var students = new List<string> { "Alice", "Bob", "Anna", "Charlie" };

// Group students by first letter of name
var groupedStudents = students.GroupBy(s => s[0]);

foreach (var group in groupedStudents)
{
    Console.WriteLine($"Group {group.Key}: {string.Join(", ", group)}");
}
```
**Output:**
```
Group A: Alice, Anna
Group B: Bob
Group C: Charlie
```

---

### **Example 5: Aggregation using `Sum()`, `Count()`, and `Average()`**
```csharp
List<int> numbers = new List<int> { 5, 10, 15, 20, 25 };

// Sum of all numbers
int sum = numbers.Sum(x => x);
Console.WriteLine("Sum: " + sum);

// Count of numbers greater than 10
int count = numbers.Count(x => x > 10);
Console.WriteLine("Count > 10: " + count);

// Average of numbers
double average = numbers.Average(x => x);
Console.WriteLine("Average: " + average);
```
**Output:**
```
Sum: 75
Count > 10: 3
Average: 15
```

---

## **3. Comparison: Lambda Expressions vs. Query Syntax**
LINQ supports **query syntax** and **method syntax (Lambda expressions)**.

### **Example: Using Query Syntax**
```csharp
var result = from num in numbers
             where num > 25
             select num;
```
### **Example: Using Lambda Expression**
```csharp
var result = numbers.Where(x => x > 25);
```
- Both return the same result, but lambda expressions are more concise.

---

## **4. Why Use Lambda Expressions in LINQ?**
✅ **Concise and Readable** – Shortens code compared to traditional loops.  
✅ **Flexible** – Can be used in multiple LINQ methods.  
✅ **Improves Performance** – Optimized for functional programming patterns.  

---

## **5. Conclusion**
Lambda expressions in LINQ allow for writing compact and readable queries in C#. They are extensively used in `Where()`, `Select()`, `OrderBy()`, `GroupBy()`, and aggregation functions like `Sum()` and `Count()`. Understanding them helps in efficient data manipulation and querying in C#. 🚀
