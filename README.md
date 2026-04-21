# Legacy App Modernization with GitHub Copilot (.NET Focus)

> **Duration:** ~1.5 hours hands-on workshop

---

## 🎯 Objective

Modernize a real-world .NET application using GitHub Copilot (agent + extensions) to:

- **Assess** modernization readiness
- **Generate** upgrade recommendations
- **Apply** changes with AI assistance
- **Prepare for** and **deploy to** the cloud

---

## 🧰 Core Resources

| Resource | Link |
|----------|------|
| **Workshop repo** (✅ recommended primary) | <https://github.com/VeVarunSharma/dotnet-migration-copilot-samples> |
| Official .NET modernization guidance | <https://dotnet.microsoft.com/en-us/platform/modernize> |
| Modernize .NET tools & repo | <https://github.com/dotnet/modernize-dotnet> |

---

## 📦 About the Sample App — Contoso University

| Attribute | Value |
|-----------|-------|
| Framework | ASP.NET MVC 5 / .NET Framework 4.8 |
| ORM | Entity Framework Core 3.1.32 |
| Messaging | Microsoft Message Queuing (MSMQ) |
| Hosting | IIS Express (on-premises) |
| Database | SQL Server LocalDB |

The app is a university management system with CRUD for students, courses, instructors, and departments, plus notification and file upload features. It represents a typical legacy .NET application with Windows-only dependencies — an ideal candidate for modernization.

**Target state after the workshop:** .NET 6/8 app deployable to Azure App Service with Azure SQL Database.

---

## ✅ Prerequisites (REQUIRED — before the workshop)

Participants **MUST** have the following installed and configured:

- [ ] **GitHub account** with a Copilot seat assigned
- [ ] **Visual Studio Code** (latest stable)
- [ ] **VS Code Extensions installed:**
  - [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot)
  - [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat)
  - [GitHub Copilot modernization for .NET](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.vscode-dotnet-modernize)
- [ ] **Git** installed
- [ ] **.NET SDK** installed (6 or 8)
- [ ] **GitHub CLI** (`gh`) installed
- [ ] **GitHub Copilot CLI** installed
- [ ] **Repo access** prepared (pre-cloned or accessible)

### ✅ Strongly Recommended

- [ ] **Azure subscription** (Contributor role) — needed for Phase 5
- [ ] **Docker Desktop** (optional, for containerization exploration)

---

## 🚨 Pre-Workshop Validation

> Have participants verify **all** of the following before the session begins.

```bash
# 1. GitHub CLI is authenticated
gh auth status

# 2. Copilot is active in VS Code
#    → Open VS Code, open a file, confirm Copilot icon is active in the status bar

# 3. Repo clones and builds
git clone https://github.com/VeVarunSharma/dotnet-migration-copilot-samples.git
cd dotnet-migration-copilot-samples

# 4. Copilot Chat responds
#    → Open Copilot Chat in VS Code and send any prompt (e.g., "Hello")
```

> **Tip:** If `gh auth status` fails, run `gh auth login` and follow the prompts.

---

## 🧠 Workshop Flow (Phased)

### Phase 1 — Repo Exploration + Context Setup (10–15 min)

**✅ Goal:** Build context for Copilot agents

1. Clone the repo and open it in VS Code:
   ```bash
   git clone https://github.com/VeVarunSharma/dotnet-migration-copilot-samples.git
   cd dotnet-migration-copilot-samples
   code .
   ```
2. Walk through:
   - App structure (`ContosoUniversity/` folder layout)
   - Dependencies (`ContosoUniversity.csproj`, `packages.config`)
   - Runtime version (.NET Framework 4.8)
3. **Prompt Copilot** — use the `@modernize-dotnet` agent or Copilot Chat:
   > "Analyze this application and summarize modernization opportunities, risks, and target state (.NET version + cloud readiness)."

---

### Phase 2 — Run App Modernization Assessment (20 min)

**✅ Goal:** Generate structured modernization insights

**Tools to use:**
- GitHub Copilot modernization for .NET extension
- *OR* Copilot Chat + CLI

**Tasks:**
1. Generate a modernization report covering:
   - Outdated frameworks and libraries
   - Breaking changes between .NET Framework 4.8 → .NET 6/8
   - Package upgrade paths
   - Cloud readiness gaps (MSMQ, IIS Express, LocalDB, file system dependencies)

**✅ Output:**
- Assessment summary document
- Recommended upgrade path: **.NET Framework 4.8 → .NET 6/8**

---

### Phase 3 — Apply Suggested Fixes (30–40 min)

**✅ Goal:** Move from analysis → execution

Use Copilot (agent mode / chat / CLI) to:

1. **Upgrade project files** — convert `.csproj` from legacy format to SDK-style
2. **Refactor incompatible APIs** — replace `System.Web`, `System.Messaging`, etc.
3. **Replace deprecated libraries** — update NuGet packages to modern equivalents
4. **Introduce config modernization** — migrate from `Web.config` to `appsettings.json` and environment variables

**Prompt pattern:**
> In `/plan` mode: "Apply the modernization steps incrementally. Show changes, explain why, and validate after each step."

**✅ Key learning:**
> Copilot = execution + reasoning, not just code generation.

