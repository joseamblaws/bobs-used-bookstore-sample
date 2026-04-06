# Next Steps

## Issues resolved
- Transformed Bookstore.Domain.csproj to net8.0
- Transformed Bookstore.Data.csproj to net8.0
- Transformed Bookstore.Web.csproj to net8.0
- Transformed Bookstore.Cdk.csproj to net8.0
- Transformed Bookstore.Domain.Tests.csproj to net8.0

## Overview

The transformation appears to have completed successfully. No build errors were detected across any of the projects in the solution:

- `Bookstore.Data`
- `Bookstore.Domain.Tests`
- `Bookstore.Cdk`
- `Bookstore.Web`
- `Bookstore.Domain`

The following steps outline how to validate, test, and deploy the migrated solution.

---

## 1. Restore Dependencies

Run a full NuGet package restore to ensure all dependencies are resolved correctly in the new target framework.

```bash
dotnet restore
```

Review the output for any warnings related to package compatibility or deprecated packages that may need to be updated.

---

## 2. Build the Entire Solution

Perform a full solution build to confirm there are no compilation issues.

```bash
dotnet build --configuration Release
```

Address any warnings that surface during the build, particularly those related to nullable reference types or obsolete APIs, as these can indicate areas that may cause runtime issues.

---

## 3. Run the Unit Tests

Execute the test project to verify that existing business logic behaves as expected after the migration.

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated before proceeding, as they may indicate behavioral regressions introduced during the transformation.

---

## 4. Validate the Data Layer

Since `Bookstore.Data` handles data access, verify the following:

- **Connection strings** in configuration files (e.g., `appsettings.json`) are correct and point to the intended database.
- If Entity Framework Core is in use, confirm that migrations are up to date by running:

```bash
dotnet ef migrations list --project app/Bookstore.Data
```

If migrations are missing or out of sync, generate a new migration and apply it to the target database:

```bash
dotnet ef migrations add PostMigration --project app/Bookstore.Data
dotnet ef database update --project app/Bookstore.Data
```

---

## 5. Validate the Web Project

Run the `Bookstore.Web` project locally to confirm the application starts and behaves correctly.

```bash
dotnet run --project app/Bookstore.Web --configuration Release
```

Check the following:

- The application starts without runtime exceptions.
- Key routes and pages load as expected.
- Any authentication or authorization flows function correctly.
- Static assets are served properly.

---

## 6. Validate the CDK Project

If `Bookstore.Cdk` defines infrastructure, verify that the project builds and synthesizes correctly using the AWS CDK toolchain.

```bash
dotnet build app/Bookstore.Cdk --configuration Release
```

If the CDK project uses the `Amazon.CDK` NuGet packages, confirm that the package versions are compatible with your installed AWS CDK CLI version.

---

## 7. Review Target Framework Versions

Open each `.csproj` file and confirm that the `<TargetFramework>` element reflects the intended cross-platform .NET version (e.g., `net8.0`). Ensure consistency across all projects to avoid inter-project compatibility issues.

---

## 8. Check for Removed or Changed APIs

Cross-platform .NET removes certain APIs that were available in .NET Framework. Run the .NET Upgrade Assistant compatibility analyzer or the Platform Compatibility Analyzer to identify any remaining usage of unsupported APIs:

```bash
dotnet add package Microsoft.DotNet.UpgradeAssistant.Extensions.Default.Analyzers
dotnet build
```

Review any resulting analyzer warnings and update the affected code accordingly.

---

## 9. Manual Smoke Test

Before deploying to any environment, perform a manual walkthrough of the core application workflows, such as browsing, searching, and purchasing books, to confirm end-to-end functionality is intact.