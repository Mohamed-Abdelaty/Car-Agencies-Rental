# 🚘 Car-Agencies-Rental

<div align="center">

### **Enterprise-Grade Car Rental Fleet Management & Leasing Platform**

*Engineered with ASP.NET Core 8 MVC, Entity Framework Core 8, Microsoft SQL Server 2022, and Docker.*

<br/>

[![.NET 8.0](https://img.shields.io/badge/.NET-8.0-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/)
[![C# 12](https://img.shields.io/badge/C%23-12.0-239120?style=for-the-badge&logo=c-sharp&logoColor=white)](https://learn.microsoft.com/dotnet/csharp/)
[![ASP.NET Core MVC](https://img.shields.io/badge/ASP.NET_Core-MVC-512BD4?style=for-the-badge&logo=dotnet&logoColor=white)](https://dotnet.microsoft.com/apps/aspnet/mvc)
[![EF Core](https://img.shields.io/badge/EF_Core-8.0.8-512BD4?style=for-the-badge&logo=nuget&logoColor=white)](https://www.nuget.org/packages/Microsoft.EntityFrameworkCore)
[![SQL Server 2022](https://img.shields.io/badge/SQL_Server-2022-CC292B?style=for-the-badge&logo=microsoftsqlserver&logoColor=white)](https://www.microsoft.com/sql-server)
[![Bootstrap 5](https://img.shields.io/badge/Bootstrap-5.3.3-7952B3?style=for-the-badge&logo=bootstrap&logoColor=white)](https://getbootstrap.com/)
[![Docker](https://img.shields.io/badge/Docker-Orchestrated-2496ED?style=for-the-badge&logo=docker&logoColor=white)](https://www.docker.com/)
[![License: MIT](https://img.shields.io/badge/License-MIT-green.svg?style=for-the-badge)](LICENSE)

<br/>

```
┌────────────────────────────────────────────────────────────────────────────────────────┐
│  ⚡ Real-Time Fleet Availability  •  💰 Automated Pricing Engine  •  🛡️ Ownership Guard  │
│  🐳 Multi-Container Docker Mesh   •  📊 Executive Analytics       •  📜 Complete Audits │
└────────────────────────────────────────────────────────────────────────────────────────┘
```

<br/>

[Features](#-key-features) • [Architecture](#-system-architecture) • [Database Design](#-database-schema--erd) • [Workflows](#-rental-lifecycle--workflows) • [API Directory](#-api-routes--controller-directory) • [Quick Start](#-quick-start)

---

</div>

<br/>

## ⚡ Key Features

* **🏢 Multi-Branch Agency Management:** Manage multiple autonomous agency branches across cities, with comprehensive metadata, geolocation, and assigned fleet inventories.
* **🚗 Dynamic Fleet Inventory & Availability:** Live availability state tracking (`Available`, `Rented`, `UnderMaintenance`) with multi-parameter filtering by agency branch and operational status.
* **💰 Automated Rental Pricing Engine:** Automatically computes lease costs server-side based on vehicle daily rates and duration:
  $$\text{Total Price} = \max(1, \text{Days}) \times \text{Daily Rate}$$
  Eliminates client-side price tampering and guarantees billing integrity.
* **🔄 Synchronized Vehicle State Transitions:** Vehicles atomically toggle to `Rented` upon contract creation and automatically return to `Available` once checked in via the dedicated `Return` action.
* **👥 Customer & Driver Directory:** Registry tracking customer contact info and driving license numbers, with full historical records of past and active bookings.
* **🛡️ Resource-Level Ownership Authorization:** Built on **ASP.NET Core Identity**. While read actions are open to authorized operators, mutating actions (`Edit`, `Delete`, `Return`) verify `CreatedByUserId`, preventing unauthorized modification of records created by other agents.
* **📊 Executive Analytics Dashboard:** Live operations center displaying total agencies, vehicle availability ratios, active leases, cumulative revenue, and recent contracts.
* **🎨 Modern Dark UI Kit:** Custom 788-line CSS design system (`site.css`) built over Bootstrap 5.3.3 featuring deep navy tones (`#0D1117`), gradient badges, responsive tables, and unobtrusive client-side validation.

---

## 📊 System Metrics & Telemetry

### 📈 Core Architecture Benchmarks

| Metric Dimension | Verified Value | Implementation Details |
| :--- | :---: | :--- |
| **Framework Target** | `.NET 8.0` | C# 12 on ASP.NET Core 8 MVC LTS runtime |
| **Controller Actions** | **40 Endpoints** | Distributed across 6 domain controllers |
| **Database Tables** | **11 Tables** | 4 core business domains + 7 ASP.NET Identity tables |
| **Razor Views** | **21 Views** | Strongly-typed `.cshtml` views with 6 ViewModels |
| **Custom Styling** | **788 Lines** | High-contrast modern dark design system (`site.css`) |
| **Docker Services** | **2 Containers** | Web App container (`:8080`) + SQL Server 2022 (`:1433`) |
| **Pre-Seeded Dataset** | **4 Cars / 2 Agencies** | Initial baseline records generating 300.00 EGP total value |

<br/>

### 📊 Seed Fleet Daily Rates (EGP / Day)

```
Chevrolet Optra    [25.00 EGP]  █████████████░░░░░░░░░░░░░░░
Hyundai Elantra    [30.00 EGP]  ████████████████░░░░░░░░░░░░
Toyota Corolla     [35.00 EGP]  ███████████████████░░░░░░░░░
Kia Sportage       [45.00 EGP]  ████████████████████████░░░░
```

### 📊 Seed Fleet Availability Ratio

```
Available Cars:     [75%]  ██████████████████████████████░░░░░░░░░░  (3 Vehicles)
Under Maintenance:  [25%]  ██████████░░░░░░░░░░░░░░░░░░░░░░░░░░░░░░  (1 Vehicle)
```

---

## 🏗️ System Architecture

The application is structured into four distinct layers: Presentation, Security Gateway, Application Controllers, and Data Persistence.

```
┌────────────────────────────────────────────────────────────────────────┐
│                        🌐 PRESENTATION LAYER                           │
│     Razor Views (.cshtml)  •  Bootstrap 5.3.3  •  Custom Dark CSS      │
│         jQuery 3.7.1  •  Unobtrusive Validation  •  site.js            │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ HTTPS / Form POST
┌───────────────────────────────────▼────────────────────────────────────┐
│                  🛡️ SECURITY & MIDDLEWARE GATEWAY                      │
│        Authentication Cookie  •  [ValidateAntiForgeryToken]            │
│            Ownership Guard: (CreatedByUserId == CurrentUserId)          │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Validated Request
┌───────────────────────────────────▼────────────────────────────────────┐
│                    ⚡ APPLICATION CONTROLLER LAYER                     │
│  HomeController       AgenciesController      CarsController           │
│  (KPIs & Revenue)     (Branch Operations)     (Fleet Inventory)        │
│                                                                        │
│  CustomersController  RentalsController       AccountController        │
│  (Client Registry)    (Booking Lifecycle)     (Identity & Sessions)    │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Strongly-Typed LINQ Queries
┌───────────────────────────────────▼────────────────────────────────────┐
│                     🗄️ DATA ACCESS & PERSISTENCE                        │
│             Entity Framework Core 8.0.8 (DbContext / Code-First)       │
│                 DbInitializer (Automatic Seeding on Boot)             │
└───────────────────────────────────┬────────────────────────────────────┘
                                    │ Port 1433
┌───────────────────────────────────▼────────────────────────────────────┐
│                     💾 MICROSOFT SQL SERVER 2022                       │
│           (LocalDB in Development / Containerized in Production)       │
└────────────────────────────────────────────────────────────────────────┘
```

---

## 🗄️ Database Schema & ERD

### Relational Entity Blueprint

```
 ┌──────────────────────┐                   ┌──────────────────────┐
 │       AGENCY         │                   │         CAR          │
 ├──────────────────────┤                   ├──────────────────────┤
 │ * AgencyId      [PK] │ 1               * │ * CarId         [PK] │
 │   Name               │───────────────────│   Brand              │
 │   Address            │                   │   Model              │
 │   PhoneNumber        │  (Cascade Delete) │   Year               │
 │   Email              │                   │ * PlateNumber   [UK] │
 │   FoundedOn          │                   │   DailyRate          │
 │   CreatedByUserId    │                   │   Status             │
 └──────────────────────┘                   │ * AgencyId      [FK] │
                                            │   CreatedByUserId    │
                                            └──────────┬───────────┘
                                                       │ 1
                                                       │
                                                       │ * (Restrict Delete)
 ┌──────────────────────┐                   ┌──────────▼───────────┐
 │       CUSTOMER       │                   │        RENTAL        │
 ├──────────────────────┤                   ├──────────────────────┤
 │ * CustomerId    [PK] │ 1               * │ * RentalId      [PK] │
 │   FullName           │───────────────────│ * CarId         [FK] │
 │   PhoneNumber        │ (Restrict Delete) │ * CustomerId    [FK] │
 │   Email              │                   │   StartDate          │
 │   DrivingLicenseNo   │                   │   EndDate            │
 │   CreatedByUserId    │                   │   TotalPrice         │
 └──────────────────────┘                   │   Status             │
                                            │   CreatedByUserId    │
                                            └──────────────────────┘
```

### Relational Integrity Constraints
* **Agency ➔ Cars:** `One-to-Many` with `DeleteBehavior.Cascade` (Deleting a branch removes its fleet).
* **Car ➔ Rentals:** `One-to-Many` with `DeleteBehavior.Restrict` (Vehicles with rental records cannot be deleted).
* **Customer ➔ Rentals:** `One-to-Many` with `DeleteBehavior.Restrict` (Customers with rental contracts cannot be deleted).
* **License Plate:** Enforced unique index (`Car.PlateNumber`).

---

## 🔄 Rental Lifecycle & Workflows

### 1. Booking Transaction Step-by-Step Flow

```
[ Operator Submits Form ]
          │
          ▼
{ Validate Dates: EndDate > StartDate? }
   ├─► NO  ──► Display Error: "End date must be after start date"
   └─► YES
          │
          ▼
{ Fetch Selected Car & Verify Status == Available? }
   ├─► NO  ──► Display Error: "Selected car is not available for rent"
   └─► YES
          │
          ▼
[ Calculate TotalPrice = Math.Max(1, EndDate - StartDate).Days * Car.DailyRate ]
          │
          ▼
[ Atomically Update Car.Status = CarStatus.Rented ]
          │
          ▼
[ Stamp Rental.CreatedByUserId = CurrentUserId ]
          │
          ▼
[ Commit Database Transaction via EF Core SaveChangesAsync() ]
          │
          ▼
[ Redirect to /Rentals/Index with Success Notification ]
```

---

### 2. Vehicle Availability State Transitions

```
                    ┌─────────────────────────┐
                    │     CAR REGISTERED      │
                    └────────────┬────────────┘
                                 │
                                 ▼
                     ┌───────────────────────┐
                     │       AVAILABLE       │ ◄────────────────┐
                     │ (Ready for Customers) │                  │
                     └───────────┬───────────┘                  │
                                 │                              │
                POST /Rentals/Create                            │ POST /Rentals/Return/{id}
                                 │                              │ (Contract Completed)
                                 ▼                              │
                     ┌───────────────────────┐                  │
                     │        RENTED         │ ─────────────────┘
                     │ (Active Lease Period) │
                     └───────────────────────┘
```

---

## 🌐 API Routes & Controller Directory

The platform exposes **40 action routes** across 6 controllers:

### 1. Account Management (`/Account`)
* `GET  /Account/Register` — User registration interface
* `POST /Account/Register` — Validates credentials, creates user, and logs in
* `GET  /Account/Login` — Sign-in interface (supports username or email)
* `POST /Account/Login` — Validates password and issues cookie
* `POST /Account/Logout` — Invalidates session and clears cookie

### 2. Executive Dashboard (`/Home`)
* `GET  /` or `/Home/Index` — Executive overview with live KPIs and recent contracts
* `GET  /Home/Error` — Application error diagnostics

### 3. Agency Branch Management (`/Agencies`)
* `GET  /Agencies` — Branch directory with optional search filter (`?searchString=`)
* `GET  /Agencies/Details/{id}` — Branch overview, fleet breakdown, and car list
* `GET  /Agencies/Create` — Agency creation form
* `POST /Agencies/Create` — Persists new branch with creator ownership
* `GET  /Agencies/Edit/{id}` — Edit branch form `[Owner Only]`
* `POST /Agencies/Edit/{id}` — Persists updates `[Owner Only]`
* `GET  /Agencies/Delete/{id}` — Deletion confirmation view `[Owner Only]`
* `POST /Agencies/Delete/{id}` — Deletes branch with cascade to fleet `[Owner Only]`

### 4. Fleet Inventory & Tracking (`/Cars`)
* `GET  /Cars` — Fleet directory with multi-parameter filter (`?agencyId=&status=`)
* `GET  /Cars/Details/{id}` — Vehicle specifications and historical rental contracts
* `GET  /Cars/Create` — Car registration form with branch dropdown
* `POST /Cars/Create` — Adds new vehicle to inventory
* `GET  /Cars/Edit/{id}` — Edit vehicle form `[Owner Only]`
* `POST /Cars/Edit/{id}` — Persists vehicle updates `[Owner Only]`
* `GET  /Cars/Delete/{id}` — Vehicle deletion confirmation `[Owner Only]`
* `POST /Cars/Delete/{id}` — Removes vehicle (restricted if rented) `[Owner Only]`

### 5. Client Directory (`/Customers`)
* `GET  /Customers` — Customer directory with name/phone search (`?searchString=`)
* `GET  /Customers/Details/{id}` — Customer profile and full rental history
* `GET  /Customers/Create` — Customer registration form
* `POST /Customers/Create` — Registers customer with driving license number
* `GET  /Customers/Edit/{id}` — Edit customer form `[Owner Only]`
* `POST /Customers/Edit/{id}` — Persists customer updates `[Owner Only]`
* `GET  /Customers/Delete/{id}` — Deletion confirmation `[Owner Only]`
* `POST /Customers/Delete/{id}` — Removes customer (restricted if rentals exist) `[Owner Only]`

### 6. Rental Contracts & Lifecycle (`/Rentals`)
* `GET  /Rentals` — Contract list with status filter (`?status=`)
* `GET  /Rentals/Details/{id}` — 3-card breakdown (Car, Customer, Contract)
* `GET  /Rentals/Create` — Booking form with available vehicles and customer select list
* `POST /Rentals/Create` — Automates pricing, locks car as `Rented`, activates lease
* `GET  /Rentals/Edit/{id}` — Edit contract form `[Owner Only]`
* `POST /Rentals/Edit/{id}` — Updates contract terms `[Owner Only]`
* `GET  /Rentals/Delete/{id}` — Deletion confirmation `[Owner Only]`
* `POST /Rentals/Delete/{id}` — Deletes contract `[Owner Only]`
* `POST /Rentals/Return/{id}` — Marks lease `Completed` and frees vehicle to `Available` `[Owner Only]`

---

## 📂 Repository Directory Tree

```
CarAgenciesMVC/
├── 📁 Controllers/                     # HTTP Request Handlers
│   ├── AccountController.cs            # Auth, Login & Registration
│   ├── AgenciesController.cs           # Agency branch management
│   ├── CarsController.cs               # Fleet inventory & filtering
│   ├── CustomersController.cs          # Customer & driver directory
│   ├── HomeController.cs               # KPI Dashboard & analytics
│   └── RentalsController.cs            # Booking engine & lifecycle
│
├── 📁 Data/                            # Database Infrastructure
│   ├── ApplicationDbContext.cs         # EF Core DbContext with relationship rules
│   └── DbInitializer.cs                # Database bootstrapper with pre-seeded data
│
├── 📁 Migrations/                      # Schema Versioning
│   └── ApplicationDbContextModelSnapshot.cs # Full schema snapshot of 11 tables
│
├── 📁 Models/                          # Domain Entities
│   ├── Agency.cs                       # Branch entity
│   ├── Car.cs                          # Vehicle specifications
│   ├── Customer.cs                     # Driver registry
│   ├── Rental.cs                       # Transactional contract
│   └── 📁 Enums/                       # Domain Status Enums
│       ├── CarStatus.cs                # Available, Rented, UnderMaintenance
│       └── RentalStatus.cs             # Active, Completed, Cancelled
│
├── 📁 ViewModels/                      # Presentation Models (DTOs)
│   ├── AgencyDetailsViewModel.cs       # Branch stats & fleet breakdown
│   ├── CarFormViewModel.cs             # Vehicle form wrapper
│   ├── DashboardViewModel.cs           # KPI cards & recent contracts
│   ├── LoginViewModel.cs               # Sign-in credentials
│   ├── RegisterViewModel.cs            # Registration form
│   └── RentalFormViewModel.cs          # Booking form with available cars
│
├── 📁 Views/                           # Razor View Templates (.cshtml)
│   ├── 📁 Account/                     # Login & Register views
│   ├── 📁 Agencies/                    # CRUD views for branches
│   ├── 📁 Cars/                        # CRUD views for fleet
│   ├── 📁 Customers/                   # CRUD views for customers
│   ├── 📁 Home/                        # Dashboard view
│   ├── 📁 Rentals/                     # Booking & details views
│   └── 📁 Shared/                      # Master layout & validation partials
│
├── 📁 wwwroot/                         # Static Assets
│   ├── 📁 css/site.css                 # 788-line custom Dark Theme UI Kit
│   └── 📁 js/site.js                   # Bootstrap tooltips & deletion confirmations
│
├── appsettings.json                    # Connection strings & configuration
├── CarAgenciesMVC.csproj               # Project dependencies (.NET 8)
├── docker-compose.yml                  # Multi-container orchestration (App + SQL Server)
├── Dockerfile                          # Multi-stage container build definition
└── Program.cs                          # Pipeline & dependency injection
```

---

## 🚀 Quick Start

### Method 1: Local Development (.NET CLI)

1. **Clone the Repository:**
   ```bash
   git clone https://github.com/Mohamed-Abdelaty/Car-Agencies-Rental.git
   cd Car-Agencies-Rental/CarAgenciesMVC
   ```

2. **Verify Database Connection (`appsettings.json`):**
   ```json
   {
     "ConnectionStrings": {
       "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=CarAgenciesDb;Trusted_Connection=True;MultipleActiveResultSets=true"
     }
   }
   ```

3. **Restore & Run:**
   ```bash
   dotnet restore
   dotnet build
   dotnet run
   ```
   *The database schema and initial test records will be automatically created on startup via `DbInitializer.Initialize()`.*

4. **Open in Browser:**
   - HTTPS: `https://localhost:5001`
   - HTTP: `http://localhost:5000`

---

### Method 2: Containerized Mesh (Docker Compose)

Run the full production stack with zero local dependencies other than Docker:

1. **Start Services:**
   ```bash
   docker compose up -d --build
   ```

2. **Check Container Status:**
   ```bash
   docker compose ps
   ```
   *The web application will automatically delay startup until SQL Server 2022 passes its internal healthcheck.*

3. **Access Services:**
   - **Web Application:** `http://localhost:8080`
   - **SQL Server:** `localhost:1433` (User: `sa`, Password: `CarAgencies@123`)

4. **Stop Services:**
   ```bash
   docker compose down
   ```

---

## 👥 Authors & Acknowledgements

Developed as the **Graduation Capstone Project for the Information Technology Institute (ITI) C# Track**:

* **Mohamed Abdelaty Mohamed Elshaboury** ([@Elshaboury2005](https://github.com/Elshaboury2005)) — ID: 75
* **Reem Ahmed Amin Hashish** — ID: 74

---

## 📄 License

This project is licensed under the **MIT License**. See the [LICENSE](LICENSE) file for details.