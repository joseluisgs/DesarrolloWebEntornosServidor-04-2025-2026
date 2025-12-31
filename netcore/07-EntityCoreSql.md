# Entity Framework Core en ASP.NET Core

- [Entity Framework Core en ASP.NET Core](#entity-framework-core-en-aspnet-core)
  - [¿Qué es Entity Framework Core?](#qué-es-entity-framework-core)
  - [Configuración Inicial](#configuración-inicial)
    - [Instalación de Paquetes NuGet](#instalación-de-paquetes-nuget)
    - [Configuración de la Conexión](#configuración-de-la-conexión)
    - [Registro del DbContext](#registro-del-dbcontext)
  - [Entidades y DbContext](#entidades-y-dbcontext)
    - [Definiendo Entidades](#definiendo-entidades)
    - [Identificadores](#identificadores)
      - [Identity (Autoincremental)](#identity-autoincremental)
      - [GUID/UUID](#guiduuid)
      - [Comparación](#comparación)
    - [Marcas Temporales (Timestamps)](#marcas-temporales-timestamps)
    - [Validaciones con Data Annotations](#validaciones-con-data-annotations)
  - [Relaciones entre Entidades](#relaciones-entre-entidades)
    - [Relación Uno a Uno (One-to-One)](#relación-uno-a-uno-one-to-one)
    - [Relación Uno a Muchos (One-to-Many)](#relación-uno-a-muchos-one-to-many)
    - [Relación Muchos a Muchos (Many-to-Many)](#relación-muchos-a-muchos-many-to-many)
    - [Propiedades de Navegación](#propiedades-de-navegación)
    - [Entidades Embebidas (Owned Types)](#entidades-embebidas-owned-types)
    - [Herencia (Table Per Hierarchy)](#herencia-table-per-hierarchy)
    - [Opciones de Cascada](#opciones-de-cascada)
    - [Eager Loading vs Lazy Loading](#eager-loading-vs-lazy-loading)
    - [Borrado Físico vs Lógico](#borrado-físico-vs-lógico)
    - [Recursión Infinita en JSON](#recursión-infinita-en-json)
  - [Migraciones](#migraciones)
    - [Crear una Migración](#crear-una-migración)
    - [Aplicar Migraciones](#aplicar-migraciones)
    - [Comandos Útiles](#comandos-útiles)
  - [Repositorios con EF Core](#repositorios-con-ef-core)
    - [Repositorio Genérico](#repositorio-genérico)
    - [Repositorio Específico](#repositorio-específico)
    - [Consultas con LINQ](#consultas-con-linq)
    - [Consultas SQL Nativas](#consultas-sql-nativas)
  - [Testing con EF Core](#testing-con-ef-core)
    - [InMemory Database](#inmemory-database)
    - [TestContainers](#testcontainers)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![EF Core Banner](../images/banner07.png)

---

## ¿Qué es Entity Framework Core?

**Entity Framework Core (EF Core)** es un ORM (Object-Relational Mapper) moderno, ligero y extensible para .NET.   Permite trabajar con bases de datos relacionales usando objetos . NET, eliminando la necesidad de escribir SQL manualmente en la mayoría de los casos. 

**Características principales:**

✅ **Code First**: Define el modelo de datos con clases C# y EF Core crea la base de datos

✅ **Database First**: Genera clases C# a partir de una base de datos existente

✅ **Migraciones**: Control de versiones del esquema de la base de datos

✅ **LINQ**: Consultas fuertemente tipadas con C#

✅ **Change Tracking**: Seguimiento automático de cambios en entidades

✅ **Soporte multi-plataforma**: SQL Server, PostgreSQL, MySQL, SQLite, In-Memory, etc.

---

## Configuración Inicial

### Instalación de Paquetes NuGet

```bash
# EF Core base
dotnet add package Microsoft.EntityFrameworkCore

# Provider para SQL Server
dotnet add package Microsoft.EntityFrameworkCore.SqlServer

# Provider para PostgreSQL
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL

# Provider para SQLite
dotnet add package Microsoft. EntityFrameworkCore. Sqlite

# Herramientas de línea de comandos para migraciones
dotnet add package Microsoft.EntityFrameworkCore.Design

# Para InMemory (solo para tests)
dotnet add package Microsoft.EntityFrameworkCore.InMemory
```

---

### Configuración de la Conexión

**appsettings.json:**

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=FunkosDb;User Id=sa;Password=YourPassword;TrustServerCertificate=True",
    "PostgresConnection": "Host=localhost;Database=funkosdb;Username=postgres;Password=postgres",
    "SqliteConnection": "Data Source=funkos.db"
  },
  "Logging": {
    "LogLevel":  {
      "Default": "Information",
      "Microsoft.EntityFrameworkCore": "Warning"
    }
  }
}
```

**appsettings.Development.json:**

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Server=localhost;Database=FunkosDb_Dev;User Id=sa;Password=DevPassword;TrustServerCertificate=True"
  },
  "Logging": {
    "LogLevel":  {
      "Microsoft.EntityFrameworkCore. Database.Command": "Information"
    }
  }
}
```

---

### Registro del DbContext

**Program.cs:**

```csharp
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

// Registrar DbContext con SQL Server
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(
        builder.Configuration.GetConnectionString("DefaultConnection"),
        sqlOptions => sqlOptions.EnableRetryOnFailure() // Reintentos automáticos
    )
);

// Alternativa: PostgreSQL
// builder.Services.AddDbContext<ApplicationDbContext>(options =>
//     options.UseNpgsql(builder.Configuration.GetConnectionString("PostgresConnection"))
// );

// Alternativa: SQLite
// builder.Services.AddDbContext<ApplicationDbContext>(options =>
//     options.UseSqlite(builder.Configuration.GetConnectionString("SqliteConnection"))
// );

// Habilitar logging detallado en desarrollo
if (builder.Environment.IsDevelopment())
{
    builder.Services.AddDbContext<ApplicationDbContext>(options =>
        options
            .UseSqlServer(builder.Configuration.GetConnectionString("DefaultConnection"))
            .EnableSensitiveDataLogging() // Mostrar valores de parámetros
            .EnableDetailedErrors() // Errores detallados
    );
}

var app = builder.Build();

// Aplicar migraciones automáticamente al iniciar (opcional, solo en dev)
using (var scope = app.Services.CreateScope())
{
    var dbContext = scope. ServiceProvider.GetRequiredService<ApplicationDbContext>();
    dbContext.Database.Migrate();
}

app.Run();
```

---

## Entidades y DbContext

### Definiendo Entidades

**Entidad Funko:**

```csharp
using System. ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;

namespace FunkosApi.Models;

[Table("funkos")] // Nombre de la tabla
public class Funko
{
    [Key] // Clave primaria
    [DatabaseGenerated(DatabaseGeneratedOption. Identity)] // Autoincremental
    public int Id { get; set; }

    [Required(ErrorMessage = "El nombre es obligatorio")]
    [StringLength(100, MinimumLength = 3, ErrorMessage = "El nombre debe tener entre 3 y 100 caracteres")]
    [Column("nombre", TypeName = "varchar(100)")] // Nombre de columna y tipo SQL
    public string Nombre { get; set; } = string.Empty;

    [Required]
    [Range(0.01, double.MaxValue, ErrorMessage = "El precio debe ser mayor a 0")]
    [Column("precio", TypeName = "decimal(18,2)")]
    public decimal Precio { get; set; }

    [Range(0, int.MaxValue, ErrorMessage = "La cantidad no puede ser negativa")]
    [Column("cantidad")]
    public int Cantidad { get; set; }

    [Required(ErrorMessage = "La categoría es obligatoria")]
    [StringLength(50)]
    [Column("categoria", TypeName = "varchar(50)")]
    public string Categoria { get; set; } = string.Empty;

    [StringLength(500)]
    [Column("imagen", TypeName = "varchar(500)")]
    public string?  Imagen { get; set; }

    [Column("fecha_creacion")]
    public DateTime FechaCreacion { get; set; } = DateTime.UtcNow;

    [Column("fecha_actualizacion")]
    public DateTime FechaActualizacion { get; set; } = DateTime.UtcNow;

    [Column("activo")]
    public bool Activo { get; set; } = true; // Borrado lógico
}
```

---

### Identificadores

#### Identity (Autoincremental)

```csharp
[Key]
[DatabaseGenerated(DatabaseGeneratedOption.Identity)]
public int Id { get; set; }
```

**Ventajas:**
- ✅ Simple y eficiente
- ✅ Soporte nativo en SQL Server, PostgreSQL, MySQL
- ✅ Índices automáticos

**Desventajas:**
- ❌ No conoces el ID hasta insertar en la BD
- ❌ Dificulta tests unitarios
- ❌ Problemas con relaciones antes de guardar

---

#### GUID/UUID

```csharp
[Key]
public Guid Id { get; set; } = Guid.NewGuid();
```

**Ventajas:**
- ✅ ID conocido antes de insertar
- ✅ Facilita tests y relaciones
- ✅ Útil para sistemas distribuidos

**Desventajas:**
- ❌ Más espacio de almacenamiento (16 bytes vs 4 bytes)
- ❌ Índices más grandes y lentos
- ❌ Menos legible

---

#### Comparación

| Aspecto | Identity | GUID |
|: --------|:---------|:-----|
| **Tamaño** | 4 bytes (int) | 16 bytes (Guid) |
| **Legibilidad** | ✅ Sí (1, 2, 3.. .) | ❌ No (guid largo) |
| **Conocido antes de Insert** | ❌ No | ✅ Sí |
| **Testing** | ❌ Complicado | ✅ Fácil |
| **Rendimiento** | ✅ Mejor | ⚠️ Aceptable |
| **Sistemas distribuidos** | ❌ Conflictos | ✅ Sin conflictos |

**Recomendación:** Usa `int` con `Identity` salvo que necesites GUIDs por distribución o testing.

---

### Marcas Temporales (Timestamps)

**Opción 1: Manualmente en la entidad**

```csharp
public DateTime FechaCreacion { get; set; } = DateTime.UtcNow;
public DateTime FechaActualizacion { get; set; } = DateTime.UtcNow;
```

**Opción 2: Sobrescribir `SaveChangesAsync` en el DbContext**

```csharp
public class ApplicationDbContext : DbContext
{
    public DbSet<Funko> Funkos { get; set; } = null!;

    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options) : base(options) { }

    public override Task<int> SaveChangesAsync(CancellationToken cancellationToken = default)
    {
        var entries = ChangeTracker.Entries()
            .Where(e => e.Entity is Funko && (e.State == EntityState.Added || e.State == EntityState. Modified));

        foreach (var entry in entries)
        {
            var funko = (Funko)entry.Entity;

            if (entry.State == EntityState.Added)
            {
                funko. FechaCreacion = DateTime. UtcNow;
            }

            funko.FechaActualizacion = DateTime.UtcNow;
        }

        return base.SaveChangesAsync(cancellationToken);
    }
}
```

---

### Validaciones con Data Annotations

Ya vimos las **Data Annotations** anteriormente.   Aquí un resumen aplicado a entidades:

```csharp
[Required] // No nulo
[StringLength(100, MinimumLength = 3)] // Longitud
[Range(0.01, double.MaxValue)] // Rango numérico
[EmailAddress] // Formato de email
[RegularExpression(@"^\d{3}-\d{2}-\d{4}$")] // Regex
[Url] // URL válida
[Phone] // Número de teléfono
[CreditCard] // Tarjeta de crédito
[Compare("OtraPropiedad")] // Comparar con otra propiedad
```

---

## Relaciones entre Entidades

### Relación Uno a Uno (One-to-One)

**Ejemplo:   Funko y Detalle**

```csharp
public class Funko
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string.Empty;

    // Propiedad de navegación
    public DetalleFunko?  Detalle { get; set; }
}

public class DetalleFunko
{
    public int Id { get; set; }
    public string Descripcion { get; set; } = string.Empty;
    public string Fabricante { get; set; } = string.Empty;

    // Clave foránea
    public int FunkoId { get; set; }

    // Propiedad de navegación inversa
    public Funko Funko { get; set; } = null!;
}
```

**Configuración en DbContext:**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Funko>()
        .HasOne(f => f.Detalle)
        .WithOne(d => d.Funko)
        .HasForeignKey<DetalleFunko>(d => d. FunkoId)
        .OnDelete(DeleteBehavior.Cascade);
}
```

---

### Relación Uno a Muchos (One-to-Many)

**Ejemplo:   Categoría y Funkos**

```csharp
public class Categoria
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string. Empty;

    // Colección de navegación
    public ICollection<Funko> Funkos { get; set; } = new List<Funko>();
}

public class Funko
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string. Empty;

    // Clave foránea
    public int CategoriaId { get; set; }

    // Propiedad de navegación
    public Categoria Categoria { get; set; } = null!;
}
```

**Configuración:**

```csharp
modelBuilder.Entity<Categoria>()
    .HasMany(c => c.Funkos)
    .WithOne(f => f.Categoria)
    .HasForeignKey(f => f.CategoriaId)
    .OnDelete(DeleteBehavior.Restrict); // No eliminar categoría si tiene funkos
```

---

### Relación Muchos a Muchos (Many-to-Many)

**Ejemplo:  Funko y Etiquetas**

```csharp
public class Funko
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string.Empty;

    public ICollection<Etiqueta> Etiquetas { get; set; } = new List<Etiqueta>();
}

public class Etiqueta
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string. Empty;

    public ICollection<Funko> Funkos { get; set; } = new List<Funko>();
}
```

**Configuración (EF Core 5+):**

```csharp
modelBuilder.Entity<Funko>()
    .HasMany(f => f.Etiquetas)
    .WithMany(e => e.Funkos)
    .UsingEntity(j => j.ToTable("FunkoEtiquetas")); // Tabla intermedia
```

**Configuración manual (con entidad intermedia explícita):**

```csharp
public class FunkoEtiqueta
{
    public int FunkoId { get; set; }
    public Funko Funko { get; set; } = null!;

    public int EtiquetaId { get; set; }
    public Etiqueta Etiqueta { get; set; } = null!;

    public DateTime FechaAsignacion { get; set; } = DateTime.UtcNow;
}

modelBuilder.Entity<FunkoEtiqueta>()
    .HasKey(fe => new { fe.FunkoId, fe.EtiquetaId }); // Clave compuesta

modelBuilder.Entity<FunkoEtiqueta>()
    .HasOne(fe => fe. Funko)
    .WithMany()
    .HasForeignKey(fe => fe.FunkoId);

modelBuilder.Entity<FunkoEtiqueta>()
    .HasOne(fe => fe.Etiqueta)
    .WithMany()
    .HasForeignKey(fe => fe.EtiquetaId);
```

---

### Propiedades de Navegación

**Importante:**  

- **Propiedad de navegación**:  Permite navegar desde una entidad a otra (ej: `Funko.Categoria`)
- **Clave foránea**: Campo que almacena el ID de la entidad relacionada (ej: `CategoriaId`)

```csharp
public class Funko
{
    public int Id { get; set; }

    // Clave foránea (opcional pero recomendada)
    public int? CategoriaId { get; set; }

    // Propiedad de navegación
    public Categoria?  Categoria { get; set; }
}
```

**Si omites la clave foránea**, EF Core la creará automáticamente como "shadow property" (no visible en la clase).

---

### Entidades Embebidas (Owned Types)

Similar a `@Embeddable` en JPA.

```csharp
public class Funko
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string.Empty;

    // Entidad embebida
    public Direccion Direccion { get; set; } = new();
}

[Owned] // Marca la entidad como "owned"
public class Direccion
{
    public string Calle { get; set; } = string.Empty;
    public string Ciudad { get.  set; } = string.Empty;
    public string CodigoPostal { get.  set; } = string.Empty;
}
```

**Configuración:**

```csharp
modelBuilder.Entity<Funko>()
    .OwnsOne(f => f. Direccion);
```

**Resultado:**  Las propiedades de `Direccion` se mapean a columnas de la tabla `funkos` (ej: `Direccion_Calle`, `Direccion_Ciudad`).

---

### Herencia (Table Per Hierarchy)

**Ejemplo:  Jerarquía de Productos**

```csharp
public abstract class Producto
{
    public int Id { get.  set; }
    public string Nombre { get. set; } = string.Empty;
    public decimal Precio { get. set; }
}

public class Funko : Producto
{
    public string Categoria { get.  set; } = string.Empty;
}

public class Libro : Producto
{
    public string Autor { get. set; } = string.Empty;
    public int Paginas { get. set; }
}
```

**Configuración:**

```csharp
modelBuilder.Entity<Producto>()
    .HasDiscriminator<string>("TipoProducto")
    .HasValue<Funko>("Funko")
    .HasValue<Libro>("Libro");
```

**Resultado:**  Una sola tabla `productos` con columna discriminadora `TipoProducto`.

---

### Opciones de Cascada

**DeleteBehavior:**

- `Cascade`: Eliminar entidades relacionadas automáticamente
- `Restrict`: No permitir eliminación si hay entidades relacionadas
- `SetNull`: Establecer clave foránea en NULL
- `NoAction`: No hacer nada (puede lanzar error de BD)

```csharp
modelBuilder.Entity<Categoria>()
    .HasMany(c => c.Funkos)
    .WithOne(f => f.Categoria)
    .OnDelete(DeleteBehavior. Cascade); // Eliminar funkos al eliminar categoría
```

---

### Eager Loading vs Lazy Loading

**Eager Loading (Carga anticipada):**

```csharp
// Cargar funkos con sus categorías
var funkos = await _context.Funkos
    . Include(f => f.Categoria) // Eager loading
    .ToListAsync();
```

**Lazy Loading (Carga perezosa):**

```csharp
// Instalar paquete
dotnet add package Microsoft.EntityFrameworkCore.Proxies

// Habilitar en DbContext
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseSqlServer(connectionString)
           .UseLazyLoadingProxies() // Habilitar lazy loading
);

// Marcar propiedades de navegación como virtual
public class Funko
{
    public int Id { get.  set; }
    public string Nombre { get. set; } = string.Empty;

    public int CategoriaId { get. set; }
    public virtual Categoria Categoria { get. set; } = null!; // Virtual para lazy loading
}

// Uso (carga automática al acceder)
var funko = await _context.Funkos. FindAsync(1);
var categoriaNombre = funko.Categoria.Nombre; // Se carga automáticamente
```

⚠️ **Advertencia:** Lazy Loading puede causar el problema **N+1** (muchas consultas a la BD).

---

### Borrado Físico vs Lógico

**Borrado Lógico (Soft Delete):**

```csharp
public class Funko
{
    public int Id { get.  set; }
    public string Nombre { get. set; } = string.Empty;
    
    // Campo para borrado lógico
    public bool Activo { get. set; } = true;
    public DateTime?  FechaEliminacion { get. set; }
}
```

**Filtro global para ocultar entidades eliminadas:**

```csharp
protected override void OnModelCreating(ModelBuilder modelBuilder)
{
    modelBuilder.Entity<Funko>()
        .HasQueryFilter(f => f.Activo); // Solo mostrar activos
}
```

**Repositorio con borrado lógico:**

```csharp
public class FunkoRepository
{
    private readonly ApplicationDbContext _context;

    public async Task<bool> DeleteAsync(int id)
    {
        var funko = await _context.Funkos.FindAsync(id);
        if (funko is null) return false;

        // Borrado lógico
        funko.Activo = false;
        funko.FechaEliminacion = DateTime.UtcNow;

        await _context.SaveChangesAsync();
        return true;
    }

    public async Task<List<Funko>> GetAllIncludingDeletedAsync()
    {
        // Ignorar el filtro global para ver eliminados
        return await _context. Funkos
            . IgnoreQueryFilters()
            .ToListAsync();
    }
}
```

---

### Recursión Infinita en JSON

Al serializar entidades con relaciones bidireccionales, puedes causar un ciclo infinito.

**Solución 1:  `[JsonIgnore]`**

```csharp
public class Funko
{
    public int Id { get.  set; }
    public string Nombre { get. set; } = string.Empty;

    public int CategoriaId { get.  set; }
    public Categoria Categoria { get. set; } = null!;
}

public class Categoria
{
    public int Id { get. set; }
    public string Nombre { get. set; } = string.Empty;

    [JsonIgnore] // Ignorar al serializar
    public ICollection<Funko> Funkos { get.  set; } = new List<Funko>();
}
```

**Solución 2:  Configurar ciclos en `Program.cs`**

```csharp
builder.Services.AddControllers()
    .AddJsonOptions(options =>
    {
        options.JsonSerializerOptions.ReferenceHandler = ReferenceHandler. IgnoreCycles;
    });
```

**Solución 3 (recomendada):  Usar DTOs**

Mapear entidades a DTOs que no tengan relaciones bidireccionales. 

---

## Migraciones

Las **migraciones** son control de versiones para el esquema de la base de datos.

### Crear una Migración

```bash
# Crear migración inicial
dotnet ef migrations add InitialCreate

# Crear migración con nombre descriptivo
dotnet ef migrations add AddCategoriasTable

# Ver SQL que se ejecutará
dotnet ef migrations script
```

**Estructura generada:**

```
Migrations/
├── 20250115120000_InitialCreate.cs
├── 20250115120000_InitialCreate.Designer.cs
└── ApplicationDbContextModelSnapshot.cs
```

---

### Aplicar Migraciones

```bash
# Aplicar migraciones pendientes
dotnet ef database update

# Aplicar hasta una migración específica
dotnet ef database update AddCategoriasTable

# Revertir todas las migraciones
dotnet ef database update 0

# Aplicar en producción
dotnet ef database update --connection "Server=prod;Database=FunkosDb;..."
```

---

### Comandos Útiles

```bash
# Listar migraciones
dotnet ef migrations list

# Eliminar última migración (si no se aplicó)
dotnet ef migrations remove

# Generar script SQL de todas las migraciones
dotnet ef migrations script -o migrations.sql

# Ver SQL de la última migración
dotnet ef migrations script --output last-migration.sql

# Crear base de datos sin aplicar migraciones
dotnet ef database update 0
```

---

## Repositorios con EF Core

### Repositorio Genérico

```csharp
public interface IRepository<T> where T : class
{
    Task<IEnumerable<T>> GetAllAsync();
    Task<T?> GetByIdAsync(int id);
    Task<T> AddAsync(T entity);
    Task<T> UpdateAsync(T entity);
    Task<bool> DeleteAsync(int id);
    Task<bool> ExistsAsync(int id);
}

public class Repository<T> : IRepository<T> where T : class
{
    protected readonly ApplicationDbContext _context;
    protected readonly DbSet<T> _dbSet;

    public Repository(ApplicationDbContext context)
    {
        _context = context;
        _dbSet = context.Set<T>();
    }

    public virtual async Task<IEnumerable<T>> GetAllAsync()
    {
        return await _dbSet.ToListAsync();
    }

    public virtual async Task<T?> GetByIdAsync(int id)
    {
        return await _dbSet.FindAsync(id);
    }

    public virtual async Task<T> AddAsync(T entity)
    {
        await _dbSet.AddAsync(entity);
        await _context.SaveChangesAsync();
        return entity;
    }

    public virtual async Task<T> UpdateAsync(T entity)
    {
        _dbSet.Update(entity);
        await _context. SaveChangesAsync();
        return entity;
    }

    public virtual async Task<bool> DeleteAsync(int id)
    {
        var entity = await _dbSet.FindAsync(id);
        if (entity is null) return false;

        _dbSet.Remove(entity);
        await _context.SaveChangesAsync();
        return true;
    }

    public virtual async Task<bool> ExistsAsync(int id)
    {
        return await _dbSet.FindAsync(id) is not null;
    }
}
```

---

### Repositorio Específico

```csharp
public interface IFunkoRepository : IRepository<Funko>
{
    Task<IEnumerable<Funko>> GetByCategor iaAsync(string categoria);
    Task<Funko?> GetByNombreAsync(string nombre);
    Task<IEnumerable<Funko>> SearchAsync(string searchTerm);
    Task<bool> ExistsByNombreAsync(string nombre);
}

public class FunkoRepository :  Repository<Funko>, IFunkoRepository
{
    public FunkoRepository(ApplicationDbContext context) : base(context) { }

    public override async Task<IEnumerable<Funko>> GetAllAsync()
    {
        return await _dbSet
            .Include(f => f. Categoria) // Eager loading
            .Where(f => f.Activo) // Solo activos
            .ToListAsync();
    }

    public override async Task<Funko?> GetByIdAsync(int id)
    {
        return await _dbSet
            .Include(f => f.Categoria)
            .FirstOrDefaultAsync(f => f.Id == id && f.Activo);
    }

    public async Task<IEnumerable<Funko>> GetByCategoriaAsync(string categoria)
    {
        return await _dbSet
            .Where(f => f.Categoria == categoria && f.Activo)
            .OrderBy(f => f.Nombre)
            .ToListAsync();
    }

    public async Task<Funko?> GetByNombreAsync(string nombre)
    {
        return await _dbSet
            .FirstOrDefaultAsync(f => f. Nombre == nombre && f. Activo);
    }

    public async Task<IEnumerable<Funko>> SearchAsync(string searchTerm)
    {
        return await _dbSet
            .Where(f => f.Activo &&
                       (f.Nombre.Contains(searchTerm) ||
                        f.Categoria. Contains(searchTerm)))
            .ToListAsync();
    }

    public async Task<bool> ExistsByNombreAsync(string nombre)
    {
        return await _dbSet
            .AnyAsync(f => f.Nombre == nombre && f.Activo);
    }
}
```

---

### Consultas con LINQ

```csharp
// Filtrar
var funkos = await _context. Funkos
    .Where(f => f.Precio > 20 && f.Activo)
    .ToListAsync();

// Ordenar
var funkos = await _context.Funkos
    . OrderBy(f => f. Nombre)
    .ThenByDescending(f => f. Precio)
    .ToListAsync();

// Proyección (Select)
var nombres = await _context.Funkos
    .Select(f => f. Nombre)
    .ToListAsync();

// Agrupación
var porCategoria = await _context.Funkos
    .GroupBy(f => f.Categoria)
    .Select(g => new { Categoria = g.Key, Cantidad = g.Count() })
    .ToListAsync();

// Paginación
var pagina = 2;
var tamaño = 10;
var funkos = await _context.Funkos
    .Skip((pagina - 1) * tamaño)
    .Take(tamaño)
    .ToListAsync();

// Joins
var resultado = await _context.Funkos
    .Join(_context. Categorias,
          f => f.CategoriaId,
          c => c.Id,
          (f, c) => new { Funko = f, Categoria = c })
    .ToListAsync();

// Contar
var total = await _context.Funkos.CountAsync();
var activos = await _context.Funkos. CountAsync(f => f. Activo);

// Existencia
var existe = await _context.Funkos.AnyAsync(f => f.Nombre == "Iron Man");

// Primero o default
var funko = await _context.Funkos.FirstOrDefaultAsync(f => f.Id == 1);

// Único (lanza excepción si hay más de uno o ninguno)
var funko = await _context.Funkos.SingleAsync(f => f.Id == 1);
```

---

### Consultas SQL Nativas

```csharp
// Query SQL nativa
var funkos = await _context.Funkos
    .FromSqlRaw("SELECT * FROM funkos WHERE precio > {0}", 20)
    .ToListAsync();

// Query SQL interpolada (más segura)
var precioMinimo = 20m;
var funkos = await _context.Funkos
    . FromSqlInterpolated($"SELECT * FROM funkos WHERE precio > {precioMinimo}")
    .ToListAsync();

// Stored Procedure
var funkos = await _context.Funkos
    .FromSqlRaw("EXEC GetFunkosPorCategoria @Categoria", 
                new SqlParameter("@Categoria", "Marvel"))
    .ToListAsync();

// Ejecutar comando SQL (INSERT, UPDATE, DELETE)
var filasAfectadas = await _context.Database
    .ExecuteSqlRawAsync("UPDATE funkos SET activo = 0 WHERE precio < {0}", 10);
```

---

## Testing con EF Core

### InMemory Database

```csharp
using Microsoft.EntityFrameworkCore;
using NUnit.Framework;

[TestFixture]
public class FunkoRepositoryInMemoryTests
{
    private ApplicationDbContext _context = null!;
    private FunkoRepository _repository = null! ;

    [SetUp]
    public void Setup()
    {
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseInMemoryDatabase(databaseName:  Guid.NewGuid().ToString()) // BD única por test
            .Options;

        _context = new ApplicationDbContext(options);
        _repository = new FunkoRepository(_context);

        // Seed data
        _context.Funkos.AddRange(
            new Funko { Nombre = "Iron Man", Precio = 25m, Cantidad = 10, Categoria = "Marvel" },
            new Funko { Nombre = "Batman", Precio = 30m, Cantidad = 5, Categoria = "DC" }
        );
        _context.SaveChanges();
    }

    [TearDown]
    public void TearDown()
    {
        _context.Database.EnsureDeleted();
        _context.Dispose();
    }

    [Test]
    public async Task GetAllAsync_ReturnsAllFunkos()
    {
        // Act
        var result = await _repository.GetAllAsync();

        // Assert
        result.Should().HaveCount(2);
    }

    [Test]
    public async Task GetByIdAsync_WithValidId_ReturnsFunko()
    {
        // Arrange
        var funko = _context.Funkos.First();

        // Act
        var result = await _repository.GetByIdAsync(funko.Id);

        // Assert
        result.Should().NotBeNull();
        result!.Nombre.Should().Be(funko.Nombre);
    }

    [Test]
    public async Task AddAsync_CreatesFunko()
    {
        // Arrange
        var newFunko = new Funko { Nombre = "Spider-Man", Precio = 28m, Cantidad = 15, Categoria = "Marvel" };

        // Act
        var result = await _repository.AddAsync(newFunko);

        // Assert
        result.Id.Should().BeGreaterThan(0);
        _context.Funkos.Should().Contain(result);
    }
}
```

---

### TestContainers

Ya vimos TestContainers anteriormente.   Aquí un ejemplo específico para EF Core:

```csharp
using Testcontainers.PostgreSql;

[TestFixture]
public class FunkoRepositoryPostgreSQLTests
{
    private PostgreSqlContainer _postgres = null!;
    private ApplicationDbContext _context = null!;
    private FunkoRepository _repository = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetup()
    {
        _postgres = new PostgreSqlBuilder()
            .WithDatabase("funkosdb")
            .WithUsername("postgres")
            .WithPassword("postgres")
            .Build();

        await _postgres.StartAsync();
    }

    [SetUp]
    public async Task Setup()
    {
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgres. GetConnectionString())
            .Options;

        _context = new ApplicationDbContext(options);
        await _context.Database.MigrateAsync(); // Aplicar migraciones

        _repository = new FunkoRepository(_context);

        // Seed data
        _context. Funkos.AddRange(
            new Funko { Nombre = "Iron Man", Precio = 25m, Cantidad = 10, Categoria = "Marvel" },
            new Funko { Nombre = "Batman", Precio = 30m, Cantidad = 5, Categoria = "DC" }
        );
        await _context.SaveChangesAsync();
    }

    [TearDown]
    public async Task TearDown()
    {
        await _context.Database.EnsureDeletedAsync();
        await _context. DisposeAsync();
    }

    [OneTimeTearDown]
    public async Task OneTimeTeardown()
    {
        await _postgres.DisposeAsync();
    }

    [Test]
    public async Task GetAllAsync_ReturnsAllFunkos()
    {
        var result = await _repository.GetAllAsync();
        result.Should().HaveCount(2);
    }

    // Más tests...
}
```

---

## Práctica de Clase

**Objetivo:** Crear un sistema completo con Entity Framework Core. 

**Tareas:**

1. ✅ Crear entidades `Funko` y `Categoria` con relación Uno a Muchos
2. ✅ Configurar DbContext y migraciones
3. ✅ Implementar repositorio genérico y específico
4. ✅ Crear consultas LINQ complejas (filtros, joins, agrupaciones, paginación)
5. ✅ Implementar borrado lógico
6. ✅ Testear con InMemory y TestContainers
7. ✅ Integrar con el servicio y controlador

**Criterios de evaluación:**

- ✅ Entidades correctamente configuradas
- ✅ Migraciones funcionando
- ✅ Repositorios implementados
- ✅ Consultas LINQ optimizadas
- ✅ Tests completos (unitarios e integración)
- ✅ Integración con capa de servicio

---

## Proyecto del Curso

Puedes encontrar el proyecto con Entity Framework Core en el repositorio del curso.
- [Proyecto EntityFramework](https://github.com/joseluisgs/PersonasBasicRestNet)
- [Proyecto Integrador](https://github.com/joseluisgs/TiendaDawApi-NetCore)

---
