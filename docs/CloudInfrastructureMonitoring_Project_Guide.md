# Cloud Infrastructure Monitoring System

## Software Design Document (SDD) & Complete Setup Guide

**Version:** 1.0  
**Date:** 2026-10-16  
**Author:** Nikita Pandey  
**Repository:** [CloudInfrastructureMonitoring](https://github.com/nikitapandey03/CloudInfrastructureMonitoring)  
**Status:** In Progress

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Project Goals](#2-project-goals)
3. [Technology Stack](#3-technology-stack)
4. [How the System Works](#4-how-the-system-works)
5. [Development Roadmap](#5-development-roadmap)
6. [Folder Structure](#6-folder-structure)
7. [Phase 1 — Setup Steps](#7-phase-1--setup-steps)
8. [Git & GitHub Workflow](#8-git--github-workflow)
9. [Learning Rules](#9-learning-rules)
10. [Definition of Done](#10-definition-of-done)

---

## 1. Project Overview

Yeh project ek monitoring system banayega jo **web applications aur APIs ki health, availability, aur response time** track karega. Pehle local computer par working version banega. Uske baad database, metrics dashboard, containers, CI/CD, aur cloud deployment add karenge.

---

## 2. Project Goals

- Health endpoints se check karna ki application respond kar rahi hai ya nahi
- API response time aur request status jaise monitoring data ko record karna
- Monitoring history ko SQL Server mein store karna
- Prometheus se metrics collect karna aur Grafana mein dashboard banana
- Docker container banana aur automated build/test workflow set karna
- Later stage mein AWS/Kubernetes deployment explore karna

---

## 3. Technology Stack

| Technology | Purpose |
|------------|---------|
| C# / .NET 8 | Application logic and runtime |
| ASP.NET Core Web API | HTTP endpoints and API responses |
| Health Checks | Application dependency and health status checks |
| SQL Server + EF Core | Store monitoring history |
| Prometheus | Collect time-series metrics |
| Grafana | Visualize metrics in dashboards |
| Docker | Package the application into a container |
| GitHub Actions | Automate build and tests |
| AWS + Kubernetes | Later deployment and orchestration stage |

---

## 4. How the System Works

1. A monitoring endpoint or background checker requests a target application's health endpoint
2. The target responds with a status such as Healthy or Unhealthy
3. The monitoring service measures response duration and records the result
4. The result is saved in SQL Server when database support is added
5. Prometheus collects metrics; Grafana displays charts and trends
6. Later, alert rules and cloud deployment can be added

---

## 5. Development Roadmap

| Phase | Description | Status |
|-------|-------------|--------|
| Phase 1 | Project Setup | ✅ Complete |
| Phase 2 | Health Checks | ⏳ Pending |
| Phase 3 | Response-Time Monitoring | ⏳ Pending |
| Phase 4 | SQL Server + EF Core | ⏳ Pending |
| Phase 5 | Prometheus + Grafana | ⏳ Pending |
| Phase 6 | Docker + CI | ⏳ Pending |
| Phase 7 | AWS/Kubernetes | ⏳ Pending |

---

## 6. Folder Structure

```
CloudInfrastructureMonitoring/
├── CloudInfrastructureMonitoring.sln
├── src/
│   └── CloudInfrastructureMonitoring.Api/
│       ├── Program.cs
│       ├── appsettings.json
│       ├── Controllers/
│       ├── Models/
│       ├── Services/
│       └── Properties/
├── docs/
│   ├── CloudInfrastructureMonitoring_Project_Guide.md
│   ├── Cloud_Infrastructure_Monitoring_Project_Guide.docx
│   ├── SETUP_STEPS.md
│   └── GIT_GUIDE.md
├── tests/
│   └── CloudInfrastructureMonitoring.Tests/
├── docker-compose.yml
├── Dockerfile
└── README.md
```

---

## 7. Phase 1 — Setup Steps

### 7.1 Prerequisites

- .NET 8 SDK (ya .NET 10 SDK with net8.0 target)
- Git
- GitHub account
- VS Code ya Visual Studio

### 7.2 .NET SDK Check

```bash
dotnet --version
dotnet --list-sdks
```

### 7.3 Project Structure Banao

```bash
mkdir CloudInfrastructureMonitoring
cd CloudInfrastructureMonitoring
dotnet new sln -n CloudInfrastructureMonitoring
mkdir src
cd src
dotnet new webapi -n CloudInfrastructureMonitoring.Api --framework net8.0
cd ..
dotnet sln add src/CloudInfrastructureMonitoring.Api/CloudInfrastructureMonitoring.Api.csproj
```

### 7.4 Extra Folders Banao

```bash
cd src/CloudInfrastructureMonitoring.Api
mkdir Controllers Models Services
cd ../../..
```

### 7.5 Warning Fix — Microsoft.OpenApi

`.csproj` file mein version `3.x` se `2.10.0` karo:

```xml
<PackageReference Include="Microsoft.OpenApi" Version="2.10.0" />
```

### 7.6 Swagger UI Add Karo

```bash
cd src/CloudInfrastructureMonitoring.Api
dotnet add package Swashbuckle.AspNetCore
```

`Program.cs` update karo:

```csharp
var builder = WebApplication.CreateBuilder(args);

builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();

var summaries = new[]
{
    "Freezing", "Bracing", "Chilly", "Cool", "Mild", "Warm", "Balmy", "Hot", "Sweltering", "Scorching"
};

app.MapGet("/weatherforecast", () =>
{
    var forecast = Enumerable.Range(1, 5).Select(index =>
        new WeatherForecast
        (
            DateOnly.FromDateTime(DateTime.Now.AddDays(index)),
            Random.Shared.Next(-20, 55),
            summaries[Random.Shared.Next(summaries.Length)]
        ))
        .ToArray();
    return forecast;
})
.WithName("GetWeatherForecast");

app.Run();

record WeatherForecast(DateOnly Date, int TemperatureC, string? Summary)
{
    public int TemperatureF => 32 + (int)(TemperatureC / 0.5556);
}
```

### 7.7 Project Run Karo

```bash
dotnet run
```

### 7.8 Browser Mein Test Karo

- `http://localhost:5182/weatherforecast` → JSON data
- `http://localhost:5182/swagger` → Swagger UI

---

## 8. Git & GitHub Workflow

### 8.1 First-Time Setup

```bash
winget install --id Git.Git -e --source winget
git --version
git config --global user.name "nikitapandey03"
git config --global user.email "pandeynikita751@gmail.com"
```

### 8.2 Local to GitHub — First Time Push

```bash
cd C:/Users/deepc/source/repos/CloudInfrastructureMonitoring
dotnet new gitignore
git init
git add .
git commit -m "Phase 1: Initial project setup with API and Swagger"
git remote add origin https://github.com/nikitapandey03/CloudInfrastructureMonitoring.git
git branch -M main
git push -u origin main
```

### 8.3 Remote to Local — Clone

```bash
git clone https://github.com/nikitapandey03/CloudInfrastructureMonitoring.git
cd CloudInfrastructureMonitoring
git pull origin main
```

### 8.4 Update Workflow — Daily

```bash
git pull origin main
# <code changes karo>
git status
git add .
git commit -m "feat: Add health check endpoint"
git push origin main
```

### 8.5 Commit Message Conventions

| Prefix | Matlab |
|--------|--------|
| `feat:` | Naya feature |
| `fix:` | Bug fix |
| `docs:` | Documentation |
| `refactor:` | Code refactoring |
| `test:` | Tests |
| `chore:` | Maintenance |

### 8.6 Common Errors & Fixes

**Error: `failed to push some refs`**

```bash
git pull origin main --allow-unrelated-histories
git push origin main
```

**Error: `remote origin already exists`**

```bash
git remote remove origin
git remote add origin https://github.com/nikitapandey03/CloudInfrastructureMonitoring.git
```

**Error: Authentication failed**

```bash
winget install --id GitHub.cli
gh auth login
```

**Error: Merge conflict**

```bash
git status
git checkout --theirs <file>
git add <file>
git commit -m "Merge conflict resolved"
```

---

## 9. Learning Rules

- Pehle concept samjhenge, phir code likhenge
- Har command aur important code line ka purpose explain hoga
- Ek phase complete aur test hone ke baad hi next phase par jayenge
- Agar error aaye, exact error message ya screenshot share karna
- Resume/GitHub par kisi technology ko hands-on experience tabhi likhna jab project mein use karke samajh liya ho

---

## 10. Definition of Done

Project complete tab maana jayega jab:

- ✅ Local API health checks aur monitoring records ke saath kaam kare
- ✅ Data persist ho (SQL Server)
- ✅ Metrics dashboard visible ho (Grafana)
- ✅ Tests/build pass hon
- ✅ Deployment steps README mein documented hon
- ⚠️ Cloud deployment optional final phase hai
- ⚠️ Credentials ko source code mein commit nahi karna hai

---

## Quick Reference — Git Commands

```bash
git status
git add .
git commit -m "message"
git push origin main
git pull origin main
git remote -v
git log --oneline
git branch -a
git checkout -b feature/name
git checkout main
git merge feature/name
```

---

**Start with Phase 1. Build slowly, understand deeply.**

---

*Document Version: 1.0 | Last Updated: 2026-10-16*