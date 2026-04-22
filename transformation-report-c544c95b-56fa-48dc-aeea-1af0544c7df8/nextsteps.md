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

Address any warnings that surface during the build, particularly those related to nullable reference types, obsolete APIs, or platform compatibility.

---

## 3. Run the Domain Unit Tests

Execute the test project to verify that the core domain logic behaves as expected after migration.

```bash
dotnet test app/Bookstore.Domain.Tests/Bookstore.Domain.Tests.csproj --configuration Release --verbosity normal
```

Review the test results carefully. Any failing tests should be investigated before proceeding, as they may indicate behavioral regressions introduced during the transformation.

---

## 4. Verify Data Layer Functionality

The `Bookstore.Data` project likely contains database access logic such as Entity Framework Core migrations or a DbContext. Verify the following:

- Confirm the correct database provider package is referenced (e.g., `Microsoft.EntityFrameworkCore.SqlServer`, `Npgsql.EntityFrameworkCore.PostgreSQL`, etc.).
- If using Entity Framework Core, check that existing migrations are still valid:

```bash
dotnet ef migrations list --project app/Bookstore.Data
```

- If the schema has changed or migrations are out of sync, create a new migration:

```bash
dotnet ef migrations add PostMigration --project app/Bookstore.Data
```

- Apply pending migrations to your target database:

```bash
dotnet ef database update --project app/Bookstore.Data
```

---

## 5. Run and Validate the Web Application Locally

Start the `Bookstore.Web` project locally and manually verify core functionality.

```bash
dotnet run --project app/Bookstore.Web --configuration Release
```

Check the following:

- The application starts without runtime exceptions.
- Key pages and routes load correctly.
- Any authentication or authorization flows work as expected.
- Static assets (CSS, JavaScript) are served correctly.
- Database reads and writes function as expected end-to-end.

---

## 6. Review the CDK Project

The `Bookstore.Cdk` project is likely an AWS Cloud Development Kit (CDK) project used for infrastructure definition. Verify the following:

- Confirm the `Amazon.CDK` NuGet packages are targeting a compatible version.
- Synthesize the CDK stack to ensure the infrastructure definitions compile and resolve correctly:

```bash
cdk synth
```

- Review the synthesized CloudFormation template for any unexpected changes that may have resulted from the migration.

---

## 7. Check for Runtime Configuration

Review the following configuration files to ensure they are correct for the new environment:

- `appsettings.json` and `appsettings.Production.json` in `Bookstore.Web`
- Connection strings pointing to the correct database endpoints
- Any environment-specific settings that may have been affected by the migration

---

## 8. Deploy the Application

Once all of the above validation steps pass, deploy the application to your target environment.

```bash
dotnet publish app/Bookstore.Web --configuration Release --output ./publish
```

Copy the contents of the `./publish` directory to your target hosting environment and start the application using:

```bash
dotnet Bookstore.Web.dll
```

If deploying infrastructure via CDK, run:

```bash
cdk deploy
```

Confirm the deployment completes without errors and perform a final round of validation against the live environment.