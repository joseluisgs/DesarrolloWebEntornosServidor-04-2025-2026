# Bases de Datos Relacionales en .NET

---

- [Bases de Datos Relacionales en .NET](#bases-de-datos-relacionales-en-net)
  - [Acceso a bases de datos en .NET](#acceso-a-bases-de-datos-en-net)
  - [Entity Framework Core: ORM moderno](#entity-framework-core-orm-moderno)
  - [Patrón Repository y Unit of Work](#patrón-repository-y-unit-of-work)
  - [CRUD con Entity Framework Core](#crud-con-entity-framework-core)
  - [Consultas con LINQ y SQL raw](#consultas-con-linq-y-sql-raw)
  - [Migraciones y gestión de esquema](#migraciones-y-gestión-de-esquema)

---

## Acceso a bases de datos en .NET

El acceso a bases de datos es fundamental para gestionar información de forma segura y eficiente. En .NET existen varias opciones: 

1. **ADO.NET**:  API de bajo nivel (equivalente a JDBC en Java)
2. **Dapper**: Micro-ORM ligero y de alto rendimiento
3. **Entity Framework Core**: ORM completo y recomendado

**Ejemplo con ADO.NET (bajo nivel, similar a JDBC):**

```csharp
using System.Data;
using Npgsql; // Driver de PostgreSQL

public class AdoNetExample
{
    private readonly string _connectionString = 
        "Host=localhost;Database=testdb;Username=postgres;Password=password";

    public void EjemploBasico()
    {
        // Establecer conexión (similar a DriverManager.getConnection)
        using var connection = new NpgsqlConnection(_connectionString);
        connection.Open();

        // Crear comando SQL
        using var command = connection.CreateCommand();
        command.CommandText = "SELECT * FROM customers";

        // Ejecutar consulta
        using var reader = command.ExecuteReader();

        // Recorrer resultados
        while (reader.Read())
        {
            string nombre = reader.GetString("name");
            int edad = reader.GetInt32("age");
            Console.WriteLine($"Nombre: {nombre}, Edad: {edad}");
        }
    }

    // Ejemplo con parámetros (equivalente a PreparedStatement)
    public void EjemploConParametros()
    {
        using var connection = new NpgsqlConnection(_connectionString);
        connection.Open();

        using var command = connection.CreateCommand();
        command.CommandText = "SELECT * FROM customers WHERE age > @minAge";
        
        // Los parámetros previenen SQL injection automáticamente
        command.Parameters.AddWithValue("@minAge", 18);

        using var reader = command.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine(reader.GetString("name"));
        }
    }
}
```

---

## Entity Framework Core: ORM moderno

**Entity Framework Core (EF Core)** es el ORM recomendado para .NET, equivalente a Hibernate/Spring Data JPA.  Resuelve el desfase objeto-relacional mapeando automáticamente entre clases de C# y tablas de base de datos.

**Instalación:**

```bash
dotnet add package Microsoft.EntityFrameworkCore
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Microsoft.EntityFrameworkCore.Design
```

**1. Definición de entidades:**

```csharp
using System. ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace MiAplicacion.Domain. Entities;

[Table("productos")]
public class Producto
{
    [Key]
    [Column("id")]
    public Guid Id { get; set; } = Guid.NewGuid();

    [Required]
    [MaxLength(200)]
    [Column("nombre")]
    public string Nombre { get; set; } = string.Empty;

    [Column("precio")]
    [Precision(18, 2)]
    public decimal Precio { get; set; }

    [Column("cantidad")]
    public int Cantidad { get; set; }

    [Column("categoria")]
    [MaxLength(100)]
    public string Categoria { get; set; } = string.Empty;

    [Column("fecha_creacion")]
    public DateTime FechaCreacion { get; set; } = DateTime.UtcNow;

    [Column("activo")]
    public bool Activo { get; set; } = true;
}
```

**2. Configuración del DbContext:**

```csharp
using Microsoft.EntityFrameworkCore;
using MiAplicacion.Domain.Entities;

namespace MiAplicacion.Infrastructure.Data;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    // DbSet representa una tabla
    public DbSet<Producto> Productos => Set<Producto>();

    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // Configuración con Fluent API (alternativa a Data Annotations)
        modelBuilder. Entity<Producto>(entity =>
        {
            entity.ToTable("productos");
            entity. HasKey(p => p.Id);

            entity.Property(p => p.Nombre)
                .IsRequired()
                .HasMaxLength(200);

            entity.Property(p => p.Precio)
                .HasPrecision(18, 2);

            entity.Property(p => p. Categoria)
                .HasMaxLength(100);

            // Índices
            entity.HasIndex(p => p.Categoria);
            entity.HasIndex(p => p.Nombre);

            // Valores por defecto
            entity.Property(p => p.FechaCreacion)
                .HasDefaultValueSql("CURRENT_TIMESTAMP");
        });

        // Seed data (datos iniciales)
        modelBuilder.Entity<Producto>().HasData(
            new Producto 
            { 
                Id = Guid.NewGuid(), 
                Nombre = "Laptop", 
                Precio = 1200m, 
                Cantidad = 10,
                Categoria = "Electrónica"
            },
            new Producto 
            { 
                Id = Guid.NewGuid(), 
                Nombre = "Mouse", 
                Precio = 25m, 
                Cantidad = 50,
                Categoria = "Accesorios"
            }
        );
    }
}
```

**3. Registro en Program.cs:**

```csharp
using Microsoft.EntityFrameworkCore;
using MiAplicacion.Infrastructure. Data;

var builder = WebApplication.CreateBuilder(args);

// Registrar DbContext
builder. Services.AddDbContext<ApplicationDbContext>(options =>
    options. UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection"),
        npgsqlOptions => npgsqlOptions.EnableRetryOnFailure(
            maxRetryCount: 5,
            maxRetryDelay: TimeSpan. FromSeconds(30),
            errorCodesToAdd: null
        )
    )
);

var app = builder.Build();

// Aplicar migraciones al iniciar (solo desarrollo)
if (app.Environment. IsDevelopment())
{
    using var scope = app.Services.CreateScope();
    var context = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
    await context.Database.MigrateAsync();
}

app.Run();
```

---

## Patrón Repository y Unit of Work

El **patrón Repository** abstrae el acceso a datos y proporciona una interfaz limpia para las operaciones CRUD. El **patrón Unit of Work** gestiona las transacciones. 

**Interfaz del repositorio:**

```csharp
namespace MiAplicacion.Domain. Interfaces;

public interface IRepository<T> where T : class
{
    Task<List<T>> GetAllAsync();
    Task<T? > GetByIdAsync(Guid id);
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(Guid id);
    Task<bool> ExistsAsync(Guid id);
}
```

**Implementación del repositorio con EF Core:**

```csharp
using Microsoft.EntityFrameworkCore;
using MiAplicacion.Domain.Entities;
using MiAplicacion.Domain. Interfaces;
using MiAplicacion.Infrastructure.Data;

namespace MiAplicacion.Infrastructure.Repositories;

public class ProductoRepository : IRepository<Producto>
{
    private readonly ApplicationDbContext _context;

    public ProductoRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<List<Producto>> GetAllAsync()
    {
        return await _context.Productos
            .Where(p => p.Activo)
            .OrderBy(p => p.Nombre)
            .ToListAsync();
    }

    public async Task<Producto?> GetByIdAsync(Guid id)
    {
        return await _context. Productos
            .FirstOrDefaultAsync(p => p.Id == id && p.Activo);
    }

    public async Task<T> AddAsync(Producto producto)
    {
        _context.Productos.Add(producto);
        await _context.SaveChangesAsync();
        return producto;
    }

    public async Task UpdateAsync(Producto producto)
    {
        _context. Productos.Update(producto);
        await _context.SaveChangesAsync();
    }

    public async Task DeleteAsync(Guid id)
    {
        var producto = await GetByIdAsync(id);
        if (producto != null)
        {
            // Borrado lógico (recomendado)
            producto.Activo = false;
            await UpdateAsync(producto);

            // Borrado físico (alternativa)
            // _context. Productos.Remove(producto);
            // await _context.SaveChangesAsync();
        }
    }

    public async Task<bool> ExistsAsync(Guid id)
    {
        return await _context.Productos
            .AnyAsync(p => p.Id == id && p. Activo);
    }

    // Métodos personalizados
    public async Task<List<Producto>> GetByCategoriaAsync(string categoria)
    {
        return await _context.Productos
            . Where(p => p.Categoria == categoria && p.Activo)
            .ToListAsync();
    }

    public async Task<List<Producto>> GetProductosCarosAsync(decimal precioMinimo)
    {
        return await _context.Productos
            .Where(p => p. Precio > precioMinimo && p. Activo)
            .OrderByDescending(p => p. Precio)
            .ToListAsync();
    }
}
```

**Patrón Unit of Work (gestión de transacciones):**

```csharp
public interface IUnitOfWork :  IDisposable
{
    IRepository<Producto> Productos { get; }
    Task<int> SaveChangesAsync();
    Task BeginTransactionAsync();
    Task CommitAsync();
    Task RollbackAsync();
}

public class UnitOfWork : IUnitOfWork
{
    private readonly ApplicationDbContext _context;
    private IDbContextTransaction?  _transaction;

    public UnitOfWork(ApplicationDbContext context)
    {
        _context = context;
        Productos = new ProductoRepository(context);
    }

    public IRepository<Producto> Productos { get; }

    public async Task<int> SaveChangesAsync()
    {
        return await _context.SaveChangesAsync();
    }

    public async Task BeginTransactionAsync()
    {
        _transaction = await _context.Database.BeginTransactionAsync();
    }

    public async Task CommitAsync()
    {
        try
        {
            await SaveChangesAsync();
            await _transaction?.CommitAsync()! ;
        }
        catch
        {
            await RollbackAsync();
            throw;
        }
        finally
        {
            _transaction?.Dispose();
        }
    }

    public async Task RollbackAsync()
    {
        await _transaction?.RollbackAsync()!;
        _transaction?.Dispose();
    }

    public void Dispose()
    {
        _transaction?.Dispose();
        _context.Dispose();
    }
}
```

---

## CRUD con Entity Framework Core

**Servicio con operaciones CRUD completas:**

```csharp
using CSharpFunctionalExtensions;
using MiAplicacion.Domain.Entities;
using MiAplicacion.Domain.Interfaces;

namespace MiAplicacion.Application.Services;

public class ProductoService
{
    private readonly IRepository<Producto> _repository;

    public ProductoService(IRepository<Producto> repository)
    {
        _repository = repository;
    }

    // CREATE
    public async Task<Result<Producto>> CrearProductoAsync(CrearProductoDto dto)
    {
        return await Result. Success(dto)
            . Ensure(d => ! string.IsNullOrWhiteSpace(d.Nombre), "El nombre es requerido")
            .Ensure(d => d.Precio > 0, "El precio debe ser mayor que cero")
            .Ensure(d => d.Cantidad >= 0, "La cantidad no puede ser negativa")
            .Bind(async d =>
            {
                var producto = new Producto
                {
                    Nombre = d.Nombre,
                    Precio = d.Precio,
                    Cantidad = d.Cantidad,
                    Categoria = d. Categoria
                };

                await _repository.AddAsync(producto);
                return Result. Success(producto);
            });
    }

    // READ ALL
    public async Task<Result<List<Producto>>> ObtenerTodosAsync()
    {
        try
        {
            var productos = await _repository.GetAllAsync();
            return Result.Success(productos);
        }
        catch (Exception ex)
        {
            return Result.Failure<List<Producto>>($"Error al obtener productos:  {ex.Message}");
        }
    }

    // READ BY ID
    public async Task<Result<Producto>> ObtenerPorIdAsync(Guid id)
    {
        var producto = await _repository.GetByIdAsync(id);
        
        return producto != null
            ? Result.Success(producto)
            : Result. Failure<Producto>("Producto no encontrado");
    }

    // UPDATE
    public async Task<Result<Producto>> ActualizarProductoAsync(Guid id, ActualizarProductoDto dto)
    {
        var productoExistente = await _repository.GetByIdAsync(id);
        
        if (productoExistente == null)
            return Result.Failure<Producto>("Producto no encontrado");

        return await Result.Success(dto)
            .Ensure(d => ! string.IsNullOrWhiteSpace(d.Nombre), "El nombre es requerido")
            .Ensure(d => d.Precio > 0, "El precio debe ser mayor que cero")
            .Bind(async d =>
            {
                productoExistente.Nombre = d.Nombre;
                productoExistente.Precio = d.Precio;
                productoExistente.Cantidad = d.Cantidad;
                productoExistente.Categoria = d.Categoria;

                await _repository.UpdateAsync(productoExistente);
                return Result.Success(productoExistente);
            });
    }

    // DELETE
    public async Task<Result> EliminarProductoAsync(Guid id)
    {
        var existe = await _repository.ExistsAsync(id);
        
        if (!existe)
            return Result.Failure("Producto no encontrado");

        await _repository.DeleteAsync(id);
        return Result.Success();
    }
}

public record CrearProductoDto(string Nombre, decimal Precio, int Cantidad, string Categoria);
public record ActualizarProductoDto(string Nombre, decimal Precio, int Cantidad, string Categoria);
```

---

## Consultas con LINQ y SQL raw

**Consultas LINQ avanzadas:**

```csharp
// Consultas con filtros múltiples
var productos = await _context.Productos
    .Where(p => p.Activo)
    .Where(p => p.Precio > 100)
    .Where(p => p.Categoria == "Electrónica")
    .ToListAsync();

// Proyecciones
var resumen = await _context.Productos
    .Select(p => new
    {
        p.Nombre,
        p.Precio,
        PrecioConIVA = p.Precio * 1.21m
    })
    .ToListAsync();

// Agrupaciones
var porCategoria = await _context. Productos
    .GroupBy(p => p.Categoria)
    .Select(g => new
    {
        Categoria = g. Key,
        Cantidad = g.Count(),
        PrecioPromedio = g.Average(p => p.Precio),
        PrecioTotal = g.Sum(p => p. Precio)
    })
    .ToListAsync();

// Joins (si hubiera relaciones)
var productosConDetalles = await _context. Productos
    .Include(p => p.Categoria) // Eager loading
    .ThenInclude(c => c. Proveedor)
    .ToListAsync();
```

**SQL raw cuando LINQ no es suficiente:**

```csharp
// Consulta SQL raw con parámetros
var productos = await _context.Productos
    . FromSqlRaw("SELECT * FROM productos WHERE precio > {0} AND categoria = {1}", 
        100, "Electrónica")
    .ToListAsync();

// Procedimientos almacenados
var resultado = await _context.Database
    .ExecuteSqlRawAsync("CALL actualizar_precios(@p0)", 1. 10);
```

---

## Migraciones y gestión de esquema

Las **migraciones** permiten evolucionar el esquema de la base de datos de forma controlada. 

```bash
# Crear migración
dotnet ef migrations add InitialCreate

# Aplicar migraciones
dotnet ef database update

# Revertir migración
dotnet ef database update PreviousMigration

# Eliminar última migración (si no se aplicó)
dotnet ef migrations remove

# Generar script SQL
dotnet ef migrations script

# Ver migraciones pendientes
dotnet ef migrations list
```

**Ejemplo de migración generada:**

```csharp
public partial class InitialCreate : Migration
{
    protected override void Up(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.CreateTable(
            name: "productos",
            columns: table => new
            {
                id = table.Column<Guid>(nullable: false),
                nombre = table.Column<string>(maxLength: 200, nullable: false),
                precio = table.Column<decimal>(precision: 18, scale:  2, nullable: false),
                cantidad = table.Column<int>(nullable: false),
                categoria = table.Column<string>(maxLength: 100, nullable: false),
                fecha_creacion = table.Column<DateTime>(nullable: false, defaultValueSql: "CURRENT_TIMESTAMP"),
                activo = table.Column<bool>(nullable: false, defaultValue: true)
            },
            constraints: table =>
            {
                table.PrimaryKey("PK_productos", x => x. id);
            });

        migrationBuilder.CreateIndex(
            name: "IX_productos_categoria",
            table: "productos",
            column: "categoria");
    }

    protected override void Down(MigrationBuilder migrationBuilder)
    {
        migrationBuilder.DropTable(name: "productos");
    }
}
```

---

