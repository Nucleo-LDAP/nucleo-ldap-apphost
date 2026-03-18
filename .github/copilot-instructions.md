# Copilot Instructions for `nucleo-ldap-apphost`

## Build, test, and lint commands

This repository is a .NET Aspire AppHost solution under `src/`.

Toolchain versions are pinned via `global.json`:

- .NET SDK: `10.0.104`
- MSBuild SDK `Aspire.AppHost.Sdk`: `13.1.2`

- Restore + build solution:
  - `cd src && dotnet build Nucleo.Ldap.AppHost.slnx -v minimal`
- Run tests (current baseline command):
  - `cd src && dotnet test Nucleo.Ldap.AppHost.slnx -v minimal`
- Run a single test (when test projects exist):
  - `cd src && dotnet test Nucleo.Ldap.AppHost.slnx --filter "FullyQualifiedName~Namespace.ClassName.TestName" -v minimal`

There is currently no dedicated test project in the solution; `dotnet test` succeeds with zero tests discovered.

No lint-specific configuration was found in this repository (no custom linter config files).

## High-level architecture

This repo currently contains one solution (`src/Nucleo.Ldap.AppHost.slnx`) with a single AppHost project (`src/Nucleo.Ldap.AppHost/Nucleo.Ldap.AppHost.csproj`).

- The project uses `Aspire.AppHost.Sdk` and targets `net10.0`.
- `AppHost.cs` is the entry point and currently creates and runs an empty distributed application host:
  - `DistributedApplication.CreateBuilder(args)`
  - `builder.Build().Run()`
- Runtime/development behavior is shaped mostly by configuration files:
  - `Properties/launchSettings.json` defines `http` and `https` profiles and Aspire dashboard/resource endpoint environment variables.
  - `appsettings.json` and `appsettings.Development.json` define logging defaults and Development-specific logging overrides.

In practice, this is a scaffolded Aspire orchestration host ready to have resources/projects added in `AppHost.cs`.

## Key repository conventions

- Solution-level commands are run from `src/` against `Nucleo.Ldap.AppHost.slnx`, not from project folder by default.
- AppHost startup uses top-level statements in `AppHost.cs` (no explicit `Program` class).
- Environment selection in launch profiles sets both:
  - `ASPNETCORE_ENVIRONMENT`
  - `DOTNET_ENVIRONMENT`
- Aspire local orchestration endpoints are configured via launch profile environment variables:
  - `ASPIRE_DASHBOARD_OTLP_ENDPOINT_URL`
  - `ASPIRE_DASHBOARD_MCP_ENDPOINT_URL`
  - `ASPIRE_RESOURCE_SERVICE_ENDPOINT_URL`
- Development logging explicitly downgrades `Aspire.Hosting.Dcp` to `Warning` in `appsettings.Development.json`.
