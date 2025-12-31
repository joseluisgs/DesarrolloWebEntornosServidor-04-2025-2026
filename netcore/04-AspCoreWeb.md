# ASP.NET Core Web API

- [ASP.NET Core Web API](#aspnet-core-web-api)
  - [Creando un proyecto](#creando-un-proyecto)
    - [Opción 1: Usando la interfaz gráfica de Rider](#opción-1-usando-la-interfaz-gráfica-de-rider)
    - [Opción 2: Usando la terminal integrada de Rider](#opción-2-usando-la-terminal-integrada-de-rider)
    - [Opción 3: Usando Rider CLI Tools](#opción-3-usando-rider-cli-tools)
    - [Paquetes NuGet](#paquetes-nuget)
      - [Opción 1: Interfaz gráfica de Rider](#opción-1-interfaz-gráfica-de-rider)
      - [Opción 2: Terminal de Rider](#opción-2-terminal-de-rider)
      - [Opción 3: Editar manualmente el . csproj](#opción-3-editar-manualmente-el--csproj)
    - [Punto de Entrada](#punto-de-entrada)
    - [Parametrizando la aplicación](#parametrizando-la-aplicación)
      - [Opción 1: Usando `IConfiguration` directamente](#opción-1-usando-iconfiguration-directamente)
      - [Opción 2: Usando el patrón Options (recomendado)](#opción-2-usando-el-patrón-options-recomendado)
      - [Opción 1: Editar la configuración de ejecución](#opción-1-editar-la-configuración-de-ejecución)
      - [Opción 2: Terminal de Rider](#opción-2-terminal-de-rider-1)
      - [Opción 3: Archivo launchSettings.json](#opción-3-archivo-launchsettingsjson)
  - [ASP.NET Core MVC y Web API](#aspnet-core-mvc-y-web-api)
  - [Componentes de ASP.NET Core](#componentes-de-aspnet-core)
    - [1. **Controllers (Controladores)**](#1-controllers-controladores)
    - [2. **Services (Servicios)**](#2-services-servicios)
    - [3. **Repositories (Repositorios)**](#3-repositories-repositorios)
    - [4. **Models (Modelos)**](#4-models-modelos)
    - [5. **DTOs (Data Transfer Objects)**](#5-dtos-data-transfer-objects)
    - [Ciclo de vida de servicios (Service Lifetime)](#ciclo-de-vida-de-servicios-service-lifetime)
    - [IoC y DI en ASP.NET Core](#ioc-y-di-en-aspnet-core)
    - [Creando rutas (Endpoints)](#creando-rutas-endpoints)
    - [Responses](#responses)
    - [Requests](#requests)
      - [Parámetros de ruta](#parámetros-de-ruta)
      - [Parámetros de consulta (Query Parameters)](#parámetros-de-consulta-query-parameters)
      - [Peticiones con datos serializados](#peticiones-con-datos-serializados)
    - [Versionado de la API](#versionado-de-la-api)
  - [Postman](#postman)
  - [Práctica de clase: Mi primera API REST](#práctica-de-clase-mi-primera-api-rest)
    - [Paso 1: Crear el proyecto en Rider](#paso-1-crear-el-proyecto-en-rider)
    - [Paso 2: Crear el modelo `Funko`](#paso-2-crear-el-modelo-funko)
    - [Paso 3: Crear el repositorio](#paso-3-crear-el-repositorio)
    - [Paso 4: Crear el controlador](#paso-4-crear-el-controlador)
    - [Paso 5: Registrar servicios en `Program.cs`](#paso-5-registrar-servicios-en-programcs)
    - [Paso 6: Ejecutar y probar](#paso-6-ejecutar-y-probar)

![ASP.NET Core Banner](../images/banner04.png)

---

## Creando un proyecto

Podemos crear un proyecto ASP.NET Core Web API usando **JetBrains Rider** de las siguientes maneras:

### Opción 1: Usando la interfaz gráfica de Rider

1. **Abrir Rider** → `New Solution`
2. Seleccionar **ASP.NET Core Web Application**
3. Configurar: 
   - **Solution name**: `MiTiendaApi`
   - **Framework**: `.NET 10. 0`
   - **Type**: `Web API`
   - **Language**: `C#`
   - **Language Version**: `C# 14`
   - ✅ Enable OpenAPI support (Swagger)
   - ✅ Use controllers
   - ✅ Configure for HTTPS
   - ❌ Enable Docker (opcional)
   - ❌ Use top-level statements (por claridad, aunque es opcional)

4. Click en **Create**

![Rider New Project](../images/quickstart-1.png)
![Rider Configuration](../images/config. png)

### Opción 2: Usando la terminal integrada de Rider

```bash
# Abrir la terminal en Rider (Alt+F12 o View → Tool Windows → Terminal)

# Crear el proyecto
dotnet new webapi -n MiTiendaApi -f net10.0 --use-controllers

# Navegar al directorio
cd MiTiendaApi

# Abrir en Rider (o ya estará abierto)
# Ejecutar el proyecto
dotnet run
```

### Opción 3: Usando Rider CLI Tools

```bash
# Crear solución y proyecto de una vez
rider new webapi MiTiendaApi --framework net10.0 --language C# --use-controllers
```

**Configuración recomendada para nuestro proyecto:**

- **Framework**: . NET 10.0
- **Lenguaje**: C# 14
- **Tipo de proyecto**: ASP.NET Core Web API
- **Autenticación**: None (por ahora)
- **HTTPS**: Habilitado
- **Docker**: Opcional
- **OpenAPI (Swagger)**: Habilitado
- **Use Controllers**: ✅ Sí (en lugar de Minimal APIs)
- **Nullable**:  Habilitado

**Estructura básica del proyecto generado por Rider:**

```
MiTiendaApi/
├── Controllers/              # Controladores de la API
│   └── WeatherForecastController.cs
├── Models/                   # Modelos de datos (crear manualmente)
├── Services/                 # Servicios de lógica de negocio (crear manualmente)
├── Repositories/             # Repositorios de acceso a datos (crear manualmente)
├── DTOs/                     # Data Transfer Objects (crear manualmente)
├── Properties/
│   └── launchSettings. json   # Configuración de lanzamiento
├── appsettings.json          # Configuración de la aplicación
├── appsettings.Development.json
├── Program.cs                # Punto de entrada de la aplicación
├── MiTiendaApi.csproj        # Archivo del proyecto
└── MiTiendaApi.http          # Archivo de pruebas HTTP de Rider
```

---

### Paquetes NuGet

Los **paquetes NuGet** son el equivalente a los "starters" de Spring Boot. En Rider, podemos gestionarlos de varias formas:

#### Opción 1: Interfaz gráfica de Rider

1. Click derecho en el proyecto → **Manage NuGet Packages**
2. Buscar e instalar paquetes

#### Opción 2: Terminal de Rider

```bash
# Entity Framework Core para SQL Server
dotnet add package Microsoft. EntityFrameworkCore.SqlServer --version 10.0.0

# Entity Framework Core Tools (para migraciones)
dotnet add package Microsoft.EntityFrameworkCore.Tools --version 10.0.0

# Entity Framework Core Design
dotnet add package Microsoft.EntityFrameworkCore.Design --version 10.0.0

# Swagger/OpenAPI (ya incluido en la plantilla)
dotnet add package Swashbuckle.AspNetCore --version 7.0.0

# Logging con Serilog
dotnet add package Serilog.AspNetCore --version 10.0.0
dotnet add package Serilog.Sinks.Console --version 7.0.0
dotnet add package Serilog.Sinks.File --version 7.0.0

# Validación con FluentValidation
dotnet add package FluentValidation. AspNetCore --version 11.3.0

# AutoMapper para mapeo de DTOs
dotnet add package AutoMapper.Extensions.Microsoft.DependencyInjection --version 13.0.0
```

#### Opción 3: Editar manualmente el . csproj

```xml
<Project Sdk="Microsoft.NET. Sdk. Web">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    <LangVersion>14</LangVersion>
  </PropertyGroup>

  <ItemGroup>
    <!-- ASP.NET Core OpenAPI/Swagger -->
    <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="10.0.0" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="7.0.0" />
    
    <!-- Entity Framework Core -->
    <PackageReference Include="Microsoft.EntityFrameworkCore. SqlServer" Version="10.0.0" />
    <PackageReference Include="Microsoft.EntityFrameworkCore.Tools" Version="10.0.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
    <PackageReference Include="Microsoft.EntityFrameworkCore.Design" Version="10.0.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
    
    <!-- Logging -->
    <PackageReference Include="Serilog.AspNetCore" Version="10.0.0" />
    
    <!-- Validación -->
    <PackageReference Include="FluentValidation.AspNetCore" Version="11.3.0" />
    
    <!-- AutoMapper -->
    <PackageReference Include="AutoMapper.Extensions.Microsoft.DependencyInjection" Version="13.0.0" />
  </ItemGroup>

</Project>
```

**Restaurar paquetes en Rider:**

- Automático al guardar el archivo `.csproj`
- Manual:  Click derecho en el proyecto → **Restore NuGet Packages**
- Terminal: `dotnet restore`

---

### Punto de Entrada

El servidor tiene su entrada y configuración en el archivo **`Program.cs`**. Con . NET 10 y C# 14, usamos **top-level statements** por defecto (más conciso):

**Estructura de `Program.cs` con . NET 10 y C# 14:**

```csharp
// ===== CONFIGURACIÓN INICIAL =====
var builder = WebApplication.CreateBuilder(args);

// ===== CONFIGURACIÓN DE SERVICIOS =====

// Agregar controladores con opciones
builder.Services.AddControllers()
    .AddJsonOptions(options =>
    {
        options. JsonSerializerOptions.PropertyNamingPolicy = null; // PascalCase
        options.JsonSerializerOptions.WriteIndented = true; // JSON formateado
    });

// Configurar Swagger/OpenAPI con información detallada
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen(c =>
{
    c. SwaggerDoc("v1", new()
    {
        Title = "Mi Tienda API",
        Version = "v1",
        Description = "API REST para gestión de productos",
        Contact = new()
        {
            Name = "Tu Nombre",
            Email = "tu@email.com"
        }
    });
});

// Configurar CORS (Cross-Origin Resource Sharing)
builder.Services.AddCors(options =>
{
    options.AddDefaultPolicy(policy =>
    {
        policy. AllowAnyOrigin()
              .AllowAnyMethod()
              .AllowAnyHeader();
    });
});

// Registrar servicios personalizados con inyección de dependencias
builder. Services.AddScoped<IProductoRepository, ProductoRepository>();
builder.Services.AddScoped<IProductoService, ProductoService>();

// Configurar logging con Serilog (opcional)
// using Serilog;
// builder.Host.UseSerilog((context, configuration) => 
//     configuration.ReadFrom.Configuration(context.Configuration));

// ===== CONSTRUCCIÓN DE LA APLICACIÓN =====
var app = builder.Build();

// ===== CONFIGURACIÓN DEL PIPELINE DE MIDDLEWARE =====

// Swagger disponible en desarrollo y producción (opcional en producción)
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(c =>
    {
        c.SwaggerEndpoint("/swagger/v1/swagger.json", "Mi Tienda API v1");
        c.RoutePrefix = string. Empty; // Swagger en la raíz:  http://localhost:5000/
    });
}

// Middleware para redirección HTTPS
app.UseHttpsRedirection();

// Middleware de CORS
app.UseCors();

// Middleware de autenticación y autorización
app.UseAuthentication();
app.UseAuthorization();

// Mapear controladores
app.MapControllers();

// Mensaje de bienvenida (opcional, para depuración)
app.Logger.LogInformation("🚀 API iniciada en {Environment}", app.Environment.EnvironmentName);
app.Logger.LogInformation("📄 Swagger disponible en: {SwaggerUrl}", 
    app.Environment.IsDevelopment() ? "http://localhost:5000" : "https://tu-dominio.com/swagger");

// ===== EJECUTAR LA APLICACIÓN =====
app.Run();
```

**Explicación de componentes clave:**

1. **`WebApplication.CreateBuilder(args)`**: 
   - Crea el builder de la aplicación
   - Carga automáticamente configuración desde `appsettings.json`
   - Configura el logging por defecto

2. **`builder.Services`**:  
   - Contenedor de inyección de dependencias (IoC Container)
   - Aquí registramos todos los servicios, repositorios, controladores, etc.

3. **`builder.Build()`**: 
   - Construye la aplicación con toda la configuración

4. **Pipeline de Middleware**:  
   - Secuencia de componentes que procesan las solicitudes HTTP **en orden**
   - Importante:  el orden importa (`UseAuthentication` antes de `UseAuthorization`)

5. **`app.Run()`**: 
   - Inicia el servidor web Kestrel
   - Comienza a escuchar peticiones HTTP

**Configuración de ejecución en Rider:**

En Rider, la configuración de ejecución se encuentra en `Properties/launchSettings.json`:

```json
{
  "$schema": "http://json.schemastore.org/launchsettings. json",
  "profiles": {
    "http": {
      "commandName": "Project",
      "dotnetRunMessages": true,
      "launchBrowser": true,
      "launchUrl": "swagger",
      "applicationUrl": "http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT":  "Development"
      }
    },
    "https": {
      "commandName": "Project",
      "dotnetRunMessages":  true,
      "launchBrowser": true,
      "launchUrl": "swagger",
      "applicationUrl": "https://localhost:5001;http://localhost:5000",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Development"
      }
    }
  }
}
```

**Ejecutar el proyecto en Rider:**

- **Atajo de teclado**: `Shift + F10` (Run)
- **Botón verde de Play** en la barra superior
- **Terminal**:  `dotnet run`
- **Con hot reload**: `dotnet watch run` (recarga automática al guardar cambios)

---

### Parametrizando la aplicación

La aplicación se parametriza en el archivo `appsettings.json` ubicado en la raíz del proyecto. 

**Archivos de configuración:**

- **Global**:  `appsettings.json`
- **Desarrollo**: `appsettings.Development.json`
- **Producción**: `appsettings.Production.json`
- **Testing**: `appsettings.Testing.json` (crear manualmente si es necesario)

**Ejemplo de `appsettings.json` completo:**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "Microsoft.EntityFrameworkCore":  "Information"
    }
  },
  "AllowedHosts":  "*",
  
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=TiendaDB;User Id=sa;Password=TuPassword123! ;TrustServerCertificate=True;",
    "RedisConnection": "localhost:6379"
  },
  
  "ApiSettings": {
    "Version": "v1",
    "Title": "Mi Tienda API",
    "Description": "API REST para gestión de productos",
    "EnableCompression": true,
    "EnableCors": true
  },
  
  "Storage": {
    "RootLocation": "uploads",
    "MaxFileSize": 10485760,
    "AllowedExtensions": [ "jpg", "jpeg", "png", "pdf" ]
  },
  
  "Jwt":  {
    "SecretKey": "tu-clave-secreta-super-larga-y-segura-de-al-menos-32-caracteres",
    "Issuer": "MiTiendaApi",
    "Audience": "MiTiendaApiClients",
    "ExpirationMinutes": 60
  },
  
  "Serilog": {
    "MinimumLevel": "Information",
    "WriteTo":  [
      {
        "Name": "Console"
      },
      {
        "Name": "File",
        "Args": {
          "path": "Logs/log-.txt",
          "rollingInterval":  "Day",
          "retainedFileCountLimit": 7
        }
      }
    ]
  }
}
```

**Ejemplo de `appsettings.Development.json`:**

```json
{
  "Logging": {
    "LogLevel": {
      "Default":  "Debug",
      "Microsoft.AspNetCore": "Information"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=TiendaDB_Dev;User Id=sa;Password=DevPassword123!;TrustServerCertificate=True;"
  }
}
```

**Acceder a la configuración en código:**

#### Opción 1: Usando `IConfiguration` directamente

```csharp
public class MiServicio
{
    private readonly IConfiguration _configuration;

    public MiServicio(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public void HacerAlgo()
    {
        var version = _configuration["ApiSettings:Version"];
        var connectionString = _configuration. GetConnectionString("DefaultConnection");
        var maxFileSize = _configuration. GetValue<long>("Storage:MaxFileSize");
        
        Console.WriteLine($"API Version: {version}");
    }
}
```

#### Opción 2: Usando el patrón Options (recomendado)

```csharp
// 1. Crear la clase de configuración
public class ApiSettings
{
    public string Version { get; set; } = string.Empty;
    public string Title { get; set; } = string.Empty;
    public string Description { get; set; } = string.Empty;
    public bool EnableCompression { get; set; }
    public bool EnableCors { get; set; }
}

// 2. Registrar en Program.cs
builder.Services.Configure<ApiSettings>(
    builder.Configuration.GetSection("ApiSettings"));

// 3. Inyectar en un servicio o controlador
public class ProductosController : ControllerBase
{
    private readonly ApiSettings _apiSettings;

    public ProductosController(IOptions<ApiSettings> apiSettings)
    {
        _apiSettings = apiSettings.Value;
    }

    [HttpGet("version")]
    public IActionResult GetVersion()
    {
        return Ok(new { Version = _apiSettings. Version, Title = _apiSettings.Title });
    }
}
```

**Cambiar el entorno de ejecución en Rider:**

#### Opción 1: Editar la configuración de ejecución

1. Click en el dropdown de configuraciones (arriba a la derecha)
2. Edit Configurations
3. En **Environment Variables**, agregar:  `ASPNETCORE_ENVIRONMENT=Production`

#### Opción 2: Terminal de Rider

```bash
# Linux/macOS
export ASPNETCORE_ENVIRONMENT=Production
dotnet run

# Windows (PowerShell)
$env:ASPNETCORE_ENVIRONMENT="Production"
dotnet run

# Windows (CMD)
set ASPNETCORE_ENVIRONMENT=Production
dotnet run
```

#### Opción 3: Archivo launchSettings.json

```json
{
  "profiles": {
    "Production": {
      "commandName":  "Project",
      "launchBrowser": true,
      "applicationUrl": "https://localhost:5001",
      "environmentVariables": {
        "ASPNETCORE_ENVIRONMENT": "Production"
      }
    }
  }
}
```

---

## ASP.NET Core MVC y Web API

**ASP.NET Core MVC** es un framework que implementa el patrón **Modelo-Vista-Controlador (MVC)** para construir aplicaciones web.

**ASP.NET Core Web API** es una variante optimizada para construir **APIs RESTful** sin necesidad de vistas (solo devuelve datos en JSON/XML).

![Spring Boot Architecture](../images/srpingboot.png)

**Patrón MVC:**

1. **Modelo (Model)**: 
   - Representa los datos y la lógica de negocio
   - POCOs (Plain Old CLR Objects) - clases simples de C#
   - Ejemplo: `public record Producto(int Id, string Nombre, decimal Precio);`

2. **Vista (View)**: 
   - Renderiza el modelo en formato HTML (Razor Pages, Blazor)
   - **No se usa en Web API** (solo devolvemos JSON/XML)

3. **Controlador (Controller)**: 
   - Maneja las solicitudes HTTP
   - Interactúa con servicios/repositorios
   - Devuelve respuestas (JSON, XML, etc.)

**Características de ASP.NET Core Web API:**

✅ **Routing automático**: Rutas basadas en atributos (`[Route]`, `[HttpGet]`, etc.)

✅ **Model Binding**: Enlace automático de parámetros HTTP a objetos C#

✅ **Model Validation**: Validación automática con Data Annotations

✅ **Content Negotiation**: Soporte para JSON, XML, etc.  (configurable)

✅ **Filters**: Ejecutan código antes/después de las acciones (logging, autorización, etc.)

✅ **Middleware Pipeline**: Procesamiento modular de solicitudes HTTP

✅ **Dependency Injection**: Integrada nativamente

✅ **Async/Await**: Operaciones asíncronas para mejor rendimiento

---

## Componentes de ASP.NET Core

ASP.NET Core utiliza una arquitectura basada en capas con componentes claramente definidos:

![Components Architecture](../images/components.png)

**Componentes principales:**

### 1. **Controllers (Controladores)**

Manejan las solicitudes HTTP y devuelven respuestas. 

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    private readonly IProductoService _service;
    private readonly ILogger<ProductosController> _logger;

    public ProductosController(
        IProductoService service, 
        ILogger<ProductosController> logger)
    {
        _service = service;
        _logger = logger;
    }

    [HttpGet]
    public async Task<ActionResult<IEnumerable<Producto>>> GetAll()
    {
        _logger.LogInformation("Obteniendo todos los productos");
        var productos = await _service.GetAllAsync();
        return Ok(productos);
    }
}
```

### 2. **Services (Servicios)**

Contienen la lógica de negocio. 

```csharp
public interface IProductoService
{
    Task<IEnumerable<Producto>> GetAllAsync();
    Task<Producto? > GetByIdAsync(int id);
    Task<Producto> CreateAsync(Producto producto);
}

public class ProductoService : IProductoService
{
    private readonly IProductoRepository _repository;

    public ProductoService(IProductoRepository repository)
    {
        _repository = repository;
    }

    public async Task<IEnumerable<Producto>> GetAllAsync()
    {
        return await _repository.GetAllAsync();
    }

    public async Task<Producto?> GetByIdAsync(int id)
    {
        return await _repository. GetByIdAsync(id);
    }

    public async Task<Producto> CreateAsync(Producto producto)
    {
        // Lógica de negocio (validaciones, transformaciones, etc.)
        producto.FechaCreacion = DateTime. UtcNow;
        return await _repository.CreateAsync(producto);
    }
}
```

### 3. **Repositories (Repositorios)**

Abstraen el acceso a datos.

```csharp
public interface IProductoRepository
{
    Task<IEnumerable<Producto>> GetAllAsync();
    Task<Producto?> GetByIdAsync(int id);
    Task<Producto> CreateAsync(Producto producto);
    Task<Producto? > UpdateAsync(Producto producto);
    Task<bool> DeleteAsync(int id);
}

public class ProductoRepository :  IProductoRepository
{
    private readonly List<Producto> _productos = [];

    public Task<IEnumerable<Producto>> GetAllAsync()
    {
        return Task.FromResult<IEnumerable<Producto>>(_productos);
    }

    public Task<Producto?> GetByIdAsync(int id)
    {
        var producto = _productos.FirstOrDefault(p => p.Id == id);
        return Task.FromResult(producto);
    }

    public Task<Producto> CreateAsync(Producto producto)
    {
        producto.Id = _productos.Count > 0 ? _productos. Max(p => p.Id) + 1 : 1;
        _productos.Add(producto);
        return Task.FromResult(producto);
    }

    public Task<Producto?> UpdateAsync(Producto producto)
    {
        var existente = _productos.FirstOrDefault(p => p.Id == producto. Id);
        if (existente is null) return Task.FromResult<Producto?>(null);

        existente.Nombre = producto. Nombre;
        existente. Precio = producto.Precio;
        existente.FechaActualizacion = DateTime.UtcNow;
        return Task.FromResult<Producto?>(existente);
    }

    public Task<bool> DeleteAsync(int id)
    {
        var producto = _productos.FirstOrDefault(p => p.Id == id);
        if (producto is null) return Task.FromResult(false);

        _productos.Remove(producto);
        return Task. FromResult(true);
    }
}
```

### 4. **Models (Modelos)**

Representan las entidades del dominio.  Con C# 14, usamos **records** para inmutabilidad: 

```csharp
// Record con propiedades inmutables
public record Producto
{
    public int Id { get; init; }
    public required string Nombre { get; init; }
    public decimal Precio { get; init; }
    public int Cantidad { get; init; }
    public string? Imagen { get; init; }
    public required string Categoria { get; init; }
    public DateTime FechaCreacion { get; init; } = DateTime.UtcNow;
    public DateTime FechaActualizacion { get; init; } = DateTime. UtcNow;
}

// O usando primary constructor (C# 14)
public record ProductoDto(
    int Id,
    string Nombre,
    decimal Precio,
    int Cantidad,
    string Categoria
);
```

### 5. **DTOs (Data Transfer Objects)**

Objetos para transferir datos entre capas, separando el modelo de dominio de la API:

```csharp
// DTO para crear un producto
public record CreateProductoDto(
    string Nombre,
    decimal Precio,
    int Cantidad,
    string Categoria
);

// DTO para actualizar un producto
public record UpdateProductoDto(
    string?  Nombre,
    decimal?  Precio,
    int? Cantidad,
    string? Categoria
);

// DTO de respuesta
public record ProductoResponseDto(
    int Id,
    string Nombre,
    decimal Precio,
    int Cantidad,
    string Categoria,
    DateTime FechaCreacion
);
```

**Arquitectura en capas:**

```
┌─────────────────────────────────────┐
│         Controllers                 │  ← HTTP Requests/Responses (JSON)
├─────────────────────────────────────┤
│         Services                    │  ← Business Logic
├─────────────────────────────────────┤
│         Repositories                │  ← Data Access
├─────────────────────────────────────┤
│      Database / External APIs       │  ← Persistence
└─────────────────────────────────────┘
```

---

### Ciclo de vida de servicios (Service Lifetime)

En ASP.NET Core, los servicios se registran con diferentes **ciclos de vida (lifetimes)**:

| Lifetime | Descripción | Cuándo usar | Ejemplo |
|: ---------|:------------|:------------|:--------|
| **Transient** | Nueva instancia en cada solicitud | Servicios ligeros sin estado | `EmailService` |
| **Scoped** | Una instancia por solicitud HTTP | Repositorios, `DbContext` | `ProductoRepository` |
| **Singleton** | Una sola instancia para toda la app | Cachés, configuración global | `CacheService` |

**Ejemplo de registro en `Program.cs`:**

```csharp
// Transient:  nueva instancia cada vez
builder.Services.AddTransient<IEmailService, EmailService>();

// Scoped: una instancia por request HTTP
builder.Services.AddScoped<IProductoRepository, ProductoRepository>();
builder.Services.AddScoped<IProductoService, ProductoService>();

// Singleton: una sola instancia para toda la aplicación
builder.Services.AddSingleton<ICacheService, CacheService>();
```

**Diagrama visual:**

```
Request 1:  [Transient1] [Transient2] [Scoped1 ────] [Singleton ──────────────]
Request 2:  [Transient3] [Transient4] [Scoped2 ────] [Singleton (misma inst. )]
Request 3:  [Transient5] [Transient6] [Scoped3 ────] [Singleton (misma inst.)]
```

---

### IoC y DI en ASP.NET Core

**Inversión de Control (IoC)** e **Inyección de Dependencias (DI)** están integradas nativamente en ASP. NET Core.

**Inyección por Constructor (recomendado en . NET 10 / C# 14):**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController(
    IProductoService productoService,
    ILogger<ProductosController> logger) : ControllerBase
{
    // C# 14: Primary constructor con parámetros inyectados automáticamente
    
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Producto>>> GetAll()
    {
        logger.LogInformation("Obteniendo todos los productos");
        var productos = await productoService.GetAllAsync();
        return Ok(productos);
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<Producto>> GetById(int id)
    {
        var producto = await productoService.GetByIdAsync(id);
        return producto is null ? NotFound() : Ok(producto);
    }
}
```

**Comparación con versiones anteriores:**

```csharp
// ❌ Versión antigua (C# 10)
public class ProductosController :  ControllerBase
{
    private readonly IProductoService _productoService;
    private readonly ILogger<ProductosController> _logger;

    public ProductosController(
        IProductoService productoService,
        ILogger<ProductosController> logger)
    {
        _productoService = productoService;
        _logger = logger;
    }
}

// ✅ Versión moderna (C# 14 con primary constructor)
public class ProductosController(
    IProductoService productoService,
    ILogger<ProductosController> logger) : ControllerBase
{
    // Parámetros disponibles automáticamente como 'productoService' y 'logger'
}
```

**Ventajas de DI:**

✅ **Desacoplamiento**: Clases no dependen de implementaciones concretas

✅ **Testabilidad**: Fácil sustituir dependencias reales por mocks

✅ **Mantenibilidad**: Cambios en dependencias no afectan a las clases consumidoras

✅ **Reutilización**: Servicios se pueden inyectar en múltiples lugares

---

### Creando rutas (Endpoints)

Para crear rutas usamos controladores con el atributo `[ApiController]` y los verbos HTTP.

**Ejemplo completo de controlador con . NET 10 y C# 14:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController(
    IProductoRepository repository,
    ILogger<ProductosController> logger) : ControllerBase
{
    // GET: api/productos
    [HttpGet]
    [ProducesResponseType(typeof(IEnumerable<Producto>), StatusCodes.Status200OK)]
    public async Task<ActionResult<IEnumerable<Producto>>> GetAll()
    {
        logger.LogInformation("GET /api/productos - Obteniendo todos los productos");
        var productos = await repository. GetAllAsync();
        return Ok(productos);
    }

    // GET: api/productos/5
    [HttpGet("{id:int}")]
    [ProducesResponseType(typeof(Producto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<ActionResult<Producto>> GetById(int id)
    {
        logger.LogInformation("GET /api/productos/{Id} - Obteniendo producto", id);
        var producto = await repository.GetByIdAsync(id);
        
        if (producto is null)
        {
            logger.LogWarning("Producto con ID {Id} no encontrado", id);
            return NotFound(new { mensaje = $"Producto con ID {id} no encontrado" });
        }

        return Ok(producto);
    }

    // POST: api/productos
    [HttpPost]
    [ProducesResponseType(typeof(Producto), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<ActionResult<Producto>> Create([FromBody] Producto producto)
    {
        if (!ModelState.IsValid)
        {
            logger.LogWarning("Modelo inválido al crear producto");
            return BadRequest(ModelState);
        }

        logger.LogInformation("POST /api/productos - Creando producto:  {Nombre}", producto.Nombre);
        var creado = await repository.CreateAsync(producto);
        
        return CreatedAtAction(
            nameof(GetById), 
            new { id = creado.Id }, 
            creado);
    }

    // PUT: api/productos/5
    [HttpPut("{id:int}")]
    [ProducesResponseType(typeof(Producto), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    [ProducesResponseType(StatusCodes. Status404NotFound)]
    public async Task<ActionResult<Producto>> Update(int id, [FromBody] Producto producto)
    {
        if (id != producto.Id)
        {
            logger.LogWarning("ID de ruta ({RouteId}) no coincide con ID del cuerpo ({BodyId})", id, producto.Id);
            return BadRequest(new { mensaje = "El ID de la ruta no coincide con el del cuerpo" });
        }

        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        logger.LogInformation("PUT /api/productos/{Id} - Actualizando producto", id);
        var actualizado = await repository.UpdateAsync(producto);
        
        return actualizado is null ? NotFound() : Ok(actualizado);
    }

    // DELETE: api/productos/5
    [HttpDelete("{id:int}")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes. Status404NotFound)]
    public async Task<IActionResult> Delete(int id)
    {
        logger.LogInformation("DELETE /api/productos/{Id} - Eliminando producto", id);
        var eliminado = await repository. DeleteAsync(id);
        
        if (!eliminado)
        {
            logger.LogWarning("No se pudo eliminar el producto con ID {Id}", id);
            return NotFound(new { mensaje = $"Producto con ID {id} no encontrado" });
        }

        return NoContent();
    }
}
```

**Atributos importantes:**

- `[ApiController]`: Activa comportamientos específicos para APIs (validación automática, inferencia de parámetros)
- `[Route("api/[controller]")]`: Define la ruta base (`[controller]` se reemplaza por "Productos")
- `[HttpGet]`, `[HttpPost]`, etc.: Especifican el verbo HTTP
- `[ProducesResponseType]`: Documenta los códigos de respuesta (útil para Swagger)
- `{id:int}`: Restricción de ruta (solo acepta enteros)

---

### Responses

Para devolver respuestas usamos `ActionResult<T>` o `IActionResult`.

**Tipos de respuestas en .NET 10:**

```csharp
// ✅ 200 OK
return Ok(producto);
return Ok(new { mensaje = "Éxito", data = producto });

// ✅ 201 Created con Location header
return CreatedAtAction(nameof(GetById), new { id = producto.Id }, producto);
return Created($"/api/productos/{producto.Id}", producto);

// ✅ 204 No Content
return NoContent();

// ✅ 400 Bad Request
return BadRequest("Datos inválidos");
return BadRequest(ModelState);
return BadRequest(new { error = "Nombre es requerido", campo = "Nombre" });

// ✅ 404 Not Found
return NotFound();
return NotFound(new { mensaje = $"Producto con ID {id} no encontrado" });

// ✅ 409 Conflict
return Conflict(new { mensaje = "El producto ya existe" });

// ✅ 422 Unprocessable Entity
return UnprocessableEntity(new { errores = validationErrors });

// ✅ 500 Internal Server Error
return StatusCode(StatusCodes.Status500InternalServerError, "Error interno");

// ✅ Custom status code
return StatusCode(418, "I'm a teapot ☕");
```

**Formato de respuesta con `ProblemDetails` (estándar RFC 7807):**

```csharp
[HttpGet("{id: int}")]
public async Task<ActionResult<Producto>> GetById(int id)
{
    var producto = await repository.GetByIdAsync(id);
    
    if (producto is null)
    {
        return NotFound(new ProblemDetails
        {
            Title = "Producto no encontrado",
            Detail = $"No existe un producto con ID {id}",
            Status = StatusCodes.Status404NotFound,
            Instance = HttpContext.Request.Path
        });
    }

    return Ok(producto);
}
```

---

### Requests

#### Parámetros de ruta

Usamos `[FromRoute]` o simplemente el nombre del parámetro: 

```csharp
// Parámetro simple
[HttpGet("{id:int}")]
public async Task<ActionResult<Producto>> GetById(int id) // [FromRoute] es implícito
{
    var producto = await repository.GetByIdAsync(id);
    return producto is null ? NotFound() : Ok(producto);
}

// Múltiples parámetros de ruta
[HttpGet("{categoria}/{id:int}")]
public async Task<ActionResult<Producto>> GetByCategoriaYId(string categoria, int id)
{
    var producto = await repository.GetByCategoriaYIdAsync(categoria, id);
    return producto is null ? NotFound() : Ok(producto);
}

// Restricciones de ruta
[HttpGet("{id:int: min(1)}")]           // ID debe ser >= 1
[HttpGet("{categoria:alpha}")]         // Solo letras
[HttpGet("{fecha: datetime}")]          // Debe ser fecha válida
[HttpGet("{activo:bool}")]             // true o false
```

#### Parámetros de consulta (Query Parameters)

Usamos `[FromQuery]`:

```csharp
[HttpGet]
public async Task<ActionResult<IEnumerable<Producto>>> GetAll(
    [FromQuery] string? categoria = null,
    [FromQuery] decimal? precioMin = null,
    [FromQuery] decimal? precioMax = null,
    [FromQuery] int pagina = 1,
    [FromQuery] int tamanoPagina = 10)
{
    var productos = await repository.GetAllAsync();

    // Filtrar por categoría (case-insensitive)
    if (!string.IsNullOrWhiteSpace(categoria))
        productos = productos.Where(p => 
            p.Categoria. Equals(categoria, StringComparison.OrdinalIgnoreCase));

    // Filtrar por rango de precio
    if (precioMin. HasValue)
        productos = productos.Where(p => p.Precio >= precioMin.Value);

    if (precioMax.HasValue)
        productos = productos. Where(p => p.Precio <= precioMax.Value);

    // Paginación
    var productosPaginados = productos
        .Skip((pagina - 1) * tamanoPagina)
        .Take(tamanoPagina);

    return Ok(new
    {
        pagina,
        tamanoPagina,
        total = productos.Count(),
        datos = productosPaginados
    });
}
```

**Ejemplos de peticiones:**

```
GET /api/productos?categoria=disney
GET /api/productos?precioMin=10&precioMax=50
GET /api/productos?categoria=marvel&precioMin=20&pagina=2&tamanoPagina=5
```

#### Peticiones con datos serializados

Usamos `[FromBody]` para recibir JSON:

```csharp
[HttpPost]
public async Task<ActionResult<Producto>> Create([FromBody] Producto producto)
{
    if (!ModelState. IsValid)
    {
        logger.LogWarning("Modelo inválido:  {Errors}", 
            string.Join(", ", ModelState.Values.SelectMany(v => v. Errors).Select(e => e.ErrorMessage)));
        return BadRequest(ModelState);
    }

    var creado = await repository.CreateAsync(producto);
    return CreatedAtAction(nameof(GetById), new { id = creado.Id }, creado);
}

[HttpPut("{id:int}")]
public async Task<ActionResult<Producto>> Update(int id, [FromBody] Producto producto)
{
    if (id != producto.Id)
        return BadRequest(new { mensaje = "El ID de la ruta no coincide con el del cuerpo" });

    if (!ModelState.IsValid)
        return BadRequest(ModelState);

    var actualizado = await repository.UpdateAsync(producto);
    return actualizado is null ? NotFound() : Ok(actualizado);
}
```

**Validación con Data Annotations:**

```csharp
public record Producto
{
    public int Id { get; init; }
    
    [Required(ErrorMessage = "El nombre es obligatorio")]
    [StringLength(100, MinimumLength = 3, ErrorMessage = "El nombre debe tener entre 3 y 100 caracteres")]
    public required string Nombre { get; init; }
    
    [Range(0.01, double.MaxValue, ErrorMessage = "El precio debe ser mayor a 0")]
    public decimal Precio { get; init; }
    
    [Range(0, int.MaxValue, ErrorMessage = "La cantidad no puede ser negativa")]
    public int Cantidad { get; init; }
    
    [Required(ErrorMessage = "La categoría es obligatoria")]
    public required string Categoria { get; init; }
}
```

---

### Versionado de la API

**Instalar paquete NuGet:**

```bash
dotnet add package Asp. Versioning. Mvc
dotnet add package Asp.Versioning.Mvc. ApiExplorer
```

**Configurar en `Program.cs`:**

```csharp
using Asp.Versioning;

var builder = WebApplication.CreateBuilder(args);

// Configurar versionado de API
builder.Services.AddApiVersioning(options =>
{
    options.DefaultApiVersion = new ApiVersion(1, 0);
    options.AssumeDefaultVersionWhenUnspecified = true;
    options.ReportApiVersions = true; // Devuelve versiones soportadas en headers
    options.ApiVersionReader = ApiVersionReader. Combine(
        new UrlSegmentApiVersionReader(),           // api/v1/productos
        new HeaderApiVersionReader("api-version"),  // Header:  api-version:  1.0
        new QueryStringApiVersionReader("version")  // ? version=1.0
    );
}).AddApiExplorer(options =>
{
    options.GroupNameFormat = "'v'VVV"; // v1, v2
    options.SubstituteApiVersionInUrl = true;
});

var app = builder.Build();
```

**Controladores versionados:**

```csharp
// Versión 1
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
public class ProductosV1Controller(IProductoRepository repository) : ControllerBase
{
    [HttpGet]
    public async Task<ActionResult<IEnumerable<Producto>>> GetAll()
    {
        var productos = await repository.GetAllAsync();
        return Ok(productos);
    }
}

// Versión 2 (con cambios)
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("2.0")]
public class ProductosV2Controller(IProductoRepository repository) : ControllerBase
{
    [HttpGet]
    public async Task<ActionResult<object>> GetAll()
    {
        var productos = await repository.GetAllAsync();
        
        // Versión 2: Devuelve formato mejorado
        return Ok(new
        {
            version = "2.0",
            timestamp = DateTime.UtcNow,
            count = productos.Count(),
            data = productos
        });
    }
}
```

**Peticiones:**

```http
GET /api/v1/productos
GET /api/v2/productos

GET /api/productos? version=1.0
GET /api/productos
Header: api-version: 2.0
```

---

## Postman

**Postman** es una herramienta para probar APIs REST. 

![Postman](../images/postman.png)

**Alternativa en Rider:  HTTP Client integrado**

Rider incluye un cliente HTTP integrado. Crear archivo `.http` en el proyecto:

**Ejemplo de archivo `MiTiendaApi.http`:**

```http
### Variables
@baseUrl = https://localhost:5001/api
@token = Bearer tu-jwt-token-aqui

### GET:  Obtener todos los productos
GET {{baseUrl}}/productos
Accept: application/json

### GET:  Obtener producto por ID
GET {{baseUrl}}/productos/1

### GET: Filtrar por categoría
GET {{baseUrl}}/productos?categoria=disney&precioMin=10

### POST: Crear nuevo producto
POST {{baseUrl}}/productos
Content-Type: application/json

{
  "nombre": "Funko Iron Man",
  "precio": 25.99,
  "cantidad": 10,
  "categoria": "Marvel",
  "imagen": "ironman.jpg"
}

### PUT: Actualizar producto
PUT {{baseUrl}}/productos/1
Content-Type: application/json

{
  "id": 1,
  "nombre": "Funko Iron Man - Edición Especial",
  "precio": 29.99,
  "cantidad": 5,
  "categoria": "Marvel"
}

### DELETE:  Eliminar producto
DELETE {{baseUrl}}/productos/1

### GET: Con autenticación
GET {{baseUrl}}/productos/protegido
Authorization: {{token}}
```

**Ejecutar en Rider:**

1. Click en el icono ▶️ verde al lado de cada petición
2. Ver resultados en la pestaña inferior
3. Shortcuts:  `Ctrl + Enter` para ejecutar

---

## Práctica de clase: Mi primera API REST

**Objetivo:** Crear una API REST completa para gestionar **Funkos** usando .NET 10, C# 14 y Rider.

### Paso 1: Crear el proyecto en Rider

```bash
# Abrir terminal en Rider (Alt + F12)
dotnet new webapi -n FunkosApi -f net10.0 --use-controllers
cd FunkosApi
```

O usar la interfaz gráfica: 
- File → New → Solution
- ASP.NET Core Web Application → Web API
- Framework: .NET 10.0

### Paso 2: Crear el modelo `Funko`

Crear carpeta `Models` y archivo `Funko.cs`:

```csharp
using System.ComponentModel.DataAnnotations;

namespace FunkosApi. Models;

public record Funko
{
    public int Id { get; init; }
    
    [Required(ErrorMessage = "El nombre es obligatorio")]
    [StringLength(100, MinimumLength = 3)]
    public required string Nombre { get; init; }
    
    [Range(0.01, double.MaxValue, ErrorMessage = "El precio debe ser mayor a 0")]
    public decimal Precio { get; init; }
    
    [Range(0, int.MaxValue)]
    public int Cantidad { get; init; }
    
    public string?  Imagen { get; init; }
    
    [Required]
    public required string Categoria { get; init; }
    
    public DateTime FechaCreacion { get; init; } = DateTime.UtcNow;
    public DateTime FechaActualizacion { get; init; } = DateTime. UtcNow;
}
```

### Paso 3: Crear el repositorio

Crear carpeta `Repositories` con la interfaz y la implementación:

**`IFunkoRepository.cs`:**

```csharp
using FunkosApi.Models;

namespace FunkosApi.Repositories;

public interface IFunkoRepository
{
    Task<IEnumerable<Funko>> GetAllAsync();
    Task<IEnumerable<Funko>> GetByCategoriaAsync(string categoria);
    Task<Funko? > GetByIdAsync(int id);
    Task<Funko> CreateAsync(Funko funko);
    Task<Funko?> UpdateAsync(Funko funko);
    Task<bool> DeleteAsync(int id);
}
```

**`FunkoRepository.cs`:**

```csharp
using FunkosApi.Models;

namespace FunkosApi.Repositories;

public class FunkoRepository : IFunkoRepository
{
    private readonly List<Funko> _funkos =
    [
        new() { Id = 1, Nombre = "Funko Iron Man", Precio = 25.99m, Cantidad = 10, Categoria = "Marvel", Imagen = "ironman.jpg" },
        new() { Id = 2, Nombre = "Funko Mickey Mouse", Precio = 19.99m, Cantidad = 15, Categoria = "Disney", Imagen = "mickey.jpg" },
        new() { Id = 3, Nombre = "Funko Batman", Precio = 22.99m, Cantidad = 8, Categoria = "DC", Imagen = "batman.jpg" }
    ];

    public Task<IEnumerable<Funko>> GetAllAsync() =>
        Task.FromResult<IEnumerable<Funko>>(_funkos);

    public Task<IEnumerable<Funko>> GetByCategoriaAsync(string categoria)
    {
        var funkos = _funkos.Where(f => 
            f.Categoria. Equals(categoria, StringComparison.OrdinalIgnoreCase));
        return Task.FromResult(funkos);
    }

    public Task<Funko?> GetByIdAsync(int id)
    {
        var funko = _funkos.FirstOrDefault(f => f.Id == id);
        return Task.FromResult(funko);
    }

    public Task<Funko> CreateAsync(Funko funko)
    {
        var nuevoId = _funkos.Count > 0 ? _funkos. Max(f => f.Id) + 1 : 1;
        var nuevoFunko = funko with 
        { 
            Id = nuevoId, 
            FechaCreacion = DateTime. UtcNow,
            FechaActualizacion = DateTime.UtcNow
        };
        _funkos.Add(nuevoFunko);
        return Task.FromResult(nuevoFunko);
    }

    public Task<Funko?> UpdateAsync(Funko funko)
    {
        var existente = _funkos.FirstOrDefault(f => f.Id == funko.Id);
        if (existente is null) return Task.FromResult<Funko?>(null);

        _funkos.Remove(existente);
        var actualizado = funko with { FechaActualizacion = DateTime.UtcNow };
        _funkos.Add(actualizado);
        return Task. FromResult<Funko?>(actualizado);
    }

    public Task<bool> DeleteAsync(int id)
    {
        var funko = _funkos. FirstOrDefault(f => f. Id == id);
        if (funko is null) return Task.FromResult(false);

        _funkos.Remove(funko);
        return Task.FromResult(true);
    }
}
```

### Paso 4: Crear el controlador

**`Controllers/FunkosController.cs`:**

```csharp
using FunkosApi.Models;
using FunkosApi.Repositories;
using Microsoft.AspNetCore.Mvc;

namespace FunkosApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class FunkosController(
    IFunkoRepository repository,
    ILogger<FunkosController> logger) : ControllerBase
{
    // GET: api/funkos
    [HttpGet]
    [ProducesResponseType(typeof(IEnumerable<Funko>), StatusCodes.Status200OK)]
    public async Task<ActionResult<IEnumerable<Funko>>> GetAll(
        [FromQuery] string?  categoria = null)
    {
        logger.LogInformation("GET /api/funkos - Categoría: {Categoria}", categoria ??  "todas");

        var funkos = string.IsNullOrWhiteSpace(categoria)
            ? await repository.GetAllAsync()
            : await repository.GetByCategoriaAsync(categoria);

        return Ok(funkos);
    }

    // GET: api/funkos/5
    [HttpGet("{id:int}")]
    [ProducesResponseType(typeof(Funko), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<ActionResult<Funko>> GetById(int id)
    {
        logger.LogInformation("GET /api/funkos/{Id}", id);
        
        var funko = await repository.GetByIdAsync(id);
        return funko is null 
            ? NotFound(new { mensaje = $"Funko con ID {id} no encontrado" })
            : Ok(funko);
    }

    // POST: api/funkos
    [HttpPost]
    [ProducesResponseType(typeof(Funko), StatusCodes.Status201Created)]
    [ProducesResponseType(StatusCodes.Status400BadRequest)]
    public async Task<ActionResult<Funko>> Create([FromBody] Funko funko)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        logger.LogInformation("POST /api/funkos - Creando:  {Nombre}", funko. Nombre);
        
        var creado = await repository.CreateAsync(funko);
        return CreatedAtAction(nameof(GetById), new { id = creado.Id }, creado);
    }

    // PUT: api/funkos/5
    [HttpPut("{id:int}")]
    [ProducesResponseType(typeof(Funko), StatusCodes.Status200OK)]
    [ProducesResponseType(StatusCodes. Status400BadRequest)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<ActionResult<Funko>> Update(int id, [FromBody] Funko funko)
    {
        if (id != funko.Id)
            return BadRequest(new { mensaje = "El ID de la ruta no coincide" });

        if (!ModelState. IsValid)
            return BadRequest(ModelState);

        logger.LogInformation("PUT /api/funkos/{Id}", id);
        
        var actualizado = await repository.UpdateAsync(funko);
        return actualizado is null ?  NotFound() : Ok(actualizado);
    }

    // DELETE: api/funkos/5
    [HttpDelete("{id:int}")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(StatusCodes.Status404NotFound)]
    public async Task<IActionResult> Delete(int id)
    {
        logger.LogInformation("DELETE /api/funkos/{Id}", id);
        
        var eliminado = await repository.DeleteAsync(id);
        return eliminado ? NoContent() : NotFound();
    }
}
```

### Paso 5: Registrar servicios en `Program.cs`

```csharp
using FunkosApi.Repositories;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// ✅ Registrar repositorio
builder.Services.AddScoped<IFunkoRepository, FunkoRepository>();

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI(c => c.RoutePrefix = string.Empty); // Swagger en raíz
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

### Paso 6: Ejecutar y probar

1. **Ejecutar en Rider**: `Shift + F10`
2. **Abrir Swagger**: `https://localhost:5001/`
3. **Crear archivo de pruebas** `FunkosApi.http`:

```http
@baseUrl = https://localhost:5001/api

### GET: Todos los funkos
GET {{baseUrl}}/funkos

### GET: Por categoría
GET {{baseUrl}}/funkos?categoria=Marvel

### GET: Por ID
GET {{baseUrl}}/funkos/1

### POST: Crear funko
POST {{baseUrl}}/funkos
Content-Type: application/json

{
  "nombre": "Funko Spider-Man",
  "precio":  24.99,
  "cantidad":  12,
  "categoria":  "Marvel",
  "imagen":  "spiderman.jpg"
}

### PUT: Actualizar
PUT {{baseUrl}}/funkos/1
Content-Type: application/json

{
  "id": 1,
  "nombre": "Funko Iron Man - Edición Especial",
  "precio": 29.99,
  "cantidad":  5,
  "categoria":  "Marvel"
}

### DELETE: Eliminar
DELETE {{baseUrl}}/funkos/1
```

---
