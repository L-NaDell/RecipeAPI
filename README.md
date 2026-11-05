# Recipe API

An ASP.NET Core Web API for storing and organizing family recipes. The long-term goal is a family recipe box that also plans the week's meals and builds a shopping list from them.

## Tech stack

- **.NET 10** / ASP.NET Core Web API (controllers)
- **Entity Framework Core 10** (code-first migrations)
- **SQL Server** (LocalDB for local development)

## Project structure

```
RecipeAPI/
├── Controllers/
│   └── RecipesController.cs   # CRUD endpoints for recipes
├── Database/
│   └── RecipeDbContext.cs     # EF Core DbContext
├── Migrations/                # EF Core migrations (generated)
├── Models/
│   └── Recipe.cs              # Recipe entity
├── Program.cs                 # App startup and service registration
├── appsettings.json           # Configuration, including the connection string
└── RecipeAPI.http             # Sample requests for testing in Visual Studio
```

## Getting started

### Prerequisites

- [.NET 10 SDK](https://dotnet.microsoft.com/download)
- SQL Server LocalDB (installed with Visual Studio's "ASP.NET and web development" workload) or SQL Server Developer Edition
- Optional: SQL Server Management Studio (SSMS) to browse the database

### 1. Clone the repo

```
git clone https://github.com/L-NaDell/RecipeAPI.git
cd RecipeAPI
```

### 2. Check the connection string

`RecipeAPI/appsettings.json` points at LocalDB by default:

```json
"ConnectionStrings": {
  "RecipeDb": "Server=(localdb)\\MSSQLLocalDB;Database=RecipeDb;Trusted_Connection=True;TrustServerCertificate=True"
}
```

If you're using a full SQL Server install, change `Server=` to match (for example `Server=localhost`).

### 3. Create the database

**Visual Studio:** install the `Microsoft.EntityFrameworkCore.Tools` NuGet package (needed once for these commands), then open Tools → NuGet Package Manager → Package Manager Console and run:

```
Update-Database
```

**CLI:**

```
dotnet tool install --global dotnet-ef
cd RecipeAPI
dotnet ef database update
```

This creates the `RecipeDb` database and its `Recipes` table.

### 4. Run the API

Press **F5** in Visual Studio, or from the CLI:

```
cd RecipeAPI
dotnet run --launch-profile http
```

The API runs at `http://localhost:5110`.

## API endpoints

| Method | Route | Description | Responses |
|---|---|---|---|
| GET | `/api/recipes` | List all recipes | 200 |
| GET | `/api/recipes/{id}` | Get one recipe | 200, 404 |
| POST | `/api/recipes` | Create a recipe | 201 |
| PUT | `/api/recipes/{id}` | Replace a recipe | 204, 400, 404 |
| DELETE | `/api/recipes/{id}` | Delete a recipe | 204, 404 |

> **Note:** `PUT` replaces the whole recipe. Any field left out of the request body is cleared.

### Example request

```http
POST /api/recipes
Content-Type: application/json

{
  "name": "Pancakes",
  "category": "Breakfast",
  "description": "Fluffy weekend pancakes",
  "ingredients": "flour,eggs,milk,sugar,baking powder"
}
```

To try every endpoint, open `RecipeAPI/RecipeAPI.http` in Visual Studio while the app is running and click **Send request** above any request.

## Recipe model

| Field | Type | Notes |
|---|---|---|
| `id` | int | Assigned by the database |
| `name` | string | Required |
| `category` | string? | For example "Breakfast", "Dessert" |
| `description` | string? | |
| `ingredients` | string? | Comma-separated for now (see roadmap) |

## Roadmap

- [ ] Add `Instructions`, `Servings` and `Notes` to recipes
- [ ] Move ingredients to their own table with quantity and unit
- [ ] Search recipes by category and name (`GET /api/recipes?category=Dessert&search=pie`)
- [ ] Use request/response classes (DTOs) instead of returning database entities
- [ ] Weekly meal plan (`/api/mealplan`)
- [ ] Shopping list generated from the week's meal plan (`/api/shoppinglist`)
- [ ] Blazor front end
- [ ] Sign-in, then hosting on Azure (App Service and Azure SQL)
- [ ] Recipe photos

## Working with migrations

After changing a model or the DbContext, create and apply a migration:

| Visual Studio (Package Manager Console) | CLI (from the `RecipeAPI` project folder) |
|---|---|
| `Add-Migration <Name>` | `dotnet ef migrations add <Name>` |
| `Update-Database` | `dotnet ef database update` |
