# Migration Script in Entity Framework

## 1. What is a Migration Script?
A **migration script** in **Entity Framework** is a way to manage database schema changes programmatically. It allows updating the database schema without losing existing data, keeping it in sync with the application models.

## 2. Enabling Migrations

Before creating a migration, ensure **Entity Framework Core** is installed in your project.

### **For EF Core**
Run this command in the **Package Manager Console (PMC):**
```sh
Add-Migration InitialCreate
```
or using **.NET CLI**:
```sh
dotnet ef migrations add InitialCreate
```

### **For EF 6 (Older Version)**
Use:
```sh
Enable-Migrations
```

## 3. Understanding Migration Files
Once a migration is created, a migration file is generated in the `Migrations` folder. This file contains:
- `Up()` method: Defines schema changes (adding tables, columns, etc.).
- `Down()` method: Reverts schema changes.

### Example Migration File
```csharp
public partial class InitialCreate : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "Users",
            columns: table => new
            {
                Id = table.Column<int>(nullable: false)
                    .Annotation("SqlServer:Identity", "1, 1"),
                Name = table.Column<string>(nullable: true),
                Email = table.Column<string>(nullable: true)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_Users", x => x.Id);
            });
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(name: "Users");
    }
}
```

## 4. Applying Migrations
To apply a migration and update the database:
```sh
Update-Database
```
or using **.NET CLI**:
```sh
dotnet ef database update
```

## 5. Rolling Back Migrations
Undo the last migration:
```sh
Remove-Migration
```
Revert to a previous migration:
```sh
Update-Database PreviousMigrationName
```

## 6. Generating SQL Script
To generate a SQL script for manual execution:
```sh
dotnet ef migrations script
```
For a specific migration:
```sh
dotnet ef migrations script InitialCreate LatestMigration
```

## 7. Handling Model Changes
If the model changes (e.g., adding a new column), create a new migration:
```sh
Add-Migration AddAgeColumn
```
Then apply it:
```sh
Update-Database
```

## Conclusion
Migration scripts in **Entity Framework** provide a structured way to handle database schema changes efficiently, keeping models and database schemas in sync.

Would you like an example for a specific database (SQL Server, MySQL, PostgreSQL)? 🚀
