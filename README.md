# 🚀 Reusable Business Modules

> **Open-source .NET libraries for reusable business modules**

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![.NET](https://img.shields.io/badge/.NET-10+-purple.svg)](https://dotnet.microsoft.com/)

---

## 📋 Table of Contents

- [The Problem](#-the-problem)
- [The Solution](#-the-solution)
- [Features](#-features)
- [Quick Start](#-quick-start)
- [Available Modules](#-available-modules)
- [Configuration](#-configuration)
- [Architecture](#-architecture)
- [Contributing](#-contributing)
- [Roadmap](#-roadmap)

---

## 🔴 The Problem

In every new project, we rewrite the same modules:

```
┌─────────────────────────────────────────────────────────────┐
│  Project 1: Spare Parts Management                          │
│  ├── 📦 Inventory System     ← Duplicated code!             │
│  ├── 👤 Customer Management  ← Duplicated code!             │
│  ├── 🔐 Auth & Permissions   ← Duplicated code!             │
│  └── 📧 Notifications        ← Duplicated code!             │
├─────────────────────────────────────────────────────────────┤
│  Project 2: Restaurant POS                                  │
│  ├── 📦 Inventory System     ← Same logic!                  │
│  ├── 👤 Customer Management  ← Same logic!                  │
│  └── ...                                                    │
├─────────────────────────────────────────────────────────────┤
│  Project 3: E-Commerce Store                                │
│  ├── 📦 Inventory System     ← Copy-Paste!                  │
│  └── ...                                                    │
└─────────────────────────────────────────────────────────────┘
```

### Resulting Problems:
- ⏰ **Time waste** - Writing the same code repeatedly
- 🐛 **Bug duplication** - Same bugs in every project
- 🔧 **Maintenance nightmare** - Fixing a bug requires updating all projects
- 📚 **No standardization** - Each project has different implementations

---

## 🟢 The Solution

**Ready-to-use NuGet packages** for common business modules, designed to be:
- ✅ **Easy to install** - Just one line
- ✅ **Flexible** - Works with any project (simple or complex)
- ✅ **Customizable** - Change what you need
- ✅ **Well-documented** - Comprehensive docs + AI Skills

```
┌─────────────────────────────────────────────────────────────┐
│                    📦 NuGet Packages                        │
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐         │
│  │  Inventory  │  │  Customers  │  │    Auth     │         │
│  │   Module    │  │   Module    │  │   Module    │         │
│  └──────┬──────┘  └──────┬──────┘  └──────┬──────┘         │
└─────────┼────────────────┼────────────────┼─────────────────┘
          │                │                │
          ▼                ▼                ▼
┌─────────────────────────────────────────────────────────────┐
│  Your Project                                               │
│                                                             │
│  // One line per module!                                    │
│  services.AddInventoryModule<AppDbContext>();               │
│  services.AddCustomersModule<AppDbContext>();               │
│  services.AddAuthModule();                                  │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## ✨ Features

### 1. 🎯 Easy Installation

```bash
dotnet add package MyCompany.Modules.Inventory
```

### 2. 🔌 Simple Integration

```csharp
// Program.cs - Just 3 lines!
builder.Services.AddInventoryModule<AppDbContext>();
app.MapInventoryEndpoints();
// Done! 🎉
```

### 3. 🗄️ Works with Your Database

```csharp
// Your existing DbContext
public class AppDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        // Add inventory tables
        modelBuilder.ApplyInventoryModuleConfiguration();
        
        // Link to your table
        modelBuilder.LinkStockToProduct<Product>();
    }
}

// Then run migration as usual
// dotnet ef migrations add AddInventoryTables
```

### 4. 🎛️ Full Customization

```csharp
services.AddInventoryModule<AppDbContext>(config =>
{
    config.EnableMultiWarehouse = true;
    config.EnableNegativeStock = false;
    config.DefaultReorderLevel = 10;
    config.TablePrefix = "Inv_";
    
    config.OnStockLow = async (item) => 
        await SendNotification($"Stock low: {item.Name}");
});
```

### 5. 🤖 AI-Ready

Each module comes with:
- 📖 Comprehensive documentation
- 🎯 AI Skills (Copilot/Claude)
- 💡 Code examples

---

## 🚀 Quick Start

### Step 1: Install

```bash
dotnet add package MyCompany.Modules.Inventory
```

### Step 2: Add to DbContext

```csharp
public class AppDbContext : DbContext
{
    public DbSet<Product> Products { get; set; }
    public DbSet<Order> Orders { get; set; }
    
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);
        
        // ✅ Add inventory tables
        modelBuilder.ApplyInventoryModuleConfiguration();
    }
}
```

### Step 3: Register Services

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseSqlServer(connectionString));

// ✅ Add inventory module
builder.Services.AddInventoryModule<AppDbContext>();

var app = builder.Build();

// ✅ Add API endpoints (optional)
app.MapInventoryEndpoints();

app.Run();
```

### Step 4: Run Migration

```bash
dotnet ef migrations add AddInventoryModule
dotnet ef database update
```

### Step 5: Use in Your Code

```csharp
public class OrderService
{
    private readonly IStockService _stockService;
    
    public OrderService(IStockService stockService)
    {
        _stockService = stockService;
    }
    
    public async Task<bool> CreateOrder(Guid productId, int quantity)
    {
        // ✅ Check stock availability
        if (!await _stockService.IsAvailableAsync(productId, quantity))
            return false;
        
        // ✅ Deduct from stock
        await _stockService.DeductAsync(productId, quantity, "Order placed");
        
        return true;
    }
}
```

---

## 📦 Available Modules

### 🟢 Phase 1 (MVP)

| Module | Description | Status |
|--------|-------------|--------|
| **Inventory** | Stock management, warehouses, movements | 🔄 In Development |

### 🟡 Phase 2

| Module | Description | Status |
|--------|-------------|--------|
| **Customers** | Customer management, addresses, categories | 📋 Planned |
| **Notifications** | Email, SMS, Push notifications | 📋 Planned |

### 🔵 Phase 3

| Module | Description | Status |
|--------|-------------|--------|
| **Auth** | Authentication, permissions, JWT | 📋 Planned |
| **Audit** | Change tracking, logging | 📋 Planned |
| **MultiTenancy** | Multi-tenant support | 📋 Planned |

---

## ⚙️ Configuration

### Feature Flags

```csharp
services.AddInventoryModule<AppDbContext>(config =>
{
    // ═══════════════════════════════════════════
    // Features
    // ═══════════════════════════════════════════
    config.EnableMultiWarehouse = false;    // Single warehouse
    config.EnableSerialNumbers = false;     // No serial tracking
    config.EnableBatchTracking = false;     // No batch/lot tracking
    config.EnableNegativeStock = false;     // Prevent negative stock
    config.TrackMovementHistory = true;     // Track movements
    
    // ═══════════════════════════════════════════
    // Database
    // ═══════════════════════════════════════════
    config.TablePrefix = "Inv_";            // Inv_StockItems
    config.Schema = "inventory";            // [inventory].[StockItems]
    
    // ═══════════════════════════════════════════
    // Defaults
    // ═══════════════════════════════════════════
    config.DefaultWarehouseName = "Main Warehouse";
    config.DefaultReorderLevel = 10;
});
```

### Integration Events

```csharp
services.AddInventoryModule<AppDbContext>(config =>
{
    // When stock is low
    config.OnStockLow = async (stockItem) =>
    {
        await _emailService.SendAsync(
            "admin@company.com",
            $"Alert: Low stock - {stockItem.ProductName}"
        );
    };
    
    // When stock runs out
    config.OnStockOut = async (stockItem) =>
    {
        await _productService.MarkAsUnavailable(stockItem.ProductId);
    };
    
    // On any stock movement
    config.OnStockMovement = async (movement) =>
    {
        await _auditService.Log($"Stock {movement.Type}: {movement.Quantity}");
    };
});
```

### Linking to Your Entities

```csharp
// Interface defining a stockable product
public interface IStockableProduct
{
    Guid Id { get; }
    string Name { get; }
    string SKU { get; }
}

// Implement on your existing entity
public class Product : IStockableProduct
{
    public Guid Id { get; set; }
    public string Name { get; set; }
    public string SKU { get; set; }
    
    // Your additional fields
    public decimal Price { get; set; }
    public string Category { get; set; }
}

// Link it
services.AddInventoryModule<AppDbContext, Product>();
```

---

## 🏗️ Architecture

### Design Principles

| Principle | Description |
|-----------|-------------|
| **SOLID** | Single Responsibility, Open/Closed, etc. |
| **Clean Architecture** | Layer separation, dependency inversion |
| **CQRS** | Command Query Responsibility Segregation |
| **Domain Events** | Event-driven communication |
| **Repository Pattern** | Data access abstraction |

### Module Structure

```
BenSumaidea.Modules.Inventory/
├── Domain/
│   ├── Entities/
│   │   ├── StockItem.cs
│   │   ├── Warehouse.cs
│   │   └── StockMovement.cs
│   ├── Events/
│   │   ├── StockLowEvent.cs
│   │   └── StockMovedEvent.cs
│   └── ValueObjects/
│       └── Quantity.cs
├── Application/
│   ├── Commands/
│   │   ├── AdjustStock/
│   │   ├── TransferStock/
│   │   └── CreateWarehouse/
│   ├── Queries/
│   │   ├── GetStockLevel/
│   │   └── SearchStock/
│   └── Interfaces/
│       └── IStockService.cs
├── Infrastructure/
│   ├── Persistence/
│   │   ├── Configurations/
│   │   └── Repositories/
│   └── Services/
│       └── StockService.cs
├── Endpoints/
│   └── InventoryEndpoints.cs
└── DependencyInjection.cs
```

### Technologies Used

| Technology | Purpose |
|------------|---------|
| **.NET 8+** | Framework |
| **EF Core 8+** | ORM |
| **MediatR** | CQRS |
| **FluentValidation** | Validation |
| **Minimal APIs** | Endpoints |

---

## 🤝 Contributing

We welcome contributions! See [CONTRIBUTING.md](CONTRIBUTING.md) for details.

### How to Contribute

1. **Fork** the repository
2. Create a **Branch** for your feature (`git checkout -b feature/amazing-feature`)
3. **Commit** your changes (`git commit -m 'Add amazing feature'`)
4. **Push** to the branch (`git push origin feature/amazing-feature`)
5. Open a **Pull Request**

### Types of Contributions

- 🐛 Bug fixes
- ✨ New features
- 📖 Documentation improvements
- 🧪 Adding tests
- 🌍 Translations
- 💡 Suggestions and ideas

---

## 🗺️ Roadmap

### Q2 2026
- [x] Design core architecture
- [ ] Inventory Module MVP
- [ ] Initial documentation
- [ ] AI Skills

### Q3 2026
- [ ] Customers Module
- [ ] Notifications Module
- [ ] Blazor UI Components

### Q4 2026
- [ ] Auth Module
- [ ] Audit Module
- [ ] MultiTenancy Support

### 2027
- [ ] More modules based on community requests
- [ ] Integration with other frameworks

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).

---

## 💬 Contact

- 🐛 [Issues](https://github.com/BenSumaidea/dotnet-business-modules/issues)
- 💡 [Discussions](https://github.com/BenSumaidea/dotnet-business-modules/discussions)
- 📧 Email: your-email@example.com

---

## ⭐ Acknowledgments

Thanks to all contributors who make this project better!

---

<div align="center">

**Made with ❤️ for the global developer community**

⭐ If you like this project, don't forget to star it!

</div>
