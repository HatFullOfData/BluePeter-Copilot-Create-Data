# BluePeter-Copilot-Create-Data

Repository of scripts to build demo and exercise data for a Power BI training course.

## Purpose
This repository contains helper scripts used to generate realistic (but fictional) sample data sets for:
- Instructor demos
- Hands-on student exercises
- Power BI modeling and DAX practice

## Scenario / Fictional Company
The data produced by these scripts is based on a fictional company that supplies party products, such as:
- Banners
- Balloons
- Other celebration and event supplies

## What you'll find here
Depending on the script set, you may find assets that:
- Create or refresh source data files (for example CSV/Excel)
- Generate customers, products, sales orders, and dates
- Produce repeatable outputs for consistent training labs

## How to use
1. Clone the repository.
2. Run the scripts relevant to the lab or demo you are preparing.
3. Load the generated data into Power BI Desktop as part of the course exercises.

## Notes
- All data is fictitious and intended for training purposes only.
- Outputs are designed to be repeatable so learners can follow along reliably.

## Microsoft Fabric Warehouse SQL scripting rules (for this repo)

When writing T-SQL scripts intended to run in a **Microsoft Fabric Warehouse**, use these rules to avoid common errors:

### Primary keys in tables
- **Do not** use `PRIMARY KEY` inline in a `CREATE TABLE` statement.
  - Example that fails in this environment:
    - `CustomerID INT NOT NULL IDENTITY(1,1), CONSTRAINT PK_Customer PRIMARY KEY (CustomerID)`
- **Do** create the table first, then add the primary key using `ALTER TABLE`:
  - `ALTER TABLE dbo.Customer ADD CONSTRAINT PK_Customer PRIMARY KEY (CustomerID);`

### Constraint enforcement
- Enforced constraints are not supported in Microsoft Fabric Warehouse. All constraints must include `NOT ENFORCED` syntax.
  - Example that fails in this environment:
    - `ALTER TABLE dbo.Customer ADD CONSTRAINT PK_Customer PRIMARY KEY (CustomerID);`
  - Example that works:
    - `ALTER TABLE dbo.Customer ADD CONSTRAINT PK_Customer PRIMARY KEY (CustomerID) NOT ENFORCED;`

### Identity columns
- Identity columns **must** use the `BIGINT` data type in Microsoft Fabric Warehouse.
- Identity columns **do not support** specifying SEED or INCREMENT values.
  - Example that fails in this environment:
    - `CustomerID INT NOT NULL IDENTITY(1,1)` (wrong data type and specifies seed/increment)
    - `CustomerID BIGINT NOT NULL IDENTITY(1,1)` (specifies seed/increment)
  - Example that works:
    - `CustomerID BIGINT NOT NULL IDENTITY`

### String data types
- Use `VARCHAR(8000)` for string columns in Microsoft Fabric Warehouse.
  - Example that fails in this environment:
    - `CustomerName NVARCHAR(255)`
  - Example that works:
    - `CustomerName VARCHAR(8000)`

### DATETIME2 precision
- The `DATETIME2` data type requires an explicit precision value between 0 and 6 in Microsoft Fabric Warehouse.
  - Example that fails in this environment:
    - `CreatedAt DATETIME2 NOT NULL`
  - Example that works:
    - `CreatedAt DATETIME2(6) NOT NULL`

### Defaults in tables
- **Do not** use `DEFAULT (...)` inline in a `CREATE TABLE` statement.
  - Example that fails in this environment:
    - `CreatedAt DATETIME2 NOT NULL DEFAULT (SYSUTCDATETIME())`
- **Do** create the table first, then add defaults using `ALTER TABLE`:
  - `ALTER TABLE dbo.MyTable ADD CONSTRAINT DF_MyTable_CreatedAt DEFAULT (SYSUTCDATETIME()) FOR CreatedAt;`

### Re-runnable scripts
- Prefer `IF NOT EXISTS (...)` BEGIN ... END` patterns so scripts can be executed multiple times safely.

### Batch separators
- `GO` may not be supported in all execution contexts. If you hit errors around `GO`, remove it and run as a single batch (or split into separate queries manually).