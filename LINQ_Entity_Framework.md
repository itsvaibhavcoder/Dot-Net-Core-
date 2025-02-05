# LINQ (Language Integrated Query) in C#

**LINQ (Language Integrated Query)** is a powerful feature in C# that allows querying collections in a declarative and readable way. It bridges the gap between programming languages and data, enabling developers to write queries directly in C# for objects, databases, XML, and more.

---

## **1. Basics of LINQ**

### **1.1 What is LINQ?**
LINQ is a set of features in C# that allow you to query data from different data sources using a unified syntax. It works with:
- Collections (`List`, `Array`, etc.)
- Databases (via LINQ to SQL, Entity Framework)
- XML (`LINQ to XML`)
- DataSets (`LINQ to DataSet`)

---

### **1.2 Types of LINQ Queries**
1. **Query Syntax** (SQL-like)
2. **Method Syntax** (Fluent API)

Both styles achieve the same results, and you can mix them if needed.

---

### **1.3 Setting Up LINQ**
Make sure to include the `System.Linq` namespace:
```csharp
using System;
using System.Linq;
using System.Collections.Generic;
```

---

## **2. LINQ Query Syntax**

### **2.1 Basic Query Syntax Example**
```csharp
List<int> numbers = new List<int> { 1, 2, 3, 4, 5, 6 };

// LINQ Query Syntax
var evenNumbers = from num in numbers
                  where num % 2 == 0
                  select num;

foreach (var number in evenNumbers)
{
    Console.WriteLine(number); // Output: 2, 4, 6
}
```

**Explanation:**
- `from num in numbers`: Iterates over the collection.
- `where num % 2 == 0`: Filters for even numbers.
- `select num`: Selects the filtered numbers.

---

### **2.2 Method Syntax Example**
```csharp
var evenNumbers = numbers.Where(num => num % 2 == 0);

foreach (var number in evenNumbers)
{
    Console.WriteLine(number); // Output: 2, 4, 6
}
```

---

## **3. Common LINQ Operators**

### **3.1 Filtering**
- **`Where`**: Filters elements based on a condition.
  ```csharp
  var even = numbers.Where(n => n % 2 == 0);
  ```

### **3.2 Projection**
- **`Select`**: Projects each element into a new form.
  ```csharp
  var squares = numbers.Select(n => n * n);  // Output: 1, 4, 9, 16, 25, 36
  ```

### **3.3 Sorting**
- **`OrderBy` / `OrderByDescending`**: Sorts elements.
  ```csharp
  var sorted = numbers.OrderBy(n => n);  // Ascending order
  ```

### **3.4 Aggregation**
- **`Sum`, `Count`, `Average`, `Min`, `Max`**
  ```csharp
  var sum = numbers.Sum();       // 21
  var count = numbers.Count();   // 6
  var average = numbers.Average(); // 3.5
  ```

### **3.5 Grouping**
- **`GroupBy`**: Groups elements based on a key.
  ```csharp
  var grouped = numbers.GroupBy(n => n % 2 == 0 ? "Even" : "Odd");
  ```

---

## **4. Advanced LINQ Concepts**

### **4.1 Joining Collections**
- **Inner Join**: Combines two collections based on a matching key.
  ```csharp
  var students = new[] {
      new { Id = 1, Name = "Alice" },
      new { Id = 2, Name = "Bob" }
  };

  var scores = new[] {
      new { StudentId = 1, Score = 90 },
      new { StudentId = 2, Score = 85 }
  };

  var query = from student in students
              join score in scores on student.Id equals score.StudentId
              select new { student.Name, score.Score };

  foreach (var result in query)
  {
      Console.WriteLine($"{result.Name}: {result.Score}");
  }
  ```

---