---

### Phase 4 — Validate + Test (15–20 min)

**✅ Goal:** Ensure the modernization is working

1. **Build + run locally:**
   ```bash
   dotnet build
   dotnet run
   ```
2. **Use Copilot to:**
   - Fix build errors and warnings
   - Add or update unit tests
   - Identify regressions from the migration
3. **Prompt:**
   > "Validate this upgrade and highlight potential runtime or deployment risks."

---

### Phase 5 — Deploy to Azure (20–30 min)

**✅ Goal:** Take the modernized app from local → cloud

**Target architecture:**
| Component | Azure Service |
|-----------|---------------|
| Web App | Azure App Service |
| Database | Azure SQL Database |

#### Step-by-step (Copilot-assisted)

1. **Create Azure resources** — prompt Copilot to generate the `az` CLI commands:
   > "Generate Azure CLI commands to create a resource group, an Azure SQL Database, and an App Service plan + web app for this .NET application. Use the East US region."

   Example output Copilot should generate:
   ```bash
   # Set variables
   RESOURCE_GROUP="contoso-university-rg"
   LOCATION="eastus"
   SQL_SERVER="contoso-sql-$(openssl rand -hex 4)"
   SQL_DB="ContosoUniversityDb"
   SQL_ADMIN="sqladmin"
   APP_SERVICE_PLAN="contoso-plan"
   WEB_APP="contoso-university-$(openssl rand -hex 4)"

   # Create resource group
   az group create --name $RESOURCE_GROUP --location $LOCATION

   # Create Azure SQL Server + Database
   az sql server create --name $SQL_SERVER --resource-group $RESOURCE_GROUP \
     --location $LOCATION --admin-user $SQL_ADMIN --admin-password '<YourPassword>'
   az sql db create --resource-group $RESOURCE_GROUP --server $SQL_SERVER \
     --name $SQL_DB --service-objective S0

   # Allow Azure services to connect
   az sql server firewall-rule create --resource-group $RESOURCE_GROUP \
     --server $SQL_SERVER --name AllowAzure --start-ip-address 0.0.0.0 --end-ip-address 0.0.0.0

   # Create App Service Plan + Web App
   az appservice plan create --name $APP_SERVICE_PLAN --resource-group $RESOURCE_GROUP \
     --location $LOCATION --sku B1 --is-linux
   az webapp create --resource-group $RESOURCE_GROUP --plan $APP_SERVICE_PLAN \
     --name $WEB_APP --runtime "DOTNETCORE:8.0"
   ```

2. **Configure connection string** — prompt Copilot:
   > "Update the app to read the database connection string from an environment variable or Azure App Service configuration, and generate the az webapp config command."

   ```bash
   az webapp config connection-string set --resource-group $RESOURCE_GROUP \
     --name $WEB_APP --connection-string-type SQLAzure \
     --settings DefaultConnection="Server=tcp:${SQL_SERVER}.database.windows.net,1433;Database=${SQL_DB};User ID=${SQL_ADMIN};Password=<YourPassword>;Encrypt=true;Connection Timeout=30;"
   ```

3. **Deploy the application:**
   ```bash
   # Publish the app
   dotnet publish -c Release -o ./publish

   # Deploy to Azure (from the publish folder)
   cd ./publish
   zip -r ../app.zip .
   cd ..
   az webapp deploy --resource-group $RESOURCE_GROUP --name $WEB_APP \
     --src-path app.zip --type zip
   ```

4. **Verify the live deployment:**
   ```bash
   az webapp browse --resource-group $RESOURCE_GROUP --name $WEB_APP
   ```

5. **Prompt Copilot for deployment validation:**
   > "Check the deployed application for common Azure deployment issues — missing connection strings, CORS, or startup errors."

**✅ Key learning:**
> Copilot can generate infrastructure-as-code and deployment scripts, not just application code.

**🧹 Cleanup (after workshop):**
```bash
az group delete --name $RESOURCE_GROUP --yes --no-wait
```

---

## 📚 Additional Resources

- [.NET Modernization Guide](https://dotnet.microsoft.com/en-us/platform/modernize)
- [GitHub Copilot Documentation](https://docs.github.com/en/copilot)
- [GitHub Copilot App Modernization](https://github.com/dotnet/modernize-dotnet)
- [Azure App Service Documentation](https://learn.microsoft.com/en-us/azure/app-service/)
- [Azure SQL Database Quickstart](https://learn.microsoft.com/en-us/azure/azure-sql/database/single-database-create-quickstart)

---

## 📁 Repository Structure

```
dotnet-migration-copilot-samples/
├── ContosoUniversity/         # The sample .NET Framework 4.8 app
│   ├── Controllers/           # MVC Controllers
│   ├── Models/                # Data models
│   ├── Views/                 # Razor views
│   ├── Data/                  # EF Core context & initializer
│   ├── Services/              # Notification service (MSMQ)
│   ├── Web.config             # Legacy configuration
│   ├── packages.config        # NuGet packages
│   └── ContosoUniversity.csproj
├── .github/
│   ├── agents/                # Copilot agent definitions
│   │   └── modernize-dotnet.agent.md
│   └── workflows/             # CI setup
└── README.md                  # ← You are here
```
