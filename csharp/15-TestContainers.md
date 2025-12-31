# Testcontainers en .  NET

---

- [Testcontainers en .  NET](#testcontainers-en---net)
  - [¿Qué es Testcontainers?](#qué-es-testcontainers)
  - [¿Para qué se usan los Testcontainers?](#para-qué-se-usan-los-testcontainers)
  - [¿Por qué son recomendables?](#por-qué-son-recomendables)
  - [Instalación y configuración](#instalación-y-configuración)
  - [Ejemplo básico:     PostgreSQL](#ejemplo-básico-----postgresql)
  - [Ejemplo avanzado:     Múltiples contenedores](#ejemplo-avanzado-----múltiples-contenedores)
  - [Testcontainers en Docker multi-etapa](#testcontainers-en-docker-multi-etapa)
    - [Windows con Docker Desktop](#windows-con-docker-desktop)
    - [Linux](#linux)
    - [Dockerfile con Testcontainers](#dockerfile-con-testcontainers)
  - [Mejores prácticas](#mejores-prácticas)

---

## ¿Qué es Testcontainers?

**Testcontainers** es una biblioteca que proporciona contenedores Docker ligeros y desechables para pruebas automatizadas.  Permite ejecutar tests de integración con servicios reales (bases de datos, colas de mensajes, APIs externas, etc.) en un entorno controlado y reproducible.

**Beneficios clave:**

✅ **Pruebas realistas**: Usa servicios reales en lugar de mocks.   

✅ **Aislamiento completo**: Cada test tiene su propia instancia limpia.  

✅ **Reproducibilidad**: Los tests funcionan igual en todos los entornos.  

✅ **Integración con CI/CD**: Compatible con cualquier pipeline que soporte Docker.

---

## ¿Para qué se usan los Testcontainers?

1. **Pruebas de integración con bases de datos reales**
2. **Pruebas con servicios externos** (Redis, Kafka, MongoDB, Elasticsearch)
3. **Pruebas de APIs** con servidores HTTP reales
4. **Pruebas end-to-end** con navegadores (Selenium)
5. **Entornos de prueba consistentes** sin instalaciones manuales

---

## ¿Por qué son recomendables? 

| **Aspecto**              | **Sin Testcontainers**                     | **Con Testcontainers**                     |
|: -------------------------|:-------------------------------------------|:-------------------------------------------|
| **Configuración**        | Manual, compleja                           | Automática, declarativa                    |
| **Aislamiento**          | Compartido, puede haber conflictos         | Completo, cada test es independiente       |
| **Reproducibilidad**     | Varía según el entorno                     | Idéntica en todos los entornos             |
| **Limpieza**             | Manual                                     | Automática al finalizar el test            |
| **CI/CD**                | Requiere configuración especial            | Funciona directamente con Docker           |

---

## Instalación y configuración

**Paquetes NuGet:**

```bash
dotnet add package Testcontainers
dotnet add package Testcontainers.  PostgreSql
dotnet add package Testcontainers.   MsSql
dotnet add package Testcontainers.MongoDb
dotnet add package Testcontainers.Redis
```

**Requisito:**  Docker debe estar ejecutándose en la máquina donde se ejecutan los tests.

---

## Ejemplo básico:     PostgreSQL

```csharp
using NUnit.Framework;
using Testcontainers.PostgreSql;
using Microsoft.EntityFrameworkCore;
using FluentAssertions;

namespace MiAplicacion.Tests.Integration;

[TestFixture]
public class ProductoRepositoryIntegrationTests
{
    private PostgreSqlContainer _postgresContainer = null!;
    private ApplicationDbContext _context = null!;
    private ProductoRepository _repository = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        // Configurar y arrancar el contenedor de PostgreSQL
        _postgresContainer = new PostgreSqlBuilder()
            .WithImage("postgres:16-alpine")
            .WithDatabase("testdb")
            .WithUsername("testuser")
            .WithPassword("testpass")
            .WithCleanUp(true) // Limpiar automáticamente
            .Build();

        await _postgresContainer.StartAsync();

        Console.WriteLine($"✅ PostgreSQL iniciado en:  {_postgresContainer.GetConnectionString()}");
    }

    [SetUp]
    public async Task SetUp()
    {
        // Crear DbContext con la conexión al contenedor
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgresContainer.  GetConnectionString())
            .Options;

        _context = new ApplicationDbContext(options);

        // Aplicar migraciones
        await _context.Database.EnsureCreatedAsync();

        // Inicializar repositorio
        _repository = new ProductoRepository(_context);
    }

    [TearDown]
    public async Task TearDown()
    {
        // Limpiar base de datos después de cada test
        await _context.Database.  EnsureDeletedAsync();
        await _context. DisposeAsync();
    }

    [OneTimeTearDown]
    public async Task OneTimeTearDown()
    {
        // Detener y eliminar el contenedor
        await _postgresContainer.StopAsync();
        await _postgresContainer.DisposeAsync();
    }

    [Test]
    public async Task AddAsync_NuevoProducto_DebeGuardarEnBaseDeDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Laptop Test",
            Precio = 1200m,
            Cantidad = 10,
            Categoria = "Electrónica"
        };

        // Act
        await _repository.AddAsync(producto);

        // Assert
        var productoGuardado = await _repository.GetByIdAsync(producto. Id);
        
        productoGuardado.Should().NotBeNull();
        productoGuardado!  .Id.Should().BeGreaterThan(0);
        productoGuardado. Nombre.Should().Be("Laptop Test");
        productoGuardado.Precio.Should().Be(1200m);
    }

    [Test]
    public async Task GetAllAsync_DebeRetornarTodosLosProductos()
    {
        // Arrange
        var productos = new[]
        {
            new Producto { Nombre = "Laptop", Precio = 1200m, Cantidad = 5, Categoria = "Electrónica" },
            new Producto { Nombre = "Mouse", Precio = 25m, Cantidad = 50, Categoria = "Accesorios" }
        };

        foreach (var p in productos)
        {
            await _repository.AddAsync(p);
        }

        // Act
        var resultado = await _repository.GetAllAsync();

        // Assert
        resultado.Should().HaveCount(2);
        resultado.Should().Contain(p => p.Nombre == "Laptop");
        resultado.Should(). Contain(p => p. Nombre == "Mouse");
    }

    [Test]
    public async Task UpdateAsync_ProductoExistente_DebeActualizarDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Laptop Original",
            Precio = 1200m,
            Cantidad = 10,
            Categoria = "Electrónica"
        };

        await _repository.AddAsync(producto);

        // Act
        producto.Nombre = "Laptop Actualizada";
        producto.Precio = 1100m;
        await _repository.UpdateAsync(producto);

        // Assert
        var productoActualizado = await _repository.GetByIdAsync(producto. Id);
        
        productoActualizado! .Nombre.Should().Be("Laptop Actualizada");
        productoActualizado.Precio.Should().Be(1100m);
    }

    [Test]
    public async Task DeleteAsync_ProductoExistente_DebeEliminarDeLaBaseDeDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Producto a Eliminar",
            Precio = 50m,
            Cantidad = 1,
            Categoria = "Test"
        };

        await _repository.AddAsync(producto);
        var idProducto = producto.Id;

        // Act
        await _repository.  DeleteAsync(idProducto);

        // Assert
        var productoEliminado = await _repository.  GetByIdAsync(idProducto);
        productoEliminado.Should().BeNull();
    }
}
```

---

## Ejemplo avanzado:     Múltiples contenedores

```csharp
using NUnit.Framework;
using Testcontainers.PostgreSql;
using Testcontainers.MongoDb;
using Testcontainers.Redis;
using FluentAssertions;

[TestFixture]
public class SistemaCompletoIntegrationTests
{
    private PostgreSqlContainer _postgresContainer = null!;
    private MongoDbContainer _mongoContainer = null!;
    private RedisContainer _redisContainer = null!;

    private ApplicationDbContext _dbContext = null!;
    private IMongoDatabase _mongoDatabase = null!;
    private IConnectionMultiplexer _redis = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        // Inicializar contenedores
        _postgresContainer = new PostgreSqlBuilder()
            .WithImage("postgres:16-alpine")
            .WithDatabase("testdb")
            .WithUsername("testuser")
            .WithPassword("testpass")
            .Build();

        _mongoContainer = new MongoDbBuilder()
            .WithImage("mongo:7")
            .Build();

        _redisContainer = new RedisBuilder()
            .WithImage("redis:7-alpine")
            .Build();

        // Arrancar todos en paralelo
        await Task.WhenAll(
            _postgresContainer.StartAsync(),
            _mongoContainer. StartAsync(),
            _redisContainer.StartAsync()
        );

        Console.WriteLine("✅ Todos los contenedores iniciados");
        Console.WriteLine($"  PostgreSQL: {_postgresContainer.GetConnectionString()}");
        Console.WriteLine($"  MongoDB:  {_mongoContainer.GetConnectionString()}");
        Console.WriteLine($"  Redis: {_redisContainer.  GetConnectionString()}");
    }

    [SetUp]
    public async Task SetUp()
    {
        // Configurar PostgreSQL
        var postgresOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgresContainer. GetConnectionString())
            .Options;

        _dbContext = new ApplicationDbContext(postgresOptions);
        await _dbContext.Database. EnsureCreatedAsync();

        // Configurar MongoDB
        var mongoClient = new MongoClient(_mongoContainer.GetConnectionString());
        _mongoDatabase = mongoClient.GetDatabase("testdb");

        // Configurar Redis
        _redis = await ConnectionMultiplexer.ConnectAsync(_redisContainer.  GetConnectionString());
    }

    [TearDown]
    public async Task TearDown()
    {
        await _dbContext.Database.EnsureDeletedAsync();
        await _dbContext.  DisposeAsync();

        _mongoDatabase. Client. DropDatabase("testdb");

        var redisDb = _redis.GetDatabase();
        await redisDb.ExecuteAsync("FLUSHDB");
    }

    [OneTimeTearDown]
    public async Task OneTimeTearDown()
    {
        await Task.  WhenAll(
            _postgresContainer.StopAsync(),
            _mongoContainer.  StopAsync(),
            _redisContainer. StopAsync()
        );

        await Task.WhenAll(
            _postgresContainer.DisposeAsync().AsTask(),
            _mongoContainer.DisposeAsync().AsTask(),
            _redisContainer. DisposeAsync().AsTask()
        );

        Console.WriteLine("✅ Todos los contenedores detenidos");
    }

    [Test]
    public async Task SistemaCompleto_DebeIntegrarTodosLosServicios()
    {
        // Arrange - Guardar en PostgreSQL
        var productoSql = new Producto
        {
            Nombre = "Producto SQL",
            Precio = 100m,
            Cantidad = 10,
            Categoria = "Test"
        };

        _dbContext. Productos.Add(productoSql);
        await _dbContext. SaveChangesAsync();

        // Arrange - Guardar en MongoDB
        var productosMongo = _mongoDatabase.GetCollection<ProductoMongo>("productos");
        var productoMongo = new ProductoMongo
        {
            Nombre = "Producto Mongo",
            Precio = 200m
        };

        await productosMongo.InsertOneAsync(productoMongo);

        // Arrange - Guardar en Redis
        var redisDb = _redis.GetDatabase();
        await redisDb.StringSetAsync("producto:cache", "Valor en cache");

        // Act & Assert - PostgreSQL
        var productosSql = await _dbContext. Productos.ToListAsync();
        productosSql.Should().HaveCount(1);
        productosSql.First().Nombre.Should().Be("Producto SQL");

        // Act & Assert - MongoDB
        var productosMongoDocs = await productosMongo.Find(_ => true).ToListAsync();
        productosMongoDocs. Should().HaveCount(1);
        productosMongoDocs.First().Nombre.Should().Be("Producto Mongo");

        // Act & Assert - Redis
        var valorCache = await redisDb.StringGetAsync("producto:cache");
        valorCache.ToString().Should().Be("Valor en cache");
    }
}
```

---

## Testcontainers en Docker multi-etapa

Para usar Testcontainers dentro de un contenedor Docker (Docker-in-Docker), necesitas configurar el acceso al daemon de Docker del host. 

**Configuración según el sistema operativo:**

### Windows con Docker Desktop

1.  Habilitar exposición del daemon: 
   - Abrir Docker Desktop
   - Settings → General
   - ✅ Activar "Expose daemon on tcp://localhost:2375 without TLS"

2. Usar `host.docker.internal` en el Dockerfile

### Linux

Configurar el daemon de Docker:

```bash
# Editar /etc/docker/daemon.json
{
  "hosts": ["unix:///var/run/docker.sock", "tcp://0.0.0.0:2375"]
}

# Reiniciar Docker
sudo systemctl restart docker
```

### Dockerfile con Testcontainers

```dockerfile
# ============================================
# Etapa 1: BUILD y TEST
# ============================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /src

# Configurar DOCKER_HOST para Testcontainers
# En Windows: host.docker.internal
# En Linux: localhost o 172.17.0.1 (gateway de Docker)
ARG DOCKER_HOST_ARG=tcp://host.docker.internal:2375
ENV DOCKER_HOST=$DOCKER_HOST_ARG

# Copiar archivos de proyecto
COPY ["MiAplicacion. Api/MiAplicacion.Api.csproj", "MiAplicacion.Api/"]
COPY ["MiAplicacion.Tests/MiAplicacion.Tests.csproj", "MiAplicacion.Tests/"]

# Restaurar dependencias
RUN dotnet restore "MiAplicacion. Api/MiAplicacion.  Api.csproj"
RUN dotnet restore "MiAplicacion.Tests/MiAplicacion.Tests.csproj"

# Copiar código fuente
COPY . . 

# Compilar
WORKDIR "/src/MiAplicacion.Api"
RUN dotnet build "MiAplicacion.Api.csproj" -c Release -o /app/build

# ============================================
# Etapa 2: TEST (con Testcontainers)
# ============================================
FROM build AS test

WORKDIR /src/MiAplicacion.  Tests

# Ejecutar tests de integración (usa Testcontainers)
RUN dotnet test "MiAplicacion.  Tests.csproj" \
    --configuration Release \
    --logger "trx;LogFileName=test-results.trx" \
    --no-build \
    --verbosity normal

# ============================================
# Etapa 3: PUBLISH
# ============================================
FROM build AS publish
WORKDIR "/src/MiAplicacion. Api"
RUN dotnet publish "MiAplicacion.Api.csproj" \
    -c Release \
    -o /app/publish \
    /p:UseAppHost=false

# ============================================
# Etapa 4: RUNTIME
# ============================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final

WORKDIR /app
COPY --from=publish /app/publish . 

EXPOSE 8080
ENV ASPNETCORE_URLS=http://+:8080

ENTRYPOINT ["dotnet", "MiAplicacion.Api.dll"]
```

**Construir la imagen:**

```bash
# Windows
docker build --build-arg DOCKER_HOST_ARG=tcp://host.docker.internal:2375 -t mi-app: 1.0 .

# Linux
docker build --build-arg DOCKER_HOST_ARG=tcp://172.17.0.1:2375 -t mi-app: 1.0 .
```

---

## Mejores prácticas

✅ **1. Usar imágenes Alpine para tests más rápidos**

```csharp
_postgresContainer = new PostgreSqlBuilder()
    .WithImage("postgres:16-alpine") // Más ligera
    .Build();
```

✅ **2. Reutilizar contenedores entre tests con `[OneTimeSetUp]`**

```csharp
[OneTimeSetUp] // Una sola vez para todos los tests
public async Task OneTimeSetUp()
{
    await _container.StartAsync();
}
```

✅ **3. Limpiar datos entre tests, no reiniciar contenedores**

```csharp
[TearDown]
public async Task TearDown()
{
    await _context.Database.EnsureDeletedAsync(); // Limpiar BD
    await _context.Database.EnsureCreatedAsync(); // Recrear
}
```

✅ **4. Usar scripts de inicialización para datos de prueba**

```csharp
_postgresContainer = new PostgreSqlBuilder()
    .WithImage("postgres:16-alpine")
    .WithResourceMapping("./init-scripts/schema.sql", "/docker-entrypoint-initdb.d/schema.  sql")
    .Build();
```

✅ **5. Configurar timeouts apropiados**

```csharp
_postgresContainer = new PostgreSqlBuilder()
    .WithImage("postgres:16-alpine")
    .WithWaitStrategy(Wait.ForUnixContainer().UntilPortIsAvailable(5432))
    .WithStartupCallback(async (container, ct) =>
    {
        Console.WriteLine("✅ PostgreSQL listo");
    })
    .Build();
```

✅ **6. Usar parallelization con cuidado**

```csharp
// En NUnit
[assembly: LevelOfParallelism(2)] // Limitar paralelismo

// O deshabilitar
[assembly:  Parallelizable(ParallelScope.None)]
```

✅ **7. Documentar requisitos de Docker**

```markdown
## Requisitos para ejecutar tests

- Docker Desktop (Windows/Mac) o Docker Engine (Linux)
- Puerto 2375 expuesto (para Testcontainers)
- Al menos 4GB de RAM disponible para Docker
```

---

