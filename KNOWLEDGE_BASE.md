# eShopLite Knowledge Base

This file is a concise, practical knowledge base for the eShopLite repository.

## 1. Repository Purpose

eShopLite is a scenario-driven monorepo of .NET 9 + .NET Aspire eCommerce reference applications.

The repository demonstrates:

- Keyword and semantic search patterns
- Azure OpenAI and alternative model integrations
- Vector data backends (in-memory, Azure AI Search, Chroma DB, SQL Server vector support)
- Agent and Model Context Protocol (MCP) patterns
- Azure deployment patterns with `azd`

## 2. High-Level Architecture

Each scenario in `scenarios/` is a mostly self-contained solution.

Typical structure inside each scenario `src/`:

- `eShopAppHost`: .NET Aspire orchestration entry point
- `Products`: backend API
- `Store`: Blazor UI frontend
- `*Entities` projects: shared domain/data contracts
- `eShopServiceDefaults`: shared Aspire service defaults
- `*.Tests`: unit/integration test projects (scenario-dependent)

Common pattern:

1. Start from `eShopAppHost`.
2. Aspire wires service discovery, endpoints, and dependencies.
3. Open the Aspire dashboard and service URLs from run output.

## 3. Scenario Catalog

### Core scenarios

- `01-SemanticSearch`: baseline keyword + semantic search
- `02-AzureAISearch`: Azure AI Search vector/hybrid pattern
- `03-RealtimeAudio`: realtime audio interactions with GPT-4o Realtime API
- `04-chromadb`: Chroma DB semantic search backend
- `05-deepseek`: DeepSeek reasoning model integration
- `06-mcp`: MCP servers/clients and tool invocation
- `07-AgentsConcurrent`: concurrent multi-agent orchestration
- `08-Sql2025`: SQL Server vector search/index usage
- `09-AzureAppService`: Azure App Service deployment variant
- `10-A2ANet`: agent-to-agent (A2A) communication patterns
- `11-GitHubModels`: local GitHub Models with cloud switch to Azure OpenAI
- `12-AzureFunctions`: Azure Functions facade and deployment boundary
- `14-MAFDevUI`: MAF Development UI scenario variant

## 4. Prerequisites

- .NET SDK 9.0+
- .NET Aspire workload
- Docker Desktop or Podman
- Azure Developer CLI (`azd`) for Azure provisioning/deployment
- Azure subscription permissions for provisioning resources

Install Aspire workload:

```bash
dotnet workload install aspire
```

## 5. Local Development Workflow

### 5.1 Run a scenario

```bash
cd scenarios/<scenario>/src/eShopAppHost
dotnet run
```

What to expect:

- Aspire dashboard URL appears in console
- Service URLs (Store, APIs) are shown in output/dashboard
- Dependencies are orchestrated automatically

### 5.2 Configure secrets (example)

Most scenarios need OpenAI/Azure service connection strings via user secrets in the relevant AppHost:

```bash
cd scenarios/<scenario>/src/eShopAppHost
dotnet user-secrets set "ConnectionStrings:openai" "Endpoint=https://<endpoint>.openai.azure.com/;Key=<key>"
```

Some scenarios may also require additional secrets (for example, AI Search).
Always follow the scenario README for exact keys.

## 6. Build, Test, and Formatting

### 6.1 Build

```bash
dotnet build
```

Or per scenario:

```bash
cd scenarios/<scenario>/src
dotnet build
```

### 6.2 Test

```bash
dotnet test
```

Or per scenario:

```bash
cd scenarios/<scenario>/src
dotnet test
```

### 6.3 Format

```bash
dotnet format
dotnet format --verify-no-changes
```

## 7. Azure Deployment Workflow

From scenario root or its AppHost folder (depending on scenario layout):

```bash
azd auth login
azd up
```

Useful follow-up commands:

```bash
azd deploy
azd monitor
azd down
```

Notes:

- `azd up` provisions Azure resources and deploys services
- Managed Identity is used in deployed environments when configured by the scenario
- Region/model availability matters for model-specific scenarios

## 8. Important Conventions

- Orchestration belongs in `eShopAppHost/Program.cs`
- Aspire resource names are generally lowercase logical identifiers
- Keep secrets out of source control; use user-secrets locally
- Prefer scenario-local changes unless intentionally modifying shared patterns
- Read each scenario's `README.md` before changing configuration or deployment logic

## 9. Troubleshooting Checklist

### Missing OpenAI connection string

- Set required `ConnectionStrings:*` user-secrets in the scenario AppHost

### Container runtime issues

- Ensure Docker/Podman is installed and running before `dotnet run`

### Port confusion

- Use Aspire dashboard and console output for actual runtime URLs

### Azure deployment failures

- Re-check `azd auth login`
- Verify subscription permissions and selected region/service availability

### Test instability after code changes

- Run `dotnet restore`, then `dotnet build`, then `dotnet test`

## 10. Where to Look First

- Repository overview: `README.md`
- Project conventions and workflow guidance: `AGENTS.md`
- Scenario setup/details: `scenarios/<scenario>/README.md`
- Deep-dive docs: `scenarios/<scenario>/docs/`

## 11. Suggested Onboarding Path

1. Start with `scenarios/01-SemanticSearch` to learn the baseline architecture.
2. Move to `scenarios/02-AzureAISearch` and `scenarios/03-RealtimeAudio` for cloud/AI extensions.
3. Explore agent patterns in `scenarios/06-mcp`, `07-AgentsConcurrent`, and `10-A2ANet`.
4. Review deployment-oriented scenarios `09-AzureAppService` and `12-AzureFunctions`.

## 12. Knowledge Base Maintenance

When adding or changing a scenario, update this file with:

- Scenario purpose and key technologies
- New required secrets/configuration
- New build/test/deploy caveats
- Operational troubleshooting notes