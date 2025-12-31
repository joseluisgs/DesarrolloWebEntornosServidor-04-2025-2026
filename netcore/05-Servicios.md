# Servicios en ASP.NET Core

- [Servicios en ASP.NET Core](#servicios-en-aspnet-core)
  - [Arquitectura de Servicios](#arquitectura-de-servicios)
  - [Manejo de Excepciones y Errores](#manejo-de-excepciones-y-errores)
    - [Excepciones Personalizadas del Dominio](#excepciones-personalizadas-del-dominio)
    - [Middleware de Manejo Global de Excepciones](#middleware-de-manejo-global-de-excepciones)
    - [Problem Details (RFC 7807)](#problem-details-rfc-7807)
  - [Railway Oriented Programming (ROP) con Result](#railway-oriented-programming-rop-con-result)
    - [¿Qué es Railway Oriented Programming?](#qué-es-railway-oriented-programming)
    - [Instalación de CSharpFunctionalExtensions](#instalación-de-csharpfunctionalextensions)
    - [Tipos de Result](#tipos-de-result)
    - [Implementación con Result en Servicios](#implementación-con-result-en-servicios)
    - [Controlador con Result (Happy Path)](#controlador-con-result-happy-path)
    - [Extensiones para Convertir Result a ActionResult](#extensiones-para-convertir-result-a-actionresult)
    - [Ventajas de ROP sobre Excepciones](#ventajas-de-rop-sobre-excepciones)
    - [Comparación:  Excepciones vs Result](#comparación--excepciones-vs-result)
  - [Caché en ASP.NET Core](#caché-en-aspnet-core)
    - [IMemoryCache (Caché en Memoria)](#imemorycache-caché-en-memoria)
    - [IDistributedCache (Redis, SQL Server)](#idistributedcache-redis-sql-server)
  - [Patrón DTO (Data Transfer Object)](#patrón-dto-data-transfer-object)
  - [Mapeadores (Mappers)](#mapeadores-mappers)
    - [Mapeo Manual](#mapeo-manual)
    - [AutoMapper](#automapper)
  - [Validadores](#validadores)
    - [Data Annotations (Validación Básica)](#data-annotations-validación-básica)
    - [FluentValidation (Validación Avanzada)](#fluentvalidation-validación-avanzada)
  - [Arquitectura Completa con ROP](#arquitectura-completa-con-rop)
  - [Práctica de clase: Servicio Completo](#práctica-de-clase-servicio-completo)

![ASP.NET Core Services Banner](../images/banner05.png)

---

## Arquitectura de Servicios

Un **servicio** encapsula la lógica de negocio de nuestra aplicación, aplicando el **Principio de Responsabilidad Única (SRP)**. 

**Responsabilidades:**

- **Controlador**: Procesa las peticiones HTTP y delega al servicio.  
- **Servicio**:  Implementa la lógica de negocio, valida datos, aplica reglas, coordina repositorios.  
- **Repositorio**:  Abstrae el acceso a datos (base de datos, API externa, etc.).

![Spring Boot Architecture](../images/spring-boot-architecture2.png)

---

## Manejo de Excepciones y Errores

### Excepciones Personalizadas del Dominio

Crear excepciones específicas del dominio mejora la claridad y facilita el testing.

**Jerarquía de excepciones:**

```csharp
namespace FunkosApi.Exceptions;

// Excepción base
public abstract class FunkoException(string message) : Exception(message);

// Recurso no encontrado (404)
public class FunkoNotFoundException(string message) : FunkoException(message);

// Datos inválidos (400/422)
public class FunkoInvalidException(string message) : FunkoException(message);

// Conflicto (409)
public class FunkoConflictException(string message) : FunkoException(message);
```

**Uso en el servicio:**

```csharp
public async Task<Funko> GetByIdAsync(int id)
{
    var funko = await repository.GetByIdAsync(id);
    
    if (funko is null)
        throw new FunkoNotFoundException($"Funko con ID {id} no encontrado");

    return funko;
}
```

---

### Middleware de Manejo Global de Excepciones

Capturar excepciones globalmente mejora la consistencia de las respuestas de error.

**Crear middleware personalizado:**

```csharp
namespace FunkosApi.Middleware;

public class GlobalExceptionHandlerMiddleware(
    RequestDelegate next,
    ILogger<GlobalExceptionHandlerMiddleware> logger)
{
    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await next(context);
        }
        catch (Exception ex)
        {
            logger.LogError(ex, "Excepción no controlada:  {Message}", ex.Message);
            await HandleExceptionAsync(context, ex);
        }
    }

    private static Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        var (statusCode, title, detail) = exception switch
        {
            FunkoNotFoundException notFound => 
                (StatusCodes.Status404NotFound, "Recurso no encontrado", notFound.Message),
            
            FunkoInvalidException invalid => 
                (StatusCodes.Status400BadRequest, "Datos inválidos", invalid.Message),
            
            FunkoConflictException conflict => 
                (StatusCodes.Status409Conflict, "Conflicto", conflict.Message),
            
            _ => 
                (StatusCodes.Status500InternalServerError, "Error interno del servidor", 
                 "Ha ocurrido un error inesperado")
        };

        context.Response.ContentType = "application/json";
        context.Response.StatusCode = statusCode;

        var problemDetails = new ProblemDetails
        {
            Status = statusCode,
            Title = title,
            Detail = detail,
            Instance = context.Request.Path
        };

        return context.Response.WriteAsJsonAsync(problemDetails);
    }
}
```

**Registrar en `Program.cs`:**

```csharp
// Después de app.Build()
app.UseMiddleware<GlobalExceptionHandlerMiddleware>();
```

---

### Problem Details (RFC 7807)

ASP.NET Core soporta el estándar **RFC 7807** para respuestas de error estructuradas.

**Configurar en `Program.cs`:**

```csharp
builder.Services.AddProblemDetails(options =>
{
    options.CustomizeProblemDetails = context =>
    {
        context.ProblemDetails.Instance = context.HttpContext.Request.Path;
        context.ProblemDetails.Extensions["traceId"] = context. HttpContext.TraceIdentifier;
        context.ProblemDetails.Extensions["timestamp"] = DateTime.UtcNow;
    };
});

// Después de app.Build()
app.UseExceptionHandler(); // Middleware integrado de ASP.NET Core
app.UseStatusCodePages(); // Para errores 4xx/5xx sin excepción
```

**Ejemplo de respuesta:**

```json
{
  "type": "https://tools.ietf.org/html/rfc7231#section-6.5.4",
  "title": "Recurso no encontrado",
  "status":  404,
  "detail": "Funko con ID 999 no encontrado",
  "instance": "/api/funkos/999",
  "traceId": "00-abc123.. .",
  "timestamp": "2025-01-15T10:30:00Z"
}
```

---

## Railway Oriented Programming (ROP) con Result

### ¿Qué es Railway Oriented Programming? 

**Railway Oriented Programming (ROP)** es un patrón de diseño funcional que modela el flujo de ejecución como dos vías de tren: 

- **Vía del éxito (Success Track)**: Todo funciona correctamente. 
- **Vía del error (Failure Track)**: Ocurre un error y se propaga automáticamente.

![ROP Diagram](../images/rop.webp)

**Ventajas sobre excepciones tradicionales:**

✅ **Errores como valores**:  Los errores son parte del tipo de retorno, no excepciones ocultas.

✅ **Flujo explícito**: Forzamos el manejo de errores en tiempo de compilación.

✅ **Más fácil de testear**: No necesitas mockear excepciones.

✅ **Composición funcional**: Encadena operaciones fácilmente con `Bind`, `Map`, `Match`.

✅ **Controladores más limpios**: El controlador solo mapea `Result` a `ActionResult`.

---

### Instalación de CSharpFunctionalExtensions

La biblioteca **CSharpFunctionalExtensions** proporciona el tipo `Result<T>` para implementar ROP.

```bash
dotnet add package CSharpFunctionalExtensions
```

---

### Tipos de Result

```csharp
using CSharpFunctionalExtensions;

// Result sin valor (solo éxito/fallo)
Result result = Result.Success();
Result failure = Result.Failure("Error message");

// Result con valor de éxito
Result<Funko> funkoResult = Result.Success(funko);
Result<Funko> notFound = Result.Failure<Funko>("Funko no encontrado");

// Result con tipo de error personalizado
Result<Funko, FunkoError> customResult = Result.Success<Funko, FunkoError>(funko);
Result<Funko, FunkoError> customError = Result.Failure<Funko, FunkoError>(new FunkoError("Error"));
```

**Métodos principales:**

- `Result.Success(value)`: Crea un resultado exitoso. 
- `Result.Failure(error)`: Crea un resultado fallido. 
- `result.IsSuccess`: Indica si fue exitoso.
- `result.IsFailure`: Indica si falló.
- `result.Value`: Obtiene el valor (solo si es exitoso).
- `result.Error`: Obtiene el mensaje de error (solo si falló).

**Métodos de composición:**

- `Bind(func)`: Encadena operaciones que devuelven `Result`.
- `Map(func)`: Transforma el valor si es exitoso.
- `Tap(action)`: Ejecuta una acción sin modificar el resultado.
- `Match(onSuccess, onFailure)`: Ejecuta una función según el resultado.

---

### Implementación con Result en Servicios

**Definir tipos de error personalizados:**

```csharp
namespace FunkosApi. Errors;

public record FunkoError(string Code, string Message)
{
    public static FunkoError NotFound(int id) => 
        new("FUNKO_NOT_FOUND", $"Funko con ID {id} no encontrado");

    public static FunkoError InvalidData(string field, string reason) => 
        new("FUNKO_INVALID", $"Campo '{field}' inválido:  {reason}");

    public static FunkoError Conflict(string message) => 
        new("FUNKO_CONFLICT", message);

    public static FunkoError ValidationFailed(string message) => 
        new("VALIDATION_FAILED", message);
}
```

**Servicio con Result:**

```csharp
using CSharpFunctionalExtensions;

namespace FunkosApi.Services;

public interface IFunkoService
{
    Task<Result<IEnumerable<Funko>>> GetAllAsync(string? categoria = null);
    Task<Result<Funko, FunkoError>> GetByIdAsync(int id);
    Task<Result<Funko, FunkoError>> CreateAsync(CreateFunkoDto dto);
    Task<Result<Funko, FunkoError>> UpdateAsync(int id, UpdateFunkoDto dto);
    Task<Result<Unit, FunkoError>> DeleteAsync(int id);
}

public class FunkoService(
    IFunkoRepository repository,
    ILogger<FunkoService> logger,
    IMemoryCache cache) : IFunkoService
{
    private const string CacheKeyPrefix = "funko_";

    public async Task<Result<IEnumerable<Funko>>> GetAllAsync(string? categoria = null)
    {
        logger.LogInformation("Obteniendo funkos - Categoría: {Categoria}", categoria ??  "todas");

        var funkos = await repository.GetAllAsync();

        if (! string.IsNullOrWhiteSpace(categoria))
            funkos = funkos.Where(f => f.Categoria. Equals(categoria, StringComparison.OrdinalIgnoreCase));

        return Result. Success(funkos);
    }

    public async Task<Result<Funko, FunkoError>> GetByIdAsync(int id)
    {
        logger.LogInformation("Buscando funko con ID: {Id}", id);

        // Intentar obtener de caché
        var cacheKey = $"{CacheKeyPrefix}{id}";
        if (cache.TryGetValue(cacheKey, out Funko? cachedFunko) && cachedFunko is not null)
        {
            logger.LogInformation("Funko {Id} obtenido de caché", id);
            return Result.Success<Funko, FunkoError>(cachedFunko);
        }

        // Si no está en caché, buscar en repositorio
        var funko = await repository.GetByIdAsync(id);

        if (funko is null)
        {
            logger.LogWarning("Funko con ID {Id} no encontrado", id);
            return Result. Failure<Funko, FunkoError>(FunkoError.NotFound(id));
        }

        // Guardar en caché
        cache.Set(cacheKey, funko, TimeSpan.FromMinutes(5));
        return Result.Success<Funko, FunkoError>(funko);
    }

    public async Task<Result<Funko, FunkoError>> CreateAsync(CreateFunkoDto dto)
    {
        logger.LogInformation("Creando funko: {Nombre}", dto.Nombre);

        // Validar lógica de negocio
        if (dto.Precio <= 0)
            return Result.Failure<Funko, FunkoError>(
                FunkoError.InvalidData("Precio", "debe ser mayor a 0"));

        if (string.IsNullOrWhiteSpace(dto.Nombre))
            return Result.Failure<Funko, FunkoError>(
                FunkoError.InvalidData("Nombre", "no puede estar vacío"));

        // Verificar si ya existe uno con el mismo nombre (ejemplo de conflicto)
        var existente = await repository.GetByNombreAsync(dto.Nombre);
        if (existente is not null)
            return Result. Failure<Funko, FunkoError>(
                FunkoError.Conflict($"Ya existe un Funko con el nombre '{dto.Nombre}'"));

        var funko = new Funko
        {
            Nombre = dto.Nombre,
            Precio = dto.Precio,
            Cantidad = dto.Cantidad,
            Categoria = dto.Categoria,
            Imagen = dto.Imagen
        };

        var creado = await repository.CreateAsync(funko);

        // Guardar en caché
        cache. Set($"{CacheKeyPrefix}{creado.Id}", creado, TimeSpan.FromMinutes(5));

        return Result.Success<Funko, FunkoError>(creado);
    }

    public async Task<Result<Funko, FunkoError>> UpdateAsync(int id, UpdateFunkoDto dto)
    {
        logger.LogInformation("Actualizando funko {Id}", id);

        // Buscar el funko existente (usando Result)
        var existenteResult = await GetByIdAsync(id);
        if (existenteResult.IsFailure)
            return existenteResult; // Propagar el error

        var existente = existenteResult.Value;

        // Validar cambios
        if (dto. Precio. HasValue && dto.Precio. Value <= 0)
            return Result.Failure<Funko, FunkoError>(
                FunkoError.InvalidData("Precio", "debe ser mayor a 0"));

        // Aplicar cambios
        var actualizado = existente with
        {
            Nombre = dto.Nombre ?? existente.Nombre,
            Precio = dto.Precio ?? existente.Precio,
            Cantidad = dto.Cantidad ?? existente.Cantidad,
            Categoria = dto.Categoria ?? existente.Categoria,
            Imagen = dto.Imagen ?? existente.Imagen,
            FechaActualizacion = DateTime.UtcNow
        };

        var resultado = await repository.UpdateAsync(actualizado);

        if (resultado is null)
            return Result. Failure<Funko, FunkoError>(
                FunkoError.NotFound(id));

        // Actualizar caché
        cache.Set($"{CacheKeyPrefix}{id}", resultado, TimeSpan.FromMinutes(5));

        return Result.Success<Funko, FunkoError>(resultado);
    }

    public async Task<Result<Unit, FunkoError>> DeleteAsync(int id)
    {
        logger.LogInformation("Eliminando funko {Id}", id);

        var eliminado = await repository.DeleteAsync(id);

        if (!eliminado)
            return Result. Failure<Unit, FunkoError>(FunkoError.NotFound(id));

        // Eliminar de caché
        cache.Remove($"{CacheKeyPrefix}{id}");

        return Result.Success<Unit, FunkoError>(Unit.Value); // Unit = void exitoso
    }
}
```

---

### Controlador con Result (Happy Path)

El controlador queda **mucho más limpio** al solo mapear `Result` a `ActionResult`:

```csharp
using CSharpFunctionalExtensions;
using Microsoft.AspNetCore. Mvc;

namespace FunkosApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class FunkosController(
    IFunkoService service,
    ILogger<FunkosController> logger) : ControllerBase
{
    // GET: api/funkos
    [HttpGet]
    [ProducesResponseType(typeof(IEnumerable<FunkoResponseDto>), StatusCodes.Status200OK)]
    public async Task<ActionResult<IEnumerable<FunkoResponseDto>>> GetAll(
        [FromQuery] string?  categoria = null)
    {
        var result = await service.GetAllAsync(categoria);

        return result.Match(
            onSuccess: funkos => Ok(funkos. Select(f => f.ToResponseDto())),
            onFailure: error => StatusCode(500, error) // Nunca debería fallar GetAll
        );
    }

    // GET: api/funkos/5
    [HttpGet("{id:int}")]
    [ProducesResponseType(typeof(FunkoResponseDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<ActionResult<FunkoResponseDto>> GetById(int id)
    {
        var result = await service.GetByIdAsync(id);

        return result.Match(
            onSuccess: funko => Ok(funko.ToResponseDto()),
            onFailure: error => NotFound(new ProblemDetails
            {
                Status = StatusCodes.Status404NotFound,
                Title = error.Code,
                Detail = error.Message
            })
        );
    }

    // POST:  api/funkos
    [HttpPost]
    [ProducesResponseType(typeof(FunkoResponseDto), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status409Conflict)]
    public async Task<ActionResult<FunkoResponseDto>> Create([FromBody] CreateFunkoDto dto)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        var result = await service.CreateAsync(dto);

        return result.Match(
            onSuccess: funko => CreatedAtAction(
                nameof(GetById),
                new { id = funko.Id },
                funko. ToResponseDto()),
            onFailure: error => error. Code switch
            {
                "FUNKO_INVALID" => BadRequest(new ProblemDetails
                {
                    Status = StatusCodes. Status400BadRequest,
                    Title = error.Code,
                    Detail = error.Message
                }),
                "FUNKO_CONFLICT" => Conflict(new ProblemDetails
                {
                    Status = StatusCodes.Status409Conflict,
                    Title = error.Code,
                    Detail = error.Message
                }),
                _ => StatusCode(500, error)
            }
        );
    }

    // PUT: api/funkos/5
    [HttpPut("{id:int}")]
    [ProducesResponseType(typeof(FunkoResponseDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<ActionResult<FunkoResponseDto>> Update(int id, [FromBody] UpdateFunkoDto dto)
    {
        if (!ModelState.IsValid)
            return BadRequest(ModelState);

        var result = await service.UpdateAsync(id, dto);

        return result.Match(
            onSuccess: funko => Ok(funko.ToResponseDto()),
            onFailure: error => error.Code switch
            {
                "FUNKO_NOT_FOUND" => NotFound(new ProblemDetails
                {
                    Status = StatusCodes.Status404NotFound,
                    Title = error.Code,
                    Detail = error.Message
                }),
                "FUNKO_INVALID" => BadRequest(new ProblemDetails
                {
                    Status = StatusCodes.Status400BadRequest,
                    Title = error.Code,
                    Detail = error.Message
                }),
                _ => StatusCode(500, error)
            }
        );
    }

    // DELETE: api/funkos/5
    [HttpDelete("{id:int}")]
    [ProducesResponseType(StatusCodes.Status204NoContent)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<IActionResult> Delete(int id)
    {
        var result = await service.DeleteAsync(id);

        return result. Match(
            onSuccess: _ => NoContent(),
            onFailure: error => NotFound(new ProblemDetails
            {
                Status = StatusCodes.Status404NotFound,
                Title = error.Code,
                Detail = error.Message
            })
        );
    }
}
```

---

### Extensiones para Convertir Result a ActionResult

Para simplificar aún más el controlador, podemos crear extensiones:

```csharp
namespace FunkosApi.Extensions;

public static class ResultExtensions
{
    public static ActionResult<T> ToActionResult<T>(
        this Result<T, FunkoError> result,
        ControllerBase controller)
    {
        return result.Match(
            onSuccess: value => controller.Ok(value),
            onFailure: error => error.Code switch
            {
                "FUNKO_NOT_FOUND" => controller.NotFound(new ProblemDetails
                {
                    Status = StatusCodes.Status404NotFound,
                    Title = error.Code,
                    Detail = error. Message
                }),
                "FUNKO_INVALID" => controller.BadRequest(new ProblemDetails
                {
                    Status = StatusCodes.Status400BadRequest,
                    Title = error.Code,
                    Detail = error.Message
                }),
                "FUNKO_CONFLICT" => controller.Conflict(new ProblemDetails
                {
                    Status = StatusCodes.Status409Conflict,
                    Title = error.Code,
                    Detail = error.Message
                }),
                _ => controller.StatusCode(500, error)
            }
        );
    }

    public static ActionResult ToActionResult(
        this Result<Unit, FunkoError> result,
        ControllerBase controller)
    {
        return result.Match(
            onSuccess: _ => controller.NoContent(),
            onFailure:  error => error.Code switch
            {
                "FUNKO_NOT_FOUND" => controller. NotFound(new ProblemDetails
                {
                    Status = StatusCodes.Status404NotFound,
                    Title = error. Code,
                    Detail = error.Message
                }),
                _ => controller.StatusCode(500, error)
            }
        );
    }
}
```

**Controlador simplificado con extensiones:**

```csharp
[HttpGet("{id:int}")]
public async Task<ActionResult<FunkoResponseDto>> GetById(int id)
{
    var result = await service.GetByIdAsync(id);
    return result
        .Map(funko => funko.ToResponseDto())
        .ToActionResult(this);
}

[HttpDelete("{id:int}")]
public async Task<IActionResult> Delete(int id)
{
    var result = await service.DeleteAsync(id);
    return result. ToActionResult(this);
}
```

---

### Ventajas de ROP sobre Excepciones

| Aspecto                   | Excepciones                    | Result (ROP)                     |
| :------------------------ | :----------------------------- | :------------------------------- |
| **Visibilidad del error** | Oculto hasta runtime           | Explícito en la firma del método |
| **Manejo obligatorio**    | ❌ Opcional (puede olvidarse)   | ✅ Forzado por el compilador      |
| **Testing**               | Difícil (mockear excepciones)  | Fácil (solo verificar Result)    |
| **Composición**           | Difícil (try-catch anidados)   | Fácil (`Bind`, `Map`, `Match`)   |
| **Performance**           | ❌ Overhead de stack unwinding  | ✅ Sin overhead                   |
| **Claridad del código**   | Mezclado con lógica de negocio | Separado (happy path vs error)   |

---

### Comparación:  Excepciones vs Result

**Con Excepciones (enfoque tradicional):**

```csharp
// Servicio
public async Task<Funko> GetByIdAsync(int id)
{
    var funko = await repository.GetByIdAsync(id);
    
    if (funko is null)
        throw new FunkoNotFoundException($"Funko {id} no encontrado");

    return funko;
}

// Controlador
[HttpGet("{id}")]
public async Task<ActionResult<Funko>> GetById(int id)
{
    try
    {
        var funko = await service.GetByIdAsync(id);
        return Ok(funko);
    }
    catch (FunkoNotFoundException ex)
    {
        return NotFound(ex.Message);
    }
    catch (Exception ex)
    {
        return StatusCode(500, ex.Message);
    }
}
```

**Con Result (ROP):**

```csharp
// Servicio
public async Task<Result<Funko, FunkoError>> GetByIdAsync(int id)
{
    var funko = await repository.GetByIdAsync(id);
    
    return funko is null
        ? Result.Failure<Funko, FunkoError>(FunkoError.NotFound(id))
        : Result.Success<Funko, FunkoError>(funko);
}

// Controlador
[HttpGet("{id}")]
public async Task<ActionResult<Funko>> GetById(int id)
{
    var result = await service.GetByIdAsync(id);
    
    return result.Match(
        onSuccess: funko => Ok(funko),
        onFailure: error => NotFound(error.Message)
    );
}
```

**Conclusión:** El enfoque con `Result` es más claro, explícito y fácil de mantener. 

---

## Caché en ASP.NET Core

### IMemoryCache (Caché en Memoria)

Caché local en la memoria del proceso de la aplicación. 

**Instalar paquete (ya incluido en web projects):**

```bash
dotnet add package Microsoft.Extensions.Caching.Memory
```

**Registrar en `Program.cs`:**

```csharp
builder.Services.AddMemoryCache();
```

**Uso en un servicio:**

```csharp
public class FunkoService(IMemoryCache cache, IFunkoRepository repository) : IFunkoService
{
    private const string CacheKeyPrefix = "funko_";
    private const string CacheKeyAll = "funkos_all";

    public async Task<IEnumerable<Funko>> GetAllAsync()
    {
        // Intentar obtener de caché
        if (cache.TryGetValue(CacheKeyAll, out IEnumerable<Funko>? cached) && cached is not null)
            return cached;

        // Si no está en caché, obtener de repositorio
        var funkos = await repository.GetAllAsync();

        // Guardar en caché con expiración
        var cacheOptions = new MemoryCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan. FromMinutes(5),
            SlidingExpiration = TimeSpan.FromMinutes(2)
        };

        cache. Set(CacheKeyAll, funkos, cacheOptions);
        return funkos;
    }

    public async Task<Funko> GetByIdAsync(int id)
    {
        var cacheKey = $"{CacheKeyPrefix}{id}";

        // Usar GetOrCreateAsync para simplificar
        return await cache. GetOrCreateAsync(cacheKey, async entry =>
        {
            entry.AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(5);
            
            var funko = await repository. GetByIdAsync(id);
            if (funko is null)
                throw new FunkoNotFoundException($"Funko {id} no encontrado");

            return funko;
        }) ??  throw new FunkoNotFoundException($"Funko {id} no encontrado");
    }

    public async Task<Funko> CreateAsync(CreateFunkoDto dto)
    {
        var funko = await repository.CreateAsync(/* ... */);

        // Invalidar caché de listado completo
        cache.Remove(CacheKeyAll);

        // Agregar a caché individual
        cache.Set($"{CacheKeyPrefix}{funko.Id}", funko, TimeSpan.FromMinutes(5));

        return funko;
    }

    public async Task DeleteAsync(int id)
    {
        await repository.DeleteAsync(id);

        // Invalidar cachés
        cache.Remove($"{CacheKeyPrefix}{id}");
        cache.Remove(CacheKeyAll);
    }
}
```

---

### IDistributedCache (Redis, SQL Server)

Caché distribuida para aplicaciones con múltiples instancias. 

**Instalar Redis:**

```bash
dotnet add package Microsoft.Extensions.Caching. StackExchangeRedis
```

**Configurar en `appsettings.json`:**

```json
{
  "ConnectionStrings": {
    "Redis": "localhost:6379"
  }
}
```

**Registrar en `Program.cs`:**

```csharp
builder.Services.AddStackExchangeRedisCache(options =>
{
    options.Configuration = builder.Configuration.GetConnectionString("Redis");
    options.InstanceName = "FunkosApi_";
});
```

**Uso:**

```csharp
public class FunkoService(IDistributedCache cache, IFunkoRepository repository) : IFunkoService
{
    public async Task<Funko? > GetByIdAsync(int id)
    {
        var cacheKey = $"funko_{id}";

        // Intentar obtener de caché
        var cachedData = await cache.GetStringAsync(cacheKey);
        if (cachedData is not null)
            return JsonSerializer.Deserialize<Funko>(cachedData);

        // Si no está, obtener de repositorio
        var funko = await repository.GetByIdAsync(id);
        if (funko is null) return null;

        // Guardar en caché
        var options = new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = TimeSpan.FromMinutes(10)
        };

        await cache.SetStringAsync(cacheKey, JsonSerializer. Serialize(funko), options);
        return funko;
    }
}
```

---

## Patrón DTO (Data Transfer Object)

Los **DTOs** transportan datos entre capas, evitando exponer entidades del dominio directamente.

![DTO Pattern](../images/dto. svg)

**Ventajas:**

✅ **Desacoplamiento**:  API independiente del modelo de dominio

✅ **Seguridad**: Ocultar campos sensibles

✅ **Flexibilidad**: Diferentes representaciones para distintos clientes

✅ **Versionado**: Facilita cambios en la API sin afectar el dominio

**Ejemplo de DTOs en . NET 10 / C# 14:**

```csharp
namespace FunkosApi.DTOs;

// DTO para crear un funko
public record CreateFunkoDto(
    [Required(ErrorMessage = "El nombre es obligatorio")]
    [StringLength(100, MinimumLength = 3, ErrorMessage = "El nombre debe tener entre 3 y 100 caracteres")]
    string Nombre,

    [Range(0.01, double.MaxValue, ErrorMessage = "El precio debe ser mayor a 0")]
    decimal Precio,

    [Range(0, int.MaxValue, ErrorMessage = "La cantidad no puede ser negativa")]
    int Cantidad,

    [Required(ErrorMessage = "La categoría es obligatoria")]
    string Categoria,

    string? Imagen = null
);

// DTO para actualizar (todos los campos opcionales)
public record UpdateFunkoDto(
    [StringLength(100, MinimumLength = 3)]
    string? Nombre,

    [Range(0.01, double.MaxValue)]
    decimal? Precio,

    [Range(0, int.MaxValue)]
    int? Cantidad,

    string? Categoria,

    string?  Imagen
);

// DTO de respuesta
public record FunkoResponseDto(
    int Id,
    string Nombre,
    decimal Precio,
    int Cantidad,
    string Categoria,
    string?  Imagen,
    DateTime FechaCreacion,
    DateTime FechaActualizacion
);
```

---

## Mapeadores (Mappers)

Los **mapeadores** convierten entre DTOs y modelos del dominio.

![Mapper](../images/mapeador.jpg)

### Mapeo Manual

```csharp
namespace FunkosApi.Mappers;

public static class FunkoMapper
{
    // De modelo a DTO de respuesta
    public static FunkoResponseDto ToResponseDto(this Funko funko) => new(
        funko.Id,
        funko.Nombre,
        funko.Precio,
        funko.Cantidad,
        funko.Categoria,
        funko.Imagen,
        funko.FechaCreacion,
        funko.FechaActualizacion
    );

    // Múltiples modelos a DTOs
    public static IEnumerable<FunkoResponseDto> ToResponseDto(this IEnumerable<Funko> funkos) =>
        funkos.Select(ToResponseDto);

    // De DTO de creación a modelo
    public static Funko ToModel(this CreateFunkoDto dto) => new()
    {
        Nombre = dto.Nombre,
        Precio = dto.Precio,
        Cantidad = dto. Cantidad,
        Categoria = dto.Categoria,
        Imagen = dto.Imagen
    };
}
```

---

### AutoMapper

**Instalar:**

```bash
dotnet add package AutoMapper. Extensions.Microsoft.DependencyInjection
```

**Crear perfil:**

```csharp
using AutoMapper;

namespace FunkosApi.Mappers;

public class FunkoMappingProfile : Profile
{
    public FunkoMappingProfile()
    {
        CreateMap<Funko, FunkoResponseDto>();
        CreateMap<CreateFunkoDto, Funko>()
            .ForMember(dest => dest.FechaCreacion, opt => opt.MapFrom(_ => DateTime. UtcNow));
    }
}
```

**Registrar:**

```csharp
builder.Services.AddAutoMapper(typeof(FunkoMappingProfile));
```

---

## Validadores

### Data Annotations (Validación Básica)

```csharp
public record CreateFunkoDto(
    [Required, StringLength(100, MinimumLength = 3)]
    string Nombre,

    [Range(0.01, double.MaxValue)]
    decimal Precio
);
```

---

### FluentValidation (Validación Avanzada)

**Instalar:**

```bash
dotnet add package FluentValidation. AspNetCore
```

**Crear validador:**

```csharp
using FluentValidation;

namespace FunkosApi. Validators;

public class CreateFunkoDtoValidator : AbstractValidator<CreateFunkoDto>
{
    public CreateFunkoDtoValidator()
    {
        RuleFor(x => x.Nombre)
            .NotEmpty().WithMessage("El nombre es obligatorio")
            .Length(3, 100);

        RuleFor(x => x.Precio)
            .GreaterThan(0).WithMessage("El precio debe ser mayor a 0");

        RuleFor(x => x.Categoria)
            .NotEmpty()
            .Must(BeValidCategory).WithMessage("Categoría no válida");
    }

    private bool BeValidCategory(string categoria)
    {
        var validCategories = new[] { "Marvel", "DC", "Disney", "Anime", "StarWars" };
        return validCategories.Contains(categoria, StringComparer.OrdinalIgnoreCase);
    }
}
```

**Registrar:**

```csharp
using FluentValidation;

builder. Services.AddFluentValidationAutoValidation();
builder.Services.AddValidatorsFromAssemblyContaining<CreateFunkoDtoValidator>();
```

---

## Arquitectura Completa con ROP

```
┌────────────────────────────────────────────────────┐
│              HTTP Request (JSON)                   │
└────────────────┬───────────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────────┐
│            FunkosController                        │
│  - Recibe peticiones HTTP                          │
│  - Valida ModelState                               │
│  - Delega al servicio                              │
│  - Convierte Result<T> a ActionResult              │
└────────────────┬───────────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────────┐
│              IFunkoService                         │
│  - Lógica de negocio                               │
│  - Validaciones de dominio                         │
│  - Gestión de caché                                │
│  - Devuelve Result<T, FunkoError>                  │
└────────────────┬───────────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────────┐
│           IFunkoRepository                         │
│  - Acceso a datos                                  │
│  - Operaciones CRUD                                │
│  - Devuelve T? (nullable)                          │
└────────────────┬───────────────────────────────────┘
                 │
                 ▼
┌────────────────────────────────────────────────────┐
│            Database / Storage                      │
└────────────────────────────────────────────────────┘
```

---

## Práctica de clase: Servicio Completo

**Objetivo:** Crear un servicio completo con Railway Oriented Programming.

**Tareas obligatorias:**

1. ✅ **Implementar servicio con ROP**: 
   - Usar `Result<T, FunkoError>` en todos los métodos
   - Definir tipos de error personalizados (`FunkoError`)
   - Manejar errores sin excepciones

2. ✅ **Implementar caché** (`IMemoryCache`)

3. ✅ **Crear DTOs**:
   - `CreateFunkoDto`
   - `UpdateFunkoDto`
   - `FunkoResponseDto`

4. ✅ **Crear mapeadores** (manual o AutoMapper)

5. ✅ **Implementar validadores** (FluentValidation)

6. ✅ **Controlador limpio**:
   - Usar `Match` para convertir `Result` a `ActionResult`
   - Sin `try-catch`
   - Código claro y conciso

7. ✅ **Probar con Postman o archivo `.http`**

**Criterios de evaluación:**

- ✅ El servicio devuelve `Result<T, FunkoError>` en todos los métodos
- ✅ Los errores se manejan sin excepciones (Railway Oriented Programming)
- ✅ El controlador está limpio y usa `Match` para convertir `Result` a `ActionResult`
- ✅ La caché funciona correctamente
- ✅ Los DTOs están bien diseñados y validados
- ✅ El mapeador funciona correctamente
- ✅ Todos los endpoints devuelven los códigos HTTP correctos
- ✅ El código está limpio, organizado y documentado

---