### **4.2 Grouping and Aggregation**
```csharp
var students = new[] {
    new { Name = "Alice", Grade = "A" },
    new { Name = "Bob", Grade = "B" },
    new { Name = "Charlie", Grade = "A" },
};

var grouped = from student in students
              group student by student.Grade into gradeGroup
              select new { Grade = gradeGroup.Key, Count = gradeGroup.Count() };

foreach (var group in grouped)
{
    Console.WriteLine($"Grade: {group.Grade}, Count: {group.Count}");
}
```

---

### **4.3 Deferred Execution**
LINQ queries are **not executed immediately**. They are executed when you iterate over the result (like in a `foreach` loop). This is known as **deferred execution**.

Example:
```csharp
var numbers = new List<int> { 1, 2, 3, 4, 5 };
var query = numbers.Where(n => n > 3); // Query defined, not executed yet

numbers.Add(6);  // Adding more data before execution

foreach (var number in query) // Now the query is executed
{
    Console.WriteLine(number); // Output: 4, 5, 6
}
```

---

### **4.4 Immediate Execution**
Use methods like `ToList()`, `ToArray()`, or `Count()` to **force immediate execution**.

```csharp
var query = numbers.Where(n => n > 3).ToList();  // Executed immediately
numbers.Add(7);  // This won't affect the result

foreach (var number in query)
{
    Console.WriteLine(number);  // Output: 4, 5, 6
}
```

---

## **5. LINQ with Complex Objects**

### **5.1 Querying Custom Objects**
```csharp
class Student
{
    public string Name { get; set; }
    public int Age { get; set; }
}

var students = new List<Student>
{
    new Student { Name = "Alice", Age = 20 },
    new Student { Name = "Bob", Age = 22 },
    new Student { Name = "Charlie", Age = 18 }
};

// Select students older than 20
var olderStudents = from student in students
                    where student.Age > 20
                    select student;

foreach (var student in olderStudents)
{
    Console.WriteLine(student.Name);  // Output: Bob
}
```

---

## **6. LINQ with Databases (LINQ to SQL/Entity Framework)**

### **6.1 LINQ to SQL Example**
When using **Entity Framework**, LINQ queries can directly interact with databases.

```csharp
using (var context = new SchoolContext())
{
    var students = from s in context.Students
                   where s.Age > 18
                   select s;

    foreach (var student in students)
    {
        Console.WriteLine(student.Name);
    }
}
```

---

## **7. LINQ to XML**

### **7.1 Querying XML with LINQ**
```csharp
XDocument xml = XDocument.Parse(@"
    <Students>
        <Student Name='Alice' Age='20' />
        <Student Name='Bob' Age='22' />
    </Students>");

var query = from student in xml.Descendants("Student")
            where (int)student.Attribute("Age") > 20
            select student.Attribute("Name").Value;

foreach (var name in query)
{
    Console.WriteLine(name);  // Output: Bob
}
```

---

## **8. LINQ Extension Methods**

LINQ is based on **extension methods** in C#. You can even create your own LINQ-style methods.

```csharp
public static class MyExtensions
{
    public static IEnumerable<int> MultiplyBy(this IEnumerable<int> source, int factor)
    {
        foreach (var num in source)
        {
            yield return num * factor;
        }
    }
}

var numbers = new List<int> { 1, 2, 3 };
var result = numbers.MultiplyBy(5);

foreach (var num in result)
{
    Console.WriteLine(num);  // Output: 5, 10, 15
}
```

---

## **9. LINQ Best Practices**

1. **Use `var` with LINQ**: Keeps code clean and avoids unnecessary type declarations.
2. **Chain methods**: Make use of method chaining for readability.
3. **Be mindful of performance**:
   - Use **immediate execution** when necessary to avoid multiple iterations.
   - Optimize queries, especially when querying large datasets or databases.
4. **Use descriptive names**: Make your queries self-explanatory.

---

## **Conclusion**

LINQ simplifies querying and manipulating data in C#. From basic filtering to advanced joins and grouping, LINQ provides a clean, readable, and powerful way to interact with data across multiple sources. Mastering both **query** and **method syntax** will give you flexibility when working with collections, databases, XML, and more.

