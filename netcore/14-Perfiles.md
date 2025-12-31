# Configuración de Entornos en ASP.NET Core

- [Configuración de Entornos en ASP.NET Core](#configuración-de-entornos-en-aspnet-core)
  - [Introducción a los Entornos](#introducción-a-los-entornos)
    - [¿Qué son los Entornos?](#qué-son-los-entornos)
    - [Entornos Predefinidos](#entornos-predefinidos)
  - [Configuración por Entorno](#configuración-por-entorno)
    - [Estructura de Archivos](#estructura-de-archivos)
    - [Configuración Base (appsettings.json)](#configuración-base-appsettingsjson)
    - [Configuración de Desarrollo (appsettings.Development.json)](#configuración-de-desarrollo-appsettingsdevelopmentjson)
    - [Configuración de Producción (appsettings. Production.json)](#configuración-de-producción-appsettings-productionjson)
  - [Configuración de Base de Datos por Entorno](#configuración-de-base-de-datos-por-entorno)
    - [Development (SQL Server LocalDB o SQLite)](#development-sql-server-localdb-o-sqlite)
    - [Production (SQL Server)](#production-sql-server)
  - [Variables de Entorno](#variables-de-entorno)
    - [Configurar en launchSettings.json](#configurar-en-launchsettingsjson)
    - [Configurar en Sistema Operativo](#configurar-en-sistema-operativo)
    - [Configurar en Docker](#configurar-en-docker)
  - [Uso de IWebHostEnvironment](#uso-de-iwebhostenvironment)
    - [En Program.cs](#en-programcs)
    - [En Controladores](#en-controladores)
    - [En Servicios](#en-servicios)
  - [Configuración de Swagger por Entorno](#configuración-de-swagger-por-entorno)
  - [Configuración de Logging por Entorno](#configuración-de-logging-por-entorno)
  - [Secretos de Usuario (User Secrets)](#secretos-de-usuario-user-secrets)
    - [Inicializar User Secrets](#inicializar-user-secrets)
    - [Agregar Secretos](#agregar-secretos)
    - [Usar Secretos](#usar-secretos)
  - [Configuración Avanzada](#configuración-avanzada)
    - [Clases de Configuración Tipadas](#clases-de-configuración-tipadas)
    - [Validación de Configuración](#validación-de-configuración)
  - [Azure App Configuration (Opcional)](#azure-app-configuration-opcional)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![Environments Banner](../images/banner15.png)

---

## Introducción a los Entornos

### ¿Qué son los Entornos?

Los **entornos** (environments) en ASP.NET Core permiten configurar la aplicación de manera diferente según dónde se ejecute (desarrollo, pruebas, producción).

**Beneficios:**
- ✅ Configuración específica por entorno
- ✅ Base de datos diferente según entorno
- ✅ Logging adaptado a cada entorno
- ✅ Swagger solo en desarrollo
- ✅ Secretos seguros en producción

---

### Entornos Predefinidos

ASP.NET Core tiene tres entornos predefinidos: 

| Entorno | Descripción | Uso |
|: --------|:------------|:----|
| **Development** | Desarrollo local | Debugging, Swagger, logs detallados |
| **Staging** | Pre-producción | Pruebas finales antes de producción |
| **Production** | Producción | Configuración optimizada y segura |

---

## Configuración por Entorno

### Estructura de Archivos

```
FunkosApi/
├── appsettings.json                    # Configuración base
├── appsettings. Development.json        # Configuración de desarrollo
├── appsettings. Staging.json            # Configuración de staging
└── appsettings.Production. json         # Configuración de producción
```

**Prioridad de carga:**
```
appsettings.json
    ↓ (sobrescribe)
appsettings.{Environment}.json
    ↓ (sobrescribe)
Variables de entorno
    ↓ (sobrescribe)
User Secrets (solo Development)
```

---

### Configuración Base (appsettings.json)

```json
{
  "Logging": {
    "LogLevel":  {
      "Default": "Information",
      "Microsoft. AspNetCore": "Warning"
    }
  },
  "AllowedHosts": "*",
  "ApplicationName": "Funkos API",
  "Jwt": {
    "Issuer": "https://localhost:5001",
    "Audience":  "https://localhost:5001",
    "ExpirationInMinutes": 60
  },
  "Pagination": {
    "DefaultPageSize": 10,
    "MaxPageSize": 100
  }
}
```

---

### Configuración de Desarrollo (appsettings.Development.json)

```json
{
  "Logging": {
    "LogLevel": {
      "Default":  "Debug",
      "Microsoft.AspNetCore": "Information",
      "Microsoft.EntityFrameworkCore": "Information"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Server=(localdb)\\mssqllocaldb;Database=FunkosDb_Dev;Trusted_Connection=true;MultipleActiveResultSets=true",
    "MongoConnection": "mongodb://localhost:27017/FunkosDb_Dev"
  },
  "Jwt": {
    "Secret": "clave-secreta-desarrollo-muy-larga-y-segura-de-al-menos-32-caracteres"
  },
  "EnableSwagger": true,
  "EnableDetailedErrors": true,
  "Cors": {
    "AllowedOrigins": ["http://localhost:3000", "http://localhost:4200"]
  }
}
```

---

### Configuración de Producción (appsettings. Production.json)

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft.AspNetCore": "Error",
      "Microsoft.EntityFrameworkCore": "Error"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Server=prod-server. database.windows.net;Database=FunkosDb;User Id=admin;Password=***;",
    "MongoConnection": "mongodb+srv://user:***@cluster. mongodb.net/FunkosDb"
  },
  "Jwt": {
    "Secret": "${JWT_SECRET}"
  },
  "EnableSwagger": false,
  "EnableDetailedErrors": false,
  "Cors": {
    "AllowedOrigins": ["https://miaplicacion.com"]
  }
}
```

---

## Configuración de Base de Datos por Entorno

### Development (SQL Server LocalDB o SQLite)

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

if (builder.Environment.IsDevelopment())
{
    // SQLite para desarrollo
    builder.Services.AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlite(builder.Configuration.GetConnectionString("DefaultConnection"))
               .EnableSensitiveDataLogging()
               .EnableDetailedErrors()
    );
}
```

**appsettings.Development.json:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Data Source=funkos_dev.db"
  }
}
```

---

### Production (SQL Server)

```csharp
if (builder.Environment.IsProduction())
{
    // SQL Server para producción
    builder.Services. AddDbContext<ApplicationDbContext>(options =>
        options.UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
    );
}
```

**appsettings.Production.json:**
```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=prod-server;Database=FunkosDb;User Id=admin;Password=${DB_PASSWORD};"
  }
}
```

---

## Variables de Entorno

### Configurar en launchSettings.json

```json
{
  "profiles": {
    "Development": {
      "commandName":  "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "launchUrl": "swagger",
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development",
        "JWT_SECRET": "clave-desarrollo",
        "DB_PASSWORD": "dev-password"
      }
    },
    "Production": {
      "commandName":  "Project",
      "launchBrowser": false,
      "applicationUrl": "https://localhost:5001",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Production"
      }
    }
  }
}
```

---

### Configurar en Sistema Operativo

**Windows:**
```cmd
setx ASPNETCORE_ENVIRONMENT "Production"
setx JWT_SECRET "mi-clave-secreta-produccion"
```

**Linux/Mac:**
```bash
export ASPNETCORE_ENVIRONMENT=Production
export JWT_SECRET=mi-clave-secreta-produccion
```

---

### Configurar en Docker

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS base
WORKDIR /app
EXPOSE 80
EXPOSE 443

ENV ASPNETCORE_ENVIRONMENT=Production
ENV JWT_SECRET=${JWT_SECRET}

FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src
COPY ["FunkosApi/FunkosApi.csproj", "FunkosApi/"]
RUN dotnet restore "FunkosApi/FunkosApi.csproj"
COPY . .
WORKDIR "/src/FunkosApi"
RUN dotnet build "FunkosApi.csproj" -c Release -o /app/build

FROM build AS publish
RUN dotnet publish "FunkosApi. csproj" -c Release -o /app/publish

FROM base AS final
WORKDIR /app
COPY --from=publish /app/publish .
ENTRYPOINT ["dotnet", "FunkosApi.dll"]
```

**docker-compose.yml:**
```yaml
version: '3.8'
services:
  funkosapi:
    image: funkosapi: latest
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - JWT_SECRET=${JWT_SECRET}
      - ConnectionStrings__DefaultConnection=Server=db;Database=FunkosDb;User=sa;Password=${DB_PASSWORD};
    ports:
      - "8080:80"
    depends_on:
      - db
  
  db:
    image: mcr.microsoft.com/mssql/server:2022-latest
    environment:
      - ACCEPT_EULA=Y
      - SA_PASSWORD=${DB_PASSWORD}
    ports:
      - "1433:1433"
```

---

## Uso de IWebHostEnvironment

### En Program.cs

```csharp
var builder = WebApplication. CreateBuilder(args);

// Acceder al entorno
var env = builder. Environment;

// Configurar según entorno
if (env.IsDevelopment())
{
    Console.WriteLine("🛠️  Ejecutando en modo DESARROLLO");
    builder.Services.AddDatabaseDeveloperPageExceptionFilter();
}
else if (env.IsProduction())
{
    Console.WriteLine("🚀 Ejecutando en modo PRODUCCIÓN");
}

var app = builder.Build();

// Configuración condicional
if (app.Environment.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
    app.UseSwagger();
    app.UseSwaggerUI();
}
else
{
    app.UseExceptionHandler("/Error");
    app.UseHsts();
}
```

---

### En Controladores

```csharp
[ApiController]
[Route("api/[controller]")]
public class DiagnosticController : ControllerBase
{
    private readonly IWebHostEnvironment _environment;
    private readonly IConfiguration _configuration;

    public DiagnosticController(IWebHostEnvironment environment, IConfiguration configuration)
    {
        _environment = environment;
        _configuration = configuration;
    }

    [HttpGet("info")]
    public ActionResult<object> GetInfo()
    {
        return Ok(new
        {
            Environment = _environment.EnvironmentName,
            ApplicationName = _environment.ApplicationName,
            ContentRootPath = _environment.ContentRootPath,
            IsDevelopment = _environment.IsDevelopment(),
            ConnectionString = _configuration.GetConnectionString("DefaultConnection")
        });
    }

    [HttpGet("config")]
    public ActionResult<object> GetConfig()
    {
        if (! _environment.IsDevelopment())
        {
            return Forbid();
        }

        return Ok(new
        {
            JwtSecret = _configuration["Jwt:Secret"],
            EnableSwagger = _configuration.GetValue<bool>("EnableSwagger"),
            AllowedHosts = _configuration["AllowedHosts"]
        });
    }
}
```

---

### En Servicios

```csharp
public class EmailService : IEmailService
{
    private readonly IWebHostEnvironment _environment;
    private readonly ILogger<EmailService> _logger;

    public EmailService(IWebHostEnvironment environment, ILogger<EmailService> logger)
    {
        _environment = environment;
        _logger = logger;
    }

    public async Task SendEmailAsync(string to, string subject, string body)
    {
        if (_environment.IsDevelopment())
        {
            // En desarrollo, solo loguear
            _logger.LogInformation("📧 [DEV] Email a {To}:  {Subject}", to, subject);
            await Task.CompletedTask;
        }
        else
        {
            // En producción, enviar email real
            _logger.LogInformation("📧 [PROD] Enviando email a {To}", to);
            // await _emailClient.SendAsync(... );
        }
    }
}
```

---

## Configuración de Swagger por Entorno

```csharp
var builder = WebApplication.CreateBuilder(args);

// Configurar Swagger solo en Development
if (builder.Environment.IsDevelopment())
{
    builder.Services.AddEndpointsApiExplorer();
    builder.Services.AddSwaggerGen(options =>
    {
        options. SwaggerDoc("v1", new OpenApiInfo
        {
            Title = "Funkos API - Development",
            Version = "v1",
            Description = "API en modo desarrollo con todas las herramientas habilitadas"
        });
    });
}

var app = builder.Build();

// Habilitar Swagger solo en Development
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(options =>
    {
        options.SwaggerEndpoint("/swagger/v1/swagger.json", "Funkos API V1");
        options.RoutePrefix = "swagger";
    });
}
```

---

## Configuración de Logging por Entorno

**appsettings.Development.json:**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft":  "Information",
      "Microsoft. Hosting.Lifetime": "Information",
      "Microsoft.EntityFrameworkCore": "Information"
    },
    "Console": {
      "IncludeScopes":  true,
      "TimestampFormat": "HH:mm:ss "
    }
  }
}
```

**appsettings.Production.json:**
```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft": "Error",
      "Microsoft.Hosting.Lifetime": "Information"
    },
    "ApplicationInsights": {
      "LogLevel": {
        "Default": "Information"
      }
    }
  }
}
```

---

## Secretos de Usuario (User Secrets)

### Inicializar User Secrets

```bash
dotnet user-secrets init
```

**Se agrega al . csproj:**
```xml
<PropertyGroup>
  <UserSecretsId>aspnet-FunkosApi-12345</UserSecretsId>
</PropertyGroup>
```

---

### Agregar Secretos

```bash
dotnet user-secrets set "Jwt:Secret" "mi-clave-secreta-desarrollo"
dotnet user-secrets set "ConnectionStrings:DefaultConnection" "Server=... ;Password=secret;"
```

---

### Usar Secretos

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

if (builder.Environment.IsDevelopment())
{
    builder.Configuration.AddUserSecrets<Program>();
}

// Acceder a secretos
var jwtSecret = builder.Configuration["Jwt:Secret"];
```

---

## Configuración Avanzada

### Clases de Configuración Tipadas

```csharp
// Clase de configuración
public class JwtSettings
{
    public string Secret { get; set; } = string.Empty;
    public string Issuer { get; set; } = string.Empty;
    public string Audience { get; set; } = string.Empty;
    public int ExpirationInMinutes { get; set; } = 60;
}

// Registrar configuración
builder.Services.Configure<JwtSettings>(
    builder.Configuration.GetSection("Jwt")
);

// Uso en servicios
public class JwtService
{
    private readonly JwtSettings _settings;

    public JwtService(IOptions<JwtSettings> settings)
    {
        _settings = settings.Value;
    }
}
```

---

### Validación de Configuración

```csharp
public class JwtSettings
{
    public string Secret { get; set; } = string. Empty;

    public void Validate()
    {
        if (string.IsNullOrEmpty(Secret))
            throw new InvalidOperationException("JWT Secret no configurado");

        if (Secret.Length < 32)
            throw new InvalidOperationException("JWT Secret debe tener al menos 32 caracteres");
    }
}

// Validar al inicio
var jwtSettings = builder.Configuration.GetSection("Jwt").Get<JwtSettings>();
jwtSettings?. Validate();
```

---

## Azure App Configuration (Opcional)

```bash
dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
```

```csharp
builder.Configuration.AddAzureAppConfiguration(options =>
{
    options.Connect(builder.Configuration["ConnectionStrings:AppConfig"])
           .Select(KeyFilter.Any, LabelFilter. Null)
           .Select(KeyFilter.Any, builder.Environment. EnvironmentName);
});
```

---

## Buenas Prácticas

✅ **Nunca commits secretos**:  Usa User Secrets o variables de entorno

✅ **Separar configuración por entorno**: Un archivo por entorno

✅ **Validar configuración**: Validar al inicio de la aplicación

✅ **Usar variables de entorno en producción**: No hardcodear secretos

✅ **Swagger solo en desarrollo**: No exponer en producción

✅ **Logs apropiados**: Detallados en dev, mínimos en prod

✅ **Base de datos diferente**: No usar misma BD en dev y prod

✅ **Documentar configuración**: Documentar todas las variables requeridas

---

## Práctica de Clase

**Objetivo**: Configurar entornos de desarrollo y producción para la API de Funkos.

**Tareas:**

1. ✅ Crear `appsettings.Development.json` con:
   - SQLite o LocalDB
   - Logs detallados
   - Swagger habilitado
   - CORS permisivo
2. ✅ Crear `appsettings.Production.json` con:
   - SQL Server
   - Logs mínimos
   - Swagger deshabilitado
   - CORS restrictivo
3. ✅ Configurar User Secrets para JWT secret en desarrollo
4. ✅ Usar `IWebHostEnvironment` para comportamiento condicional
5. ✅ Crear endpoint `/api/diagnostic/info` que muestre información del entorno (solo en dev)
6. ✅ Configurar variables de entorno para producción
7. ✅ Documentar todas las variables de configuración en README

**Criterios de evaluación:**

- ✅ Configuración correcta de Development y Production
- ✅ Base de datos diferente por entorno
- ✅ Swagger solo en desarrollo
- ✅ User Secrets configurados
- ✅ Variables de entorno documentadas
- ✅ No hay secretos en el repositorio
- ✅ Aplicación funciona en ambos entornos

---

## Proyecto del Curso

Puedes encontrar el proyecto con configuración de entornos en el repositorio del curso.