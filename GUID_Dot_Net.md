# GUID in .NET Core

A **GUID (Globally Unique Identifier)** is a **128-bit** value used to uniquely identify data across distributed systems. In **.NET Core**, the `System.Guid` structure provides methods to generate, parse, and manipulate GUIDs.

## **1. Creating a GUID**
### **Using `Guid.NewGuid()`**
```csharp
using System;

class Program
{
    static void Main()
    {
        Guid myGuid = Guid.NewGuid();
        Console.WriteLine(myGuid);
    }
}
```
✅ Generates a **random, unique GUID**.

### **Using `Guid.Parse()`**
```csharp
Guid parsedGuid = Guid.Parse("d2719db4-29b6-48d4-99c2-19b3f749bd37");
Console.WriteLine(parsedGuid);
```
✅ **Parses** a valid GUID string.

### **Using `Guid.TryParse()`**
```csharp
bool isValid = Guid.TryParse("invalid-guid", out Guid result);
Console.WriteLine(isValid);  // False
```
✅ **Safely checks** if the string is a valid GUID.

### **Creating an Empty GUID**
```csharp
Guid emptyGuid = Guid.Empty;
Console.WriteLine(emptyGuid);  // 00000000-0000-0000-0000-000000000000
```
✅ Represents a **GUID with all zeros**.

---

## **2. GUID Formats**
```csharp
Guid myGuid = Guid.NewGuid();

Console.WriteLine(myGuid.ToString("N"));  // Without hyphens
Console.WriteLine(myGuid.ToString("D"));  // Default format
Console.WriteLine(myGuid.ToString("B"));  // Braces {}
Console.WriteLine(myGuid.ToString("P"));  // Parentheses ()
Console.WriteLine(myGuid.ToString("X"));  // Hex format
```

### **Output Examples:**
```
d2719db429b648d499c219b3f749bd37       ("N")
d2719db4-29b6-48d4-99c2-19b3f749bd37   ("D")
{d2719db4-29b6-48d4-99c2-19b3f749bd37} ("B")
(d2719db4-29b6-48d4-99c2-19b3f749bd37) ("P")
{0xd2719db4,0x29b6,0x48d4,{0x99,0xc2,0x19,0xb3,0xf7,0x49,0xbd,0x37}} ("X")
```

---

## **3. Why Use GUIDs?**
✅ **Uniqueness:** Used for database keys, tracking sessions, and identifying objects globally.  
✅ **Distributed Systems:** Useful when IDs need to be unique across multiple databases and servers.  
✅ **Security:** Hard to guess, making them useful for tokens and API keys.  

---

## **4. When NOT to Use GUIDs**
❌ **Performance Issues in Databases** – GUIDs are **larger than integers**, leading to higher storage and indexing overhead.  
❌ **Sorting Issues** – Random GUIDs **do not maintain order**, which affects performance in clustered indexes.  

🔹 **Alternative:** Use **sequential GUIDs** (`NEWSEQUENTIALID()` in SQL Server) when order matters.

---

## **Conclusion**
GUIDs are a powerful tool in .NET Core for ensuring unique identifiers. However, they should be used wisely, considering storage and performance implications.
