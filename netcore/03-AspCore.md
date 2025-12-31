# ASP.NET Core

- [ASP.NET Core](#aspnet-core)
  - [¿Qué es . NET y ASP.NET Core?](#qué-es-net-y-aspnet-core)
  - [Características clave de ASP.NET Core](#características-clave-de-aspnet-core)
  - [Módulos principales de ASP.NET Core](#módulos-principales-de-aspnet-core)
  - [Servicios (Services) en ASP.NET Core](#servicios-services-en-aspnet-core)
  - [Inversión de Control e Inyección de Dependencias](#inversión-de-control-e-inyección-de-dependencias)
  - [Práctica de clase:  ASP.NET Core](#práctica-de-clase-aspnet-core)

![ASP.NET Core Banner](../images/banner03.png)

---

## ¿Qué es .NET y ASP.NET Core?

**.  NET** es una plataforma de desarrollo de código abierto y multiplataforma creada por Microsoft para construir diferentes tipos de aplicaciones:  web, móviles, de escritorio, juegos, IoT, etc.

**ASP.NET Core** es un framework de código abierto y multiplataforma para construir aplicaciones web modernas, APIs REST, aplicaciones en tiempo real con SignalR, y servicios basados en la nube.

**Evolución:**

- **.NET Framework** (2002): Framework original solo para Windows.  
- **.NET Core** (2016): Reescritura multiplataforma (Windows, Linux, macOS), de código abierto.  
- **.NET 5/6/7/8+** (2020+): Unificación de .NET Framework y .NET Core bajo el nombre ".  NET".

**ASP.NET Core** fue creado para abordar las limitaciones de ASP.NET Framework y ofrecer: 

✅ **Multiplataforma**: Funciona en Windows, Linux, macOS. 

✅ **Alto rendimiento**: Uno de los frameworks web más rápidos según TechEmpower Benchmarks.

✅ **Modularidad**: Arquitectura basada en paquetes NuGet, solo incluyes lo que necesitas.

✅ **Inyección de dependencias integrada**: DI es parte del núcleo del framework.  

✅ **Integración con tecnologías modernas**: Docker, Kubernetes, Azure, microservicios.  

✅ **Código abierto y comunidad activa**: Desarrollo transparente en GitHub. 

---

## Características clave de ASP.NET Core

### 1. **Multiplataforma y Alto Rendimiento**

ASP.NET Core está optimizado para el rendimiento y puede ejecutarse en Windows, Linux y macOS. Utiliza **Kestrel**, un servidor web de alto rendimiento basado en libuv.

### 2. **Modularidad** 

ASP.NET Core es modular y ligero. No incluye componentes innecesarios.  Todo se instala a través de **paquetes NuGet**.

```xml
<ItemGroup>
  <PackageReference Include="Microsoft.AspNetCore. Mvc" Version="2.2.0" />
  <PackageReference Include="Microsoft.EntityFrameworkCore. SqlServer" Version="8.0.0" />
</ItemGroup>
```

### 3. **Inyección de Dependencias Integrada (DI)**

ASP.NET Core tiene un **contenedor de inyección de dependencias (IoC Container)** integrado de forma nativa.  No necesitas bibliotecas externas.  

### 4. **Configuración Basada en JSON**

La configuración de la aplicación se gestiona a través de archivos JSON (`appsettings.json`), variables de entorno, argumentos de línea de comandos, etc. 

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=MiApp;User Id=sa;Password=MiPassword;"
  },
  "Logging": {
    "LogLevel": {
      "Default": "Information"
    }
  }
}
```

### 5. **Middleware Pipeline**

ASP.NET Core utiliza un **pipeline de middleware** para procesar las solicitudes HTTP.  Cada middleware puede procesar la solicitud, modificarla y pasarla al siguiente middleware.  

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseHttpsRedirection();
app.UseAuthentication();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

### 6. **Soporte para Pruebas**

ASP.NET Core está diseñado para ser fácilmente testeable con frameworks como **NUnit**, **xUnit** y **MSTest**. 

---

## Módulos principales de ASP.NET Core

ASP.NET Core está diseñado de manera **modular**, lo que significa que puedes elegir usar solo los componentes que necesitas. A continuación, se describen los módulos y proyectos más importantes:

![Módulos ASP.NET Core](../images/modulos-spring.png)

### 1. **ASP.NET Core MVC**

Proporciona el patrón **Modelo-Vista-Controlador (MVC)** para construir aplicaciones web y APIs REST.  

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    [HttpGet]
    public ActionResult<IEnumerable<Producto>> GetAll()
    {
        return Ok(new[] { new Producto { Id = 1, Nombre = "Laptop" } });
    }

    [HttpPost]
    public ActionResult<Producto> Create(Producto producto)
    {
        // Lógica para crear el producto
        return CreatedAtAction(nameof(GetAll), new { id = producto.Id }, producto);
    }
}
```

### 2. **ASP.NET Core Minimal APIs**

En contraste con el modelo tradicional de MVC (Model-View-Controller), las **Minimal APIs** permiten crear APIs RESTful con el mínimo de código y configuración. Este enfoque elimina la "fricción" arquitectónica, permitiendo definir endpoints directamente en el archivo `Program.cs`.

#### ¿Por qué es más rápido?: Optimización del Pipeline

La principal ventaja de rendimiento de las Minimal APIs no es solo que escribes menos código, sino que la petición atraviesa **menos capas dentro del pipeline de ASP.NET Core**.

* **Sin instanciación de clases:** En MVC, el framework debe usar *reflexión* para buscar el controlador, instanciar la clase y luego buscar el método. Las Minimal APIs usan **delegados directos**, ejecutando la función de inmediato.
* **Menos sobrecarga (Overhead):** Se salta gran parte del ciclo de vida de los controladores (como los filtros de acción y la unión de modelos compleja), lo que reduce el uso de memoria y CPU.
* **Enrutamiento optimizado:** El mapa de rutas se genera al arrancar la aplicación, permitiendo que el servidor dirija la petición al código final de forma casi instantánea.

#### Ejemplo de implementación:

```csharp
var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

// La petición llega aquí directamente, saltándose toda la infraestructura de controladores
app.MapGet("/api/v1/status", () => new { Status = "Online", Performance = "High" });
app.MapPost("/api/v1/productos", (Producto producto) =>
{
    // Lógica para crear el producto
    return Results.Created($"/api/v1/productos/{producto.Id}", producto);
});

app.Run();

```

#### Comparativa de Flujo de Ejecución

| Fase              | ASP.NET Core MVC                    | Minimal APIs                             |
| ----------------- | ----------------------------------- | ---------------------------------------- |
| **Routing**       | Basado en nombres de clases/métodos | Basado en mapa de delegados (Directo)    |
| **Instanciación** | Requiere crear objeto `Controller`  | Ninguna (función estática o lambda)      |
| **Metadatos**     | Carga pesada de Atributos           | Metadatos mínimos y optimizados          |
| **Ideal para...** | Apps grandes con muchas vistas      | Microservicios y servicios de alta carga |

> **En resumen:** Al eliminar las capas intermedias de la infraestructura de controladores, las Minimal APIs logran un mayor rendimiento (más peticiones por segundo) y una menor latencia, lo que las convierte en la opción predilecta para arquitecturas modernas de microservicios.

---

### 3. Inyección de Dependencias en ASP.NET Core

La Inyección de Dependencias (DI) es un pilar fundamental de ASP.NET Core. Es un patrón de diseño que permite alcanzar el **desacoplamiento** entre las clases y sus dependencias. En lugar de que una clase cree manualmente sus objetos (usando `new`), el contenedor de servicios de .NET se encarga de "inyectarlos" automáticamente.

#### El Contenedor de Servicios

Toda la configuración ocurre en el archivo `Program.cs`. Aquí registramos nuestras interfaces y sus implementaciones para que el framework sepa cómo resolverlas en cualquier parte de la aplicación.

#### Ciclos de Vida de los Servicios (Service Lifetimes)

Al registrar un servicio, debemos definir cuánto tiempo vivirá el objeto creado:

* **Transient (Transitorio):** Se crea una nueva instancia **cada vez que se solicita**. Ideal para componentes ligeros sin estado.
* **Scoped (Delimitado):** Se crea una instancia **una vez por cada solicitud HTTP**. Es el estándar para bases de datos (como el `DbContext`), asegurando que toda la petición comparta la misma conexión.
* **Singleton (Único):** Se crea una única instancia la **primera vez que se solicita** y vive durante todo el tiempo que la aplicación esté ejecutándose.

---

#### Implementación Práctica: Comparativa de Enfoques

Aunque el contenedor de servicios es el mismo, la forma de consumir las dependencias varía según el modelo que utilices:

##### A. Inyección en Minimal APIs (Inyección por Parámetro)

Es el método más moderno y eficiente. Las dependencias se inyectan directamente como parámetros en la expresión lambda del endpoint. El framework resuelve el servicio solo cuando se llama a esa ruta específica.

```csharp
// Registro en Program.cs
builder.Services.AddScoped<IUserRepository, UserRepository>();

// Inyección directa en el endpoint
app.MapGet("/usuarios", (IUserRepository repo) => Results.Ok(repo.GetAll()));

```

##### B. Inyección en MVC (Inyección por Constructor)

En el modelo tradicional, las dependencias se solicitan a través del constructor de la clase. El framework instancia el controlador y le pasa todas las dependencias requeridas.

```csharp
public class UsuarioController : ControllerBase
{
    private readonly IUserRepository _repo;

    // Se inyecta al crear la instancia del controlador
    public UsuarioController(IUserRepository repo)
    {
        _repo = repo;
    }

    [HttpGet("/usuarios")]
    public IActionResult Get() => Ok(_repo.GetAll());
}

```

---

#### Ventajas de este modelo:

1. **Mantenibilidad:** Puedes cambiar la implementación de un servicio (ej. cambiar el motor de Base de Datos o de Email) en un solo lugar (`Program.cs`) sin tocar la lógica de negocio.
2. **Testabilidad:** Facilita enormemente las pruebas unitarias, ya que permite inyectar versiones "falsas" (Mocks) de los servicios.
3. **Eficiencia de Recursos:** Especialmente en Minimal APIs, el sistema solo instancia lo que el endpoint necesita en ese momento preciso, optimizando la memoria y el pipeline de ejecución.


### 4. **ASP.NET Core Razor Pages**

Alternativa al patrón MVC para construir páginas web de forma más simple y orientada a páginas.  

```csharp
public class IndexModel : PageModel
{
    public string Message { get; set; }

    public void OnGet()
    {
        Message = "Bienvenido a Razor Pages";
    }
}
```

### 3. **ASP.NET Core Web API**

Facilita la creación de **APIs RESTful** usando atributos HTTP como `[HttpGet]`, `[HttpPost]`, etc.

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    [HttpGet("{id}")]
    public ActionResult<Usuario> GetById(int id)
    {
        return Ok(new Usuario { Id = id, Nombre = "Juan" });
    }
}
```

### 5. **Entity Framework Core (EF Core)**

ORM (Object-Relational Mapper) que simplifica el acceso a bases de datos mediante el mapeo de objetos C# a tablas SQL.

```csharp
public class ApplicationDbContext : DbContext
{
    public DbSet<Usuario> Usuarios { get; set; }

    protected override void OnConfiguring(DbContextOptionsBuilder optionsBuilder)
    {
        optionsBuilder.UseSqlServer("Server=localhost;Database=MiApp;.. .");
    }
}
```

### 6. **ASP.NET Core Identity**

Proporciona un sistema completo de **autenticación y autorización** con soporte para usuarios, roles, claims, JWT, OAuth, etc.

```csharp
builder.Services.AddIdentity<ApplicationUser, IdentityRole>()
    .AddEntityFrameworkStores<ApplicationDbContext>()
    .AddDefaultTokenProviders();
```

### 7. **SignalR**

Biblioteca para agregar **comunicación en tiempo real** a las aplicaciones (WebSockets, Server-Sent Events).

```csharp
public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients.All.SendAsync("ReceiveMessage", user, message);
    }
}
```

### 8. **Blazor**

Framework para construir **aplicaciones web interactivas** usando C# en lugar de JavaScript (Blazor Server o Blazor WebAssembly).

### 9. **ASP.NET Core Middleware**
Componentes que procesan las solicitudes HTTP en un pipeline.  Ejemplos:  autenticación, logging, manejo de errores, compresión, etc.

```csharp
app.Use(async (context, next) =>
{
    Console.WriteLine($"Request: {context.Request.Path}");
    await next.Invoke();
    Console.WriteLine($"Response: {context.Response.StatusCode}");
});
```

### 10. **Logging y Monitoreo**

ASP.NET Core tiene logging integrado con `ILogger<T>`. También se integra con herramientas como **Serilog**, **Application Insights**, **ELK Stack**. 

```csharp
public class MiServicio
{
    private readonly ILogger<MiServicio> _logger;

    public MiServicio(ILogger<MiServicio> logger)
    {
        _logger = logger;
    }

    public void HacerAlgo()
    {
        _logger.LogInformation("Haciendo algo...");
    }
}
```

### 11. **Configuración y Options Pattern**

ASP.NET Core usa el **Options Pattern** para gestionar configuraciones de forma tipada. 

```csharp
public class MiConfiguracion
{
    public string ApiKey { get; set; }
    public int Timeout { get; set; }
}

// En Program.cs
builder.Services.Configure<MiConfiguracion>(builder.Configuration.GetSection("MiConfiguracion"));

// Uso en un servicio
public class MiServicio
{
    private readonly MiConfiguracion _config;

    public MiServicio(IOptions<MiConfiguracion> config)
    {
        _config = config.Value;
    }
}
```

---

## Servicios (Services) en ASP.NET Core

En ASP.NET Core, los **servicios** son clases que encapsulan la lógica de negocio y son registrados en el **contenedor de inyección de dependencias (DI Container)**. 

Los servicios son equivalentes a los **Beans** de Spring.  

### Tipos de servicios según su ciclo de vida:

| Ciclo de Vida | Descripción | Uso típico |
|: --------------|:------------|:-----------|
| **Transient** | Se crea una nueva instancia cada vez que se solicita | Servicios ligeros sin estado |
| **Scoped** | Se crea una nueva instancia por cada solicitud HTTP | Repositorios, DbContext |
| **Singleton** | Se crea una única instancia para toda la aplicación | Cachés, configuraciones globales |

### Registro de servicios:

```csharp
// Program.cs
var builder = WebApplication.CreateBuilder(args);

// Transient
builder.Services.AddTransient<IMiServicio, MiServicio>();

// Scoped
builder.Services.AddScoped<IRepositorio, UsuarioRepositorio>();

// Singleton
builder.Services.AddSingleton<ICacheService, CacheService>();

var app = builder.Build();
```

### Ejemplo de servicio:

```csharp
public interface IUsuarioService
{
    Task<Usuario> ObtenerPorIdAsync(int id);
}

public class UsuarioService :  IUsuarioService
{
    private readonly ApplicationDbContext _context;
    private readonly ILogger<UsuarioService> _logger;

    public UsuarioService(ApplicationDbContext context, ILogger<UsuarioService> logger)
    {
        _context = context;
        _logger = logger;
    }

    public async Task<Usuario> ObtenerPorIdAsync(int id)
    {
        _logger. LogInformation($"Obteniendo usuario con ID {id}");
        return await _context.Usuarios.FindAsync(id);
    }
}
```

### Inyección en un controlador:

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    private readonly IUsuarioService _usuarioService;

    public UsuariosController(IUsuarioService usuarioService)
    {
        _usuarioService = usuarioService;
    }

    [HttpGet("{id}")]
    public async Task<ActionResult<Usuario>> GetById(int id)
    {
        var usuario = await _usuarioService.ObtenerPorIdAsync(id);
        return usuario == null ? NotFound() : Ok(usuario);
    }
}
```

---

## Inversión de Control e Inyección de Dependencias

**Inversión de Control (IoC)** y **Inyección de Dependencias (DI)** son principios fundamentales en ASP.NET Core que facilitan la creación de aplicaciones modulares, testeables y mantenibles. 

### Inversión de Control (IoC)

**IoC** es un principio de diseño donde el **framework** (ASP.NET Core) controla la creación y gestión de objetos, en lugar de que el código de la aplicación lo haga manualmente. 

Esto reduce el acoplamiento entre clases y permite mayor flexibilidad. 

### Inyección de Dependencias (DI)

**DI** es una técnica que implementa IoC.  En lugar de que las clases creen sus propias dependencias, estas se **inyectan** por el framework.

**Ventajas de DI:**

✅ **Desacoplamiento**: Las clases no dependen de implementaciones concretas.

✅ **Testabilidad**:  Puedes sustituir dependencias reales por mocks en tests.

✅ **Mantenibilidad**: Cambios en dependencias no afectan a las clases que las usan.

### Ejemplo sin DI (acoplamiento fuerte):

```csharp
public class UsuarioController
{
    private readonly UsuarioService _usuarioService;

    public UsuarioController()
    {
        // ❌ Acoplamiento fuerte
        _usuarioService = new UsuarioService(new ApplicationDbContext());
    }
}
```

### Ejemplo con DI (acoplamiento débil):

```csharp
public class UsuarioController
{
    private readonly IUsuarioService _usuarioService;

    // ✅ La dependencia se inyecta
    public UsuarioController(IUsuarioService usuarioService)
    {
        _usuarioService = usuarioService;
    }
}
```

### Registro e inyección:

```csharp
// Registro en Program.cs
builder. Services.AddScoped<IUsuarioService, UsuarioService>();

// ASP.NET Core automáticamente inyecta IUsuarioService en UsuarioController
```

---

## Práctica de clase:  ASP.NET Core

**Objetivo:** Investigar sobre proyectos y servicios que conozcas que usen **ASP.NET Core**.

**Tareas:**

1. **Identifica al menos 3 proyectos o empresas** que usen ASP. NET Core (ej:  Stack Overflow, Azure Portal, aplicaciones internas de Microsoft, etc.).

2. Para cada proyecto, investiga y documenta:
   - **¿En qué partes de su arquitectura usan ASP.NET Core?** (ej: APIs REST, servicios web, aplicaciones en tiempo real, microservicios).
   - **¿Qué módulos o bibliotecas de ASP.NET Core utilizan?** (ej: Entity Framework Core, SignalR, Identity, etc.).
   - **¿Qué ventajas les proporciona ASP.NET Core?** (ej: rendimiento, multiplataforma, escalabilidad, integración con Azure, etc.).

3. Presenta tu investigación en un documento o presentación.

**Ejemplo de tabla:**

| Proyecto/Empresa | Uso de ASP.NET Core | Módulos Utilizados              | Ventajas Obtenidas                            |
| :--------------- | :------------------ | :------------------------------ | :-------------------------------------------- |
| Stack Overflow   | API REST backend    | ASP.NET Core MVC, Dapper, Redis | Alto rendimiento, escalabilidad               |
| Azure Portal     | Microservicios      | ASP.NET Core Web API, SignalR   | Multiplataforma, integración nativa con Azure |
| ...              | ...                 | ...                             | ...                                           |

---

Esta práctica te ayudará a entender cómo ASP.NET Core se utiliza en aplicaciones reales y qué beneficios ofrece en diferentes contextos arquitectónicos.  🚀🌐