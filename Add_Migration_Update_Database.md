# Entity Framework: Add Migration and Update Database

## 1. Adding a Migration
In Entity Framework, migrations allow you to manage database schema changes over time.

### **Steps to Add a Migration:**
1. Open the terminal or package manager console (PMC) in Visual Studio.
2. Navigate to your project's directory if needed.
3. Run the following command:
   ```sh
   dotnet ef migrations add MigrationName
   ```
   Replace `MigrationName` with a descriptive name (e.g., `InitialCreate`).

### **Example:**
```sh
dotnet ef migrations add InitialCreate
```
This will generate a migration file in the `Migrations` folder, containing the schema changes.

---

## 2. Updating the Database
After adding a migration, you need to apply the changes to the database.

### **Steps to Update Database:**
1. Run the following command:
   ```sh
   dotnet ef database update
   ```
   This applies the latest migrations to the database.

### **Example:**
```sh
dotnet ef database update
```

---

## 3. Checking Migration Status
To check the applied migrations, use:
```sh
dotnet ef migrations list
```
This lists all migrations, indicating which ones have been applied.

---

## 4. Rolling Back a Migration
If you need to undo the last migration before updating the database:
```sh
dotnet ef migrations remove
```

If the migration is already applied, revert to a previous state:
```sh
dotnet ef database update PreviousMigrationName
```

---

## Conclusion
- **`dotnet ef migrations add MigrationName`** – Creates a migration.
- **`dotnet ef database update`** – Applies the migration to the database.
- **`dotnet ef migrations list`** – Checks the applied migrations.
- **`dotnet ef migrations remove`** – Removes the last migration.

Using migrations in Entity Framework ensures a structured and versioned approach to managing database schema changes efficiently.

