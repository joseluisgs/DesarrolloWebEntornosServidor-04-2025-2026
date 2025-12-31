# Redis con ASP.NET Core

- [Redis con ASP.NET Core](#redis-con-aspnet-core)
  - [Introducción](#introducción)
    - [¿Qué es Redis?](#qué-es-redis)
    - [¿Para qué se usa Redis?](#para-qué-se-usa-redis)
    - [¿Por qué usar Redis como caché?](#por-qué-usar-redis-como-caché)
      - [Ventajas de Redis vs Caché en memoria local](#ventajas-de-redis-vs-caché-en-memoria-local)
  - [Paso 1: Configuración Inicial del Proyecto](#paso-1-configuración-inicial-del-proyecto)
    - [1.1 Paquetes NuGet](#11-paquetes-nuget)
    - [1.2 Variables de entorno](#12-variables-de-entorno)
  - [Paso 2: Configuración de Docker](#paso-2-configuración-de-docker)
    - [2.1 Docker Compose para Desarrollo](#21-docker-compose-para-desarrollo)
    - [2.2 Docker Compose para Producción](#22-docker-compose-para-producción)
  - [Paso 3: Configuración de ASP. NET Core](#paso-3-configuración-de-asp-net-core)
    - [3.1 Configuración para Desarrollo](#31-configuración-para-desarrollo)
    - [3.2 Configuración para Producción](#32-configuración-para-producción)
    - [3.3 Configuración en Program. cs](#33-configuración-en-program-cs)
  - [Paso 4: Servicio de Caché](#paso-4-servicio-de-caché)
    - [4.1 Interfaz ICacheService](#41-interfaz-icacheservice)
    - [4.2 Implementación con MemoryCache (Desarrollo)](#42-implementación-con-memorycache-desarrollo)
    - [4.3 Implementación con Redis (Producción)](#43-implementación-con-redis-producción)
  - [Paso 5: Uso en Servicios](#paso-5-uso-en-servicios)
    - [5.1 Ejemplo con Funkos](#51-ejemplo-con-funkos)
    - [5.2 Ejemplo con Categorías](#52-ejemplo-con-categorías)
  - [Paso 6: Decorador de Caché (Patrón Decorator)](#paso-6-decorador-de-caché-patrón-decorator)
    - [6.1 Servicio Base](#61-servicio-base)
    - [6.2 Decorador con Caché](#62-decorador-con-caché)
    - [6.3 Registro en Program.cs](#63-registro-en-programcs)
  - [Paso 7: Monitoreo y Logs](#paso-7-monitoreo-y-logs)
    - [7.1 Configuración de Logs](#71-configuración-de-logs)
    - [7.2 Middleware de Logging](#72-middleware-de-logging)
  - [Paso 8: Testing](#paso-8-testing)
    - [8.1 Test de Servicio de Caché](#81-test-de-servicio-de-caché)
    - [8.2 Test con Mock de Redis](#82-test-con-mock-de-redis)
  - [Paso 9: Ejecución y Pruebas](#paso-9-ejecución-y-pruebas)
    - [9.1 Levantar el entorno](#91-levantar-el-entorno)
    - [9.2 Probar la aplicación](#92-probar-la-aplicación)
    - [9.3 Verificar funcionamiento](#93-verificar-funcionamiento)
  - [Paso 10: Estrategias Avanzadas](#paso-10-estrategias-avanzadas)
    - [10.1 Cache-Aside Pattern](#101-cache-aside-pattern)
    - [10.2 Write-Through Pattern](#102-write-through-pattern)
    - [10.3 Invalidación por Eventos](#103-invalidación-por-eventos)
  - [Resumen de Beneficios](#resumen-de-beneficios)
    - [✅ Lo que conseguimos](#-lo-que-conseguimos)
    - [⚡ Mejoras de rendimiento esperadas](#-mejoras-de-rendimiento-esperadas)
    - [🎯 Casos de uso ideales para Redis](#-casos-de-uso-ideales-para-redis)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Ejercicio Propuesto:  Redis para Funkos](#ejercicio-propuesto--redis-para-funkos)
    - [Requisitos](#requisitos)
  - [Conclusión](#conclusión)


![Redis Banner](../images/banner17.jpg)


---

## Introducción

### ¿Qué es Redis?

**Redis** (Remote Dictionary Server) es una base de datos NoSQL en memoria de código abierto que funciona como almacén de estructuras de datos clave-valor.  Es extremadamente rápida ya que mantiene todos los datos en memoria RAM.

### ¿Para qué se usa Redis?

Redis se utiliza principalmente para: 

- **🚀 Caché de aplicaciones**: Almacenar datos frecuentemente accedidos
- **📦 Almacenamiento de datos en memoria**: Para acceso rápido y compartido
- **📊 Sesiones de usuario**: Mantener estado de sesiones web
- **📈 Contadores en tiempo real**: Likes, visitas, estadísticas
- **🔔 Pub/Sub**: Sistema de mensajería
- **⏰ Datos temporales**: Con TTL (Time To Live)
- **🎯 Colas de tareas**: Para procesamiento asíncrono

### ¿Por qué usar Redis como caché?

#### Ventajas de Redis vs Caché en memoria local

| Aspecto | Caché Local (MemoryCache) | Redis |
|: --------|:--------------------------|:------|
| **Persistencia** | ❌ Se pierde al reiniciar | ✅ Persiste datos |
| **Escalabilidad** | ❌ Una instancia | ✅ Múltiples instancias |
| **Memoria** | ❌ Limitada por proceso | ✅ Memoria dedicada |
| **TTL** | ✅ Básico | ✅ Avanzado y flexible |
| **Distribución** | ❌ No compartida | ✅ Compartida entre apps |
| **Monitoreo** | ❌ Limitado | ✅ Herramientas especializadas |

---

## Paso 1: Configuración Inicial del Proyecto

### 1.1 Paquetes NuGet

```bash
# Caché distribuida con Redis
dotnet add package Microsoft.Extensions.Caching. StackExchangeRedis

# Caché en memoria (para desarrollo)
dotnet add package Microsoft.Extensions.Caching.Memory

# Serialización JSON
dotnet add package System.Text.Json
```

### 1.2 Variables de entorno

**.  env:**

```bash
# Redis Configuration
REDIS_HOST=redis-db
REDIS_PORT=6379
REDIS_PASSWORD=redisPassword123
REDIS_DATABASE=0
REDIS_SSL=false
REDIS_TIMEOUT=3000
REDIS_POOL_SIZE=20
```

---

## Paso 2: Configuración de Docker

### 2.1 Docker Compose para Desarrollo

**docker-compose. dev.yml:**

```yaml
version: '3.8'

services:
  # Redis (sin persistencia para desarrollo)
  redis-db:
    container_name: funkos-redis-dev
    image: redis:7-alpine
    restart: always
    command: redis-server --requirepass ${REDIS_PASSWORD}
    ports:
      - "${REDIS_PORT}:6379"
    networks:
      - funkos-network
    
  # Redis Commander para administración web
  redis-commander:
    container_name: funkos-redis-commander
    image: rediscommander/redis-commander:latest
    restart: always
    ports:
      - "8082:8081"
    environment: 
      REDIS_HOSTS: local:redis-db:${REDIS_PORT}: ${REDIS_DATABASE}: ${REDIS_PASSWORD}
    depends_on:
      - redis-db
    networks:
      - funkos-network

networks:
  funkos-network: 
    driver: bridge
```

### 2.2 Docker Compose para Producción

**docker-compose.yml:**

```yaml
version: '3.8'

services:
  # Redis (con persistencia para producción)
  redis-db:
    container_name: funkos-redis-prod
    image: redis:7-alpine
    restart: always
    command: redis-server --requirepass ${REDIS_PASSWORD} --appendonly yes
    ports:
      - "${REDIS_PORT}:6379"
    volumes:
      - redis-data:/data
    networks:
      - funkos-network
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 30s
      timeout: 3s
      retries: 3

volumes:
  redis-data: 

networks:
  funkos-network:
    driver: bridge
```

---

## Paso 3: Configuración de ASP. NET Core

### 3.1 Configuración para Desarrollo

**appsettings.Development. json:**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Debug",
      "Microsoft. AspNetCore": "Information"
    }
  },
  "Cache": {
    "Type": "Memory",
    "DefaultExpirationMinutes": 10
  }
}
```

### 3.2 Configuración para Producción

**appsettings.Production.json:**

```json
{
  "Logging":  {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft.AspNetCore": "Error"
    }
  },
  "Cache": {
    "Type": "Redis",
    "DefaultExpirationMinutes": 60
  },
  "Redis": {
    "Configuration": "${REDIS_HOST}:${REDIS_PORT},password=${REDIS_PASSWORD},ssl=${REDIS_SSL},abortConnect=false",
    "InstanceName": "FunkosApi:",
    "Database": 0,
    "ConnectTimeout": 3000,
    "SyncTimeout": 3000,
    "KeepAlive": 60
  },
  "CacheTTL": {
    "Categorias": 1440,
    "Usuarios": 120,
    "Productos": 60,
    "Pedidos": 30
  }
}
```

### 3.3 Configuración en Program. cs

```csharp
var builder = WebApplication.CreateBuilder(args);

// Configurar caché según entorno
if (builder.Environment.IsDevelopment())
{
    // Caché en memoria para desarrollo
    builder.Services.AddMemoryCache();
    builder.Services.AddSingleton<ICacheService, MemoryCacheService>();
}
else
{
    // Redis para producción
    var redisConnection = builder.Configuration["Redis:Configuration"];
    builder. Services.AddStackExchangeRedisCache(options =>
    {
        options.Configuration = redisConnection;
        options.InstanceName = builder.Configuration["Redis:InstanceName"];
    });
    builder.Services.AddSingleton<ICacheService, RedisCacheService>();
}

var app = builder.Build();

app.Run();
```

---

## Paso 4: Servicio de Caché

### 4.1 Interfaz ICacheService

```csharp
namespace FunkosApi.Services. Cache;

public interface ICacheService
{
    Task<T? > GetAsync<T>(string key, CancellationToken cancellationToken = default);
    Task SetAsync<T>(string key, T value, TimeSpan? expiration = null, CancellationToken cancellationToken = default);
    Task RemoveAsync(string key, CancellationToken cancellationToken = default);
    Task RemoveByPrefixAsync(string prefix, CancellationToken cancellationToken = default);
    Task<bool> ExistsAsync(string key, CancellationToken cancellationToken = default);
}
```

### 4.2 Implementación con MemoryCache (Desarrollo)

```csharp
using Microsoft.Extensions.Caching.Memory;
using System.Text.Json;

namespace FunkosApi.Services.Cache;

public class MemoryCacheService : ICacheService
{
    private readonly IMemoryCache _cache;
    private readonly ILogger<MemoryCacheService> _logger;
    private readonly TimeSpan _defaultExpiration;

    public MemoryCacheService(
        IMemoryCache cache,
        IConfiguration configuration,
        ILogger<MemoryCacheService> logger)
    {
        _cache = cache;
        _logger = logger;
        _defaultExpiration = TimeSpan.FromMinutes(
            configuration.GetValue<int>("Cache:DefaultExpirationMinutes", 10)
        );
    }

    public Task<T?> GetAsync<T>(string key, CancellationToken cancellationToken = default)
    {
        if (_cache.TryGetValue(key, out T?  value))
        {
            _logger.LogDebug("✅ Cache HIT: {Key}", key);
            return Task.FromResult(value);
        }

        _logger.LogDebug("❌ Cache MISS: {Key}", key);
        return Task.FromResult<T?>(default);
    }

    public Task SetAsync<T>(string key, T value, TimeSpan? expiration = null, CancellationToken cancellationToken = default)
    {
        var options = new MemoryCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = expiration ?? _defaultExpiration
        };

        _cache.Set(key, value, options);
        _logger.LogDebug("💾 Cache SET: {Key} (TTL: {Expiration})", key, expiration ??  _defaultExpiration);

        return Task.CompletedTask;
    }

    public Task RemoveAsync(string key, CancellationToken cancellationToken = default)
    {
        _cache.Remove(key);
        _logger.LogDebug("🗑️ Cache REMOVE: {Key}", key);
        return Task.CompletedTask;
    }

    public Task RemoveByPrefixAsync(string prefix, CancellationToken cancellationToken = default)
    {
        // MemoryCache no soporta eliminación por prefijo nativamente
        _logger.LogWarning("⚠️ RemoveByPrefix no soportado en MemoryCache");
        return Task.CompletedTask;
    }

    public Task<bool> ExistsAsync(string key, CancellationToken cancellationToken = default)
    {
        return Task.FromResult(_cache.TryGetValue(key, out _));
    }
}
```

### 4.3 Implementación con Redis (Producción)

```csharp
using Microsoft.Extensions. Caching. Distributed;
using System.Text.Json;

namespace FunkosApi.Services. Cache;

public class RedisCacheService : ICacheService
{
    private readonly IDistributedCache _cache;
    private readonly ILogger<RedisCacheService> _logger;
    private readonly TimeSpan _defaultExpiration;

    public RedisCacheService(
        IDistributedCache cache,
        IConfiguration configuration,
        ILogger<RedisCacheService> logger)
    {
        _cache = cache;
        _logger = logger;
        _defaultExpiration = TimeSpan. FromMinutes(
            configuration. GetValue<int>("Cache:DefaultExpirationMinutes", 60)
        );
    }

    public async Task<T?> GetAsync<T>(string key, CancellationToken cancellationToken = default)
    {
        var data = await _cache.GetStringAsync(key, cancellationToken);

        if (data is null)
        {
            _logger.LogDebug("❌ Redis MISS: {Key}", key);
            return default;
        }

        _logger.LogDebug("✅ Redis HIT:  {Key}", key);
        return JsonSerializer.Deserialize<T>(data);
    }

    public async Task SetAsync<T>(string key, T value, TimeSpan? expiration = null, CancellationToken cancellationToken = default)
    {
        var options = new DistributedCacheEntryOptions
        {
            AbsoluteExpirationRelativeToNow = expiration ?? _defaultExpiration
        };

        var data = JsonSerializer.Serialize(value);
        await _cache.SetStringAsync(key, data, options, cancellationToken);

        _logger.LogDebug("💾 Redis SET: {Key} (TTL: {Expiration})", key, expiration ??  _defaultExpiration);
    }

    public async Task RemoveAsync(string key, CancellationToken cancellationToken = default)
    {
        await _cache.RemoveAsync(key, cancellationToken);
        _logger.LogDebug("🗑️ Redis REMOVE:  {Key}", key);
    }

    public Task RemoveByPrefixAsync(string prefix, CancellationToken cancellationToken = default)
    {
        // Requiere acceso directo a StackExchange.Redis
        _logger.LogWarning("⚠️ RemoveByPrefix requiere implementación específica con StackExchange.Redis");
        return Task.CompletedTask;
    }

    public async Task<bool> ExistsAsync(string key, CancellationToken cancellationToken = default)
    {
        var data = await _cache.GetStringAsync(key, cancellationToken);
        return data is not null;
    }
}
```

---

## Paso 5: Uso en Servicios

### 5.1 Ejemplo con Funkos

```csharp
namespace FunkosApi.Services;

public class FunkoService :  IFunkoService
{
    private readonly IFunkoRepository _repository;
    private readonly ICacheService _cache;
    private readonly ILogger<FunkoService> _logger;
    private readonly IConfiguration _configuration;

    private string CacheKeyAll => "funkos:all";
    private string CacheKeyById(int id) => $"funkos:{id}";
    private TimeSpan CacheTTL => TimeSpan.FromMinutes(_configuration.GetValue<int>("CacheTTL:Productos", 60));

    public FunkoService(
        IFunkoRepository repository,
        ICacheService cache,
        ILogger<FunkoService> logger,
        IConfiguration configuration)
    {
        _repository = repository;
        _cache = cache;
        _logger = logger;
        _configuration = configuration;
    }

    public async Task<IEnumerable<Funko>> GetAllAsync()
    {
        // Intentar obtener de caché
        var cached = await _cache.GetAsync<IEnumerable<Funko>>(CacheKeyAll);
        if (cached is not null)
        {
            _logger. LogInformation("✅ Funkos obtenidos de caché");
            return cached;
        }

        // Si no está en caché, obtener de BD
        _logger.LogInformation("🔍 Buscando funkos en base de datos (NO en caché)");
        var funkos = await _repository.GetAllAsync();

        // Guardar en caché
        await _cache.SetAsync(CacheKeyAll, funkos, CacheTTL);

        return funkos;
    }

    public async Task<Funko? > GetByIdAsync(int id)
    {
        var cacheKey = CacheKeyById(id);

        // Intentar obtener de caché
        var cached = await _cache.GetAsync<Funko>(cacheKey);
        if (cached is not null)
        {
            _logger.LogInformation("✅ Funko {Id} obtenido de caché", id);
            return cached;
        }

        // Si no está en caché, obtener de BD
        _logger.LogInformation("🔍 Buscando funko {Id} en base de datos (NO en caché)", id);
        var funko = await _repository.GetByIdAsync(id);

        if (funko is not null)
        {
            // Guardar en caché
            await _cache.SetAsync(cacheKey, funko, CacheTTL);
        }

        return funko;
    }

    public async Task<Funko> CreateAsync(CreateFunkoDto dto)
    {
        var funko = await _repository.CreateAsync(dto);

        // Invalidar caché de todos los funkos
        await _cache.RemoveAsync(CacheKeyAll);

        _logger.LogInformation("🆕 Funko creado, caché invalidada");

        return funko;
    }

    public async Task<Funko> UpdateAsync(int id, UpdateFunkoDto dto)
    {
        var funko = await _repository.UpdateAsync(id, dto);

        // Invalidar caché específica y general
        await _cache.RemoveAsync(CacheKeyById(id));
        await _cache.RemoveAsync(CacheKeyAll);

        _logger.LogInformation("✏️ Funko {Id} actualizado, caché invalidada", id);

        return funko;
    }

    public async Task DeleteAsync(int id)
    {
        await _repository.DeleteAsync(id);

        // Invalidar caché específica y general
        await _cache.RemoveAsync(CacheKeyById(id));
        await _cache. RemoveAsync(CacheKeyAll);

        _logger.LogInformation("🗑️ Funko {Id} eliminado, caché invalidada", id);
    }
}
```

### 5.2 Ejemplo con Categorías

```csharp
public class CategoriaService : ICategoriaService
{
    private readonly ICategoriaRepository _repository;
    private readonly ICacheService _cache;
    private readonly IConfiguration _configuration;

    private string CacheKeyAll => "categorias: all";
    private string CacheKeyById(int id) => $"categorias:{id}";
    private TimeSpan CacheTTL => TimeSpan.FromHours(_configuration.GetValue<int>("CacheTTL: Categorias", 24));

    // ...  implementación similar a FunkoService
}
```

---

## Paso 6: Decorador de Caché (Patrón Decorator)

### 6.1 Servicio Base

```csharp
public interface IFunkoService
{
    Task<IEnumerable<Funko>> GetAllAsync();
    Task<Funko?> GetByIdAsync(int id);
    Task<Funko> CreateAsync(CreateFunkoDto dto);
    Task<Funko> UpdateAsync(int id, UpdateFunkoDto dto);
    Task DeleteAsync(int id);
}

public class FunkoService : IFunkoService
{
    private readonly IFunkoRepository _repository;

    // Implementación SIN caché
}
```

### 6.2 Decorador con Caché

```csharp
public class CachedFunkoService : IFunkoService
{
    private readonly IFunkoService _innerService;
    private readonly ICacheService _cache;
    private readonly ILogger<CachedFunkoService> _logger;

    private string CacheKeyAll => "funkos:all";
    private string CacheKeyById(int id) => $"funkos:{id}";
    private static readonly TimeSpan CacheTTL = TimeSpan.FromMinutes(60);

    public CachedFunkoService(
        IFunkoService innerService,
        ICacheService cache,
        ILogger<CachedFunkoService> logger)
    {
        _innerService = innerService;
        _cache = cache;
        _logger = logger;
    }

    public async Task<IEnumerable<Funko>> GetAllAsync()
    {
        var cached = await _cache.GetAsync<IEnumerable<Funko>>(CacheKeyAll);
        if (cached is not null) return cached;

        var result = await _innerService.GetAllAsync();
        await _cache.SetAsync(CacheKeyAll, result, CacheTTL);
        return result;
    }

    public async Task<Funko?> GetByIdAsync(int id)
    {
        var cacheKey = CacheKeyById(id);
        var cached = await _cache. GetAsync<Funko>(cacheKey);
        if (cached is not null) return cached;

        var result = await _innerService.GetByIdAsync(id);
        if (result is not null)
        {
            await _cache.SetAsync(cacheKey, result, CacheTTL);
        }
        return result;
    }

    public async Task<Funko> CreateAsync(CreateFunkoDto dto)
    {
        var result = await _innerService.CreateAsync(dto);
        await _cache.RemoveAsync(CacheKeyAll);
        return result;
    }

    public async Task<Funko> UpdateAsync(int id, UpdateFunkoDto dto)
    {
        var result = await _innerService.UpdateAsync(id, dto);
        await _cache.RemoveAsync(CacheKeyById(id));
        await _cache.RemoveAsync(CacheKeyAll);
        return result;
    }

    public async Task DeleteAsync(int id)
    {
        await _innerService.DeleteAsync(id);
        await _cache. RemoveAsync(CacheKeyById(id));
        await _cache.RemoveAsync(CacheKeyAll);
    }
}
```

### 6.3 Registro en Program.cs

```csharp
// Servicio base
builder.Services.AddScoped<FunkoService>();

// Decorador con caché
builder.Services.AddScoped<IFunkoService>(provider =>
{
    var baseService = provider.GetRequiredService<FunkoService>();
    var cache = provider.GetRequiredService<ICacheService>();
    var logger = provider.GetRequiredService<ILogger<CachedFunkoService>>();
    return new CachedFunkoService(baseService, cache, logger);
});
```

---

## Paso 7: Monitoreo y Logs

### 7.1 Configuración de Logs

**appsettings.json:**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Information",
      "Microsoft.AspNetCore": "Warning",
      "FunkosApi. Services.Cache": "Debug"
    }
  }
}
```

### 7.2 Middleware de Logging

```csharp
public class CacheLoggingMiddleware
{
    private readonly RequestDelegate _next;
    private readonly ILogger<CacheLoggingMiddleware> _logger;

    public CacheLoggingMiddleware(RequestDelegate next, ILogger<CacheLoggingMiddleware> logger)
    {
        _next = next;
        _logger = logger;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        var sw = Stopwatch.StartNew();
        await _next(context);
        sw.Stop();

        _logger.LogInformation(
            "Request: {Method} {Path} - Status: {StatusCode} - Time: {ElapsedMs}ms",
            context.Request.Method,
            context.Request.Path,
            context.Response.StatusCode,
            sw.ElapsedMilliseconds
        );
    }
}

// Registrar en Program.cs
app.UseMiddleware<CacheLoggingMiddleware>();
```

---

## Paso 8: Testing

### 8.1 Test de Servicio de Caché

```csharp
[TestFixture]
public class MemoryCacheServiceTests
{
    private MemoryCacheService _cacheService = null!;

    [SetUp]
    public void Setup()
    {
        var cache = new MemoryCache(new MemoryCacheOptions());
        var configuration = new ConfigurationBuilder()
            .AddInMemoryCollection(new Dictionary<string, string>
            {
                { "Cache:DefaultExpirationMinutes", "10" }
            }!)
            .Build();
        var logger = new Mock<ILogger<MemoryCacheService>>().Object;

        _cacheService = new MemoryCacheService(cache, configuration, logger);
    }

    [Test]
    public async Task GetAsync_WhenKeyDoesNotExist_ReturnsNull()
    {
        // Act
        var result = await _cacheService.GetAsync<string>("non-existent-key");

        // Assert
        result.Should().BeNull();
    }

    [Test]
    public async Task SetAsync_AndGetAsync_ReturnsValue()
    {
        // Arrange
        var key = "test-key";
        var value = "test-value";

        // Act
        await _cacheService.SetAsync(key, value);
        var result = await _cacheService.GetAsync<string>(key);

        // Assert
        result.Should().Be(value);
    }

    [Test]
    public async Task RemoveAsync_RemovesKey()
    {
        // Arrange
        var key = "test-key";
        await _cacheService.SetAsync(key, "value");

        // Act
        await _cacheService.RemoveAsync(key);
        var result = await _cacheService.GetAsync<string>(key);

        // Assert
        result.Should().BeNull();
    }
}
```

### 8.2 Test con Mock de Redis

```csharp
[TestFixture]
public class FunkoServiceCacheTests
{
    private Mock<IFunkoRepository> _repositoryMock = null!;
    private Mock<ICacheService> _cacheMock = null!;
    private FunkoService _service = null!;

    [SetUp]
    public void Setup()
    {
        _repositoryMock = new Mock<IFunkoRepository>();
        _cacheMock = new Mock<ICacheService>();
        var logger = new Mock<ILogger<FunkoService>>().Object;
        var configuration = new Mock<IConfiguration>().Object;

        _service = new FunkoService(_repositoryMock.Object, _cacheMock.Object, logger, configuration);
    }

    [Test]
    public async Task GetByIdAsync_WhenCached_ReturnsFromCache()
    {
        // Arrange
        var funko = new Funko { Id = 1, Nombre = "Iron Man" };
        _cacheMock
            .Setup(c => c. GetAsync<Funko>("funkos:1", default))
            .ReturnsAsync(funko);

        // Act
        var result = await _service.GetByIdAsync(1);

        // Assert
        result. Should().Be(funko);
        _repositoryMock.Verify(r => r.GetByIdAsync(1), Times.Never);
    }

    [Test]
    public async Task GetByIdAsync_WhenNotCached_ReturnsFromDatabase()
    {
        // Arrange
        var funko = new Funko { Id = 1, Nombre = "Iron Man" };
        _cacheMock
            .Setup(c => c.GetAsync<Funko>("funkos:1", default))
            .ReturnsAsync((Funko?)null);
        _repositoryMock
            .Setup(r => r. GetByIdAsync(1))
            .ReturnsAsync(funko);

        // Act
        var result = await _service.GetByIdAsync(1);

        // Assert
        result.Should().Be(funko);
        _cacheMock.Verify(c => c.SetAsync("funkos:1", funko, It.IsAny<TimeSpan>(), default), Times.Once);
    }
}
```

---

## Paso 9: Ejecución y Pruebas

### 9.1 Levantar el entorno

```bash
# Desarrollo
docker-compose -f docker-compose.dev.yml up -d

# Producción
docker-compose up -d
```

### 9.2 Probar la aplicación

```bash
# Desarrollo con caché en memoria
dotnet run --environment Development

# Producción con Redis
dotnet run --environment Production
```

### 9.3 Verificar funcionamiento

1. **Primera consulta GET /api/funkos/1**:  
   - Log: "🔍 Buscando funko 1 en base de datos (NO en caché)"
   - Tiempo: ~100ms

2. **Segunda consulta GET /api/funkos/1**:
   - Log: "✅ Funko 1 obtenido de caché"
   - Tiempo: ~1ms

3. **Acceder a Redis Commander**: `http://localhost:8082`
   - Ver claves: `FunkosApi:funkos:1`
   - Ver TTL y datos almacenados

---

## Paso 10: Estrategias Avanzadas

### 10.1 Cache-Aside Pattern

```csharp
public async Task<Funko? > GetByIdAsync(int id)
{
    // 1. Intentar caché
    var cached = await _cache.GetAsync<Funko>($"funkos:{id}");
    if (cached is not null) return cached;

    // 2. Si no, ir a BD
    var funko = await _repository.GetByIdAsync(id);

    // 3. Guardar en caché
    if (funko is not null)
    {
        await _cache.SetAsync($"funkos:{id}", funko, TimeSpan.FromMinutes(60));
    }

    return funko;
}
```

### 10.2 Write-Through Pattern

```csharp
public async Task<Funko> UpdateAsync(int id, UpdateFunkoDto dto)
{
    // 1. Actualizar BD
    var funko = await _repository.UpdateAsync(id, dto);

    // 2. Actualizar caché inmediatamente
    await _cache. SetAsync($"funkos:{id}", funko, TimeSpan.FromMinutes(60));

    return funko;
}
```

### 10.3 Invalidación por Eventos

```csharp
public class FunkoService
{
    private readonly IEventPublisher _eventPublisher;

    public async Task DeleteAsync(int id)
    {
        await _repository.DeleteAsync(id);

        // Publicar evento para invalidar caché distribuida
        await _eventPublisher. PublishAsync(new FunkoDeletedEvent(id));
    }
}

public class CacheInvalidationHandler : IEventHandler<FunkoDeletedEvent>
{
    private readonly ICacheService _cache;

    public async Task HandleAsync(FunkoDeletedEvent @event)
    {
        await _cache.RemoveAsync($"funkos:{@event.Id}");
        await _cache.RemoveAsync("funkos:all");
    }
}
```

---

## Resumen de Beneficios

### ✅ Lo que conseguimos

- **🔄 Fácil migración**:  Solo cambios en configuración
- **🏃‍♂️ Mayor rendimiento**: Datos en memoria ultra-rápidos
- **📈 Escalabilidad**: Redis compartido entre múltiples instancias
- **⏰ Control de TTL**: Datos expiran automáticamente
- **🔧 Flexibilidad**: Diferentes configuraciones por entorno
- **📊 Monitoreo**:  Herramientas visuales para gestionar caché

### ⚡ Mejoras de rendimiento esperadas

- **Consultas de BD**:  De ~100ms a ~1ms
- **APIs REST**: Respuesta 10-100x más rápida para datos cacheados
- **Carga del servidor**: Reducción significativa de consultas a BD

### 🎯 Casos de uso ideales para Redis

- **Datos que se leen mucho y cambian poco**:  Categorías, configuraciones
- **Sesiones de usuario**: Mantener estado entre requests
- **Resultados de consultas complejas**: Reportes, estadísticas
- **APIs externas**: Cachear respuestas de servicios externos

---

## Buenas Prácticas

✅ **Usar TTL apropiados**:  Según frecuencia de cambio de datos

✅ **Invalidar caché**: Al crear/actualizar/eliminar

✅ **Logging detallado**: Para troubleshooting

✅ **Keys descriptivas**: `entity:operation:id`

✅ **Serialización eficiente**: JSON compacto

✅ **Manejo de errores**: No fallar si Redis no está disponible

✅ **Monitoreo**: Métricas de hit/miss ratio

✅ **Documentar**: TTL y estrategias de invalidación

---

## Ejercicio Propuesto:  Redis para Funkos

### Requisitos

1. ✅ Implementar caché con Redis para el servicio de Funkos
2. ✅ Configurar TTL de 60 minutos para productos
3. ✅ Usar caché en memoria para desarrollo
4. ✅ Usar Redis para producción
5. ✅ Implementar logging detallado (cache hit/miss)
6. ✅ Invalidar caché al crear/actualizar/eliminar
7. ✅ Crear tests para verificar funcionamiento
8. ✅ Documentar estrategia de caché en README

**Criterios de evaluación:**

- ✅ Caché funciona correctamente
- ✅ TTL configurado apropiadamente
- ✅ Logs muestran hit/miss
- ✅ Invalidación funciona
- ✅ Tests completos
- ✅ Documentación clara

---

## Conclusión

Con esta implementación tienes: 

- **Desarrollo**:  Caché simple para pruebas rápidas
- **Producción**: Redis robusto y escalable
- **Código**:  Limpio y mantenible con patrón Decorator
- **Monitoring**: Logs detallados para troubleshooting
- **Performance**: Mejora significativa en velocidad de respuesta

La migración de caché en memoria a Redis es transparente para tu aplicación, pero ofrece beneficios significativos en rendimiento, escalabilidad y funcionalidades avanzadas.

---
