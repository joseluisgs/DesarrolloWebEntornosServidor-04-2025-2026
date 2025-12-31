# Resultados Avanzados en ASP.NET Core

- [Resultados Avanzados en ASP.NET Core](#resultados-avanzados-en-aspnet-core)
  - [Negociación de Contenido (Content Negotiation)](#negociación-de-contenido-content-negotiation)
    - [Configuración de XML](#configuración-de-xml)
    - [Uso desde el Cliente](#uso-desde-el-cliente)
  - [Paginación y Ordenación](#paginación-y-ordenación)
    - [Modelo de Paginación](#modelo-de-paginación)
    - [PagedList - Clase de Ayuda](#pagedlist---clase-de-ayuda)
    - [Parámetros de Paginación](#parámetros-de-paginación)
    - [Implementación en el Repositorio](#implementación-en-el-repositorio)
    - [Implementación en el Servicio](#implementación-en-el-servicio)
    - [Implementación en el Controlador](#implementación-en-el-controlador)
  - [Enlaces de Paginación en Headers](#enlaces-de-paginación-en-headers)
    - [Servicio de Enlaces de Paginación](#servicio-de-enlaces-de-paginación)
    - [Uso en el Controlador](#uso-en-el-controlador)
  - [Filtrado Dinámico con Specifications](#filtrado-dinámico-con-specifications)
    - [Patrón Specification](#patrón-specification)
    - [Implementación de Specifications](#implementación-de-specifications)
    - [Uso en el Repositorio](#uso-en-el-repositorio)
    - [Uso en el Servicio](#uso-en-el-servicio)
    - [Uso en el Controlador](#uso-en-el-controlador-1)
  - [Ejemplo Completo Integrado](#ejemplo-completo-integrado)
    - [1.  Parámetros de Consulta](#1--parámetros-de-consulta)
    - [2.  Specifications de Funkos](#2--specifications-de-funkos)
    - [3.  Repositorio](#3--repositorio)
    - [4.  Servicio](#4--servicio)
    - [5.  Controlador](#5--controlador)
  - [Optimización con IQueryable](#optimización-con-iqueryable)
    - [Uso de IQueryable](#uso-de-iqueryable)
  - [Testing de Paginación y Filtrado](#testing-de-paginación-y-filtrado)
    - [Test del Repositorio](#test-del-repositorio)
    - [Test del Controlador](#test-del-controlador)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![Advanced Results Banner](../images/banner10.png)

---

## Negociación de Contenido (Content Negotiation)

La **negociación de contenido** permite que un cliente especifique el formato de los datos que desea recibir (JSON, XML, etc.).

### Configuración de XML

**Instalar paquete NuGet:**

```bash
dotnet add package Microsoft.AspNetCore.Mvc.Formatters. Xml
```

**Configurar en Program.cs:**

```csharp
builder.Services.AddControllers()
    .AddXmlSerializerFormatters() // Habilitar XML
    .AddJsonOptions(options =>
    {
        options.JsonSerializerOptions. PropertyNamingPolicy = JsonNamingPolicy.CamelCase;
        options.JsonSerializerOptions.ReferenceHandler = ReferenceHandler.IgnoreCycles;
    });
```

---

### Uso desde el Cliente

**1.    Usando el header `Accept`:**

```http
GET /api/funkos HTTP/1.1
Accept: application/xml
```

**2.   Usando query parameter (opcional):**

```csharp
// Configurar formato por query string
builder.Services.AddControllers(options =>
{
    options. RespectBrowserAcceptHeader = true; // Respetar Accept header
    options. ReturnHttpNotAcceptable = true; // Devolver 406 si no se soporta el formato
})
.AddXmlSerializerFormatters();

// Habilitar formato por query string
builder.Services.Configure<MvcOptions>(options =>
{
    options.FormatterMappings.SetMediaTypeMappingForFormat("xml", "application/xml");
    options.FormatterMappings. SetMediaTypeMappingForFormat("json", "application/json");
});
```

**Uso:**

```http
GET /api/funkos? format=xml
GET /api/funkos?format=json
```

---

## Paginación y Ordenación

La **paginación** y **ordenación** son esenciales para:  

✅ **Eficiencia de red**:   Enviar solo datos necesarios
✅ **Experiencia de usuario**:  Datos manejables
✅ **Rendimiento del servidor**:  Reducir carga de BD y memoria
✅ **Escalabilidad**:  Manejar grandes volúmenes de datos

---

### Modelo de Paginación

**Respuesta de página:**

```csharp
namespace FunkosApi.Models. Pagination;

public record PageResponse<T>
{
    public IEnumerable<T> Content { get.  init; } = Enumerable.Empty<T>();
    public int TotalPages { get. init; }
    public long TotalElements { get. init; }
    public int PageSize { get. init; }
    public int PageNumber { get. init; }
    public int TotalPageElements { get. init; }
    public bool Empty { get. init; }
    public bool First { get. init; }
    public bool Last { get. init; }
    public string? SortBy { get. init; }
    public string? Direction { get. init; }
}
```

---

### PagedList - Clase de Ayuda

```csharp
namespace FunkosApi.Models.Pagination;

public class PagedList<T>
{
    public List<T> Items { get; }
    public int PageNumber { get; }
    public int PageSize { get; }
    public int TotalCount { get; }
    public int TotalPages => (int)Math.Ceiling(TotalCount / (double)PageSize);
    public bool HasPrevious => PageNumber > 1;
    public bool HasNext => PageNumber < TotalPages;

    public PagedList(List<T> items, int count, int pageNumber, int pageSize)
    {
        Items = items;
        TotalCount = count;
        PageNumber = pageNumber;
        PageSize = pageSize;
    }

    public static async Task<PagedList<T>> CreateAsync(
        IQueryable<T> source,
        int pageNumber,
        int pageSize,
        CancellationToken cancellationToken = default)
    {
        var count = await source.CountAsync(cancellationToken);
        var items = await source
            .Skip((pageNumber - 1) * pageSize)
            .Take(pageSize)
            .ToListAsync(cancellationToken);

        return new PagedList<T>(items, count, pageNumber, pageSize);
    }

    public PageResponse<T> ToPageResponse(string?  sortBy = null, string? direction = null)
    {
        return new PageResponse<T>
        {
            Content = Items,
            TotalPages = TotalPages,
            TotalElements = TotalCount,
            PageSize = PageSize,
            PageNumber = PageNumber,
            TotalPageElements = Items.Count,
            Empty = ! Items.Any(),
            First = PageNumber == 1,
            Last = PageNumber == TotalPages,
            SortBy = sortBy,
            Direction = direction
        };
    }
}
```

---

### Parámetros de Paginación

```csharp
namespace FunkosApi.Models.Pagination;

public record PaginationParams
{
    private const int MaxPageSize = 100;
    private int _pageSize = 10;

    public int PageNumber { get.  init; } = 1;

    public int PageSize
    {
        get => _pageSize;
        init => _pageSize = value > MaxPageSize ? MaxPageSize : value;
    }

    public string SortBy { get. init; } = "Id";
    public string Direction { get. init; } = "asc"; // "asc" o "desc"
}
```

---

### Implementación en el Repositorio

```csharp
public interface IFunkoRepository
{
    Task<PagedList<Funko>> GetAllPagedAsync(
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default);

    Task<PagedList<Funko>> GetAllPagedAsync(
        Expression<Func<Funko, bool>>?   filter,
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default);
}

public class FunkoRepository :   IFunkoRepository
{
    private readonly ApplicationDbContext _context;

    public FunkoRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    public async Task<PagedList<Funko>> GetAllPagedAsync(
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default)
    {
        var query = _context. Funkos
            .Where(f => f. Activo)
            .AsQueryable();

        return await GetPagedAsync(query, paginationParams, cancellationToken);
    }

    public async Task<PagedList<Funko>> GetAllPagedAsync(
        Expression<Func<Funko, bool>>?  filter,
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default)
    {
        var query = _context.Funkos
            .Where(f => f. Activo)
            .AsQueryable();

        if (filter is not null)
        {
            query = query.Where(filter);
        }

        return await GetPagedAsync(query, paginationParams, cancellationToken);
    }

    private async Task<PagedList<Funko>> GetPagedAsync(
        IQueryable<Funko> query,
        PaginationParams paginationParams,
        CancellationToken cancellationToken)
    {
        // Aplicar ordenación
        query = ApplySorting(query, paginationParams.SortBy, paginationParams.Direction);

        // Crear página
        return await PagedList<Funko>.CreateAsync(
            query,
            paginationParams.PageNumber,
            paginationParams.PageSize,
            cancellationToken);
    }

    private IQueryable<Funko> ApplySorting(IQueryable<Funko> query, string sortBy, string direction)
    {
        var isAscending = direction. Equals("asc", StringComparison.OrdinalIgnoreCase);

        return sortBy. ToLowerInvariant() switch
        {
            "nombre" => isAscending ?  query.OrderBy(f => f.Nombre) : query.OrderByDescending(f => f. Nombre),
            "precio" => isAscending ? query.OrderBy(f => f. Precio) : query.OrderByDescending(f => f. Precio),
            "cantidad" => isAscending ? query.OrderBy(f => f. Cantidad) : query.OrderByDescending(f => f. Cantidad),
            "categoria" => isAscending ? query.OrderBy(f => f. Categoria) : query.OrderByDescending(f => f. Categoria),
            "fechacreacion" => isAscending ? query.OrderBy(f => f.FechaCreacion) : query.OrderByDescending(f => f.FechaCreacion),
            _ => isAscending ? query.OrderBy(f => f.Id) : query.OrderByDescending(f => f.Id)
        };
    }
}
```

---

### Implementación en el Servicio

```csharp
public interface IFunkoService
{
    Task<PageResponse<FunkoResponseDto>> GetAllPagedAsync(
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default);
}

public class FunkoService :   IFunkoService
{
    private readonly IFunkoRepository _repository;

    public FunkoService(IFunkoRepository repository)
    {
        _repository = repository;
    }

    public async Task<PageResponse<FunkoResponseDto>> GetAllPagedAsync(
        PaginationParams paginationParams,
        CancellationToken cancellationToken = default)
    {
        var pagedFunkos = await _repository.GetAllPagedAsync(paginationParams, cancellationToken);

        return pagedFunkos. ToPageResponse(
            paginationParams.SortBy,
            paginationParams.Direction
        );
    }
}
```

---

### Implementación en el Controlador

```csharp
[ApiController]
[Route("api/[controller]")]
public class FunkosController :   ControllerBase
{
    private readonly IFunkoService _service;

    public FunkosController(IFunkoService service)
    {
        _service = service;
    }

    /// <summary>
    /// Obtiene todos los funkos con paginación y ordenación
    /// </summary>
    [HttpGet]
    [ProducesResponseType(typeof(PageResponse<FunkoResponseDto>), StatusCodes.Status200OK)]
    public async Task<ActionResult<PageResponse<FunkoResponseDto>>> GetAll(
        [FromQuery] int pageNumber = 1,
        [FromQuery] int pageSize = 10,
        [FromQuery] string sortBy = "Id",
        [FromQuery] string direction = "asc")
    {
        var paginationParams = new PaginationParams
        {
            PageNumber = pageNumber,
            PageSize = pageSize,
            SortBy = sortBy,
            Direction = direction
        };

        var result = await _service.GetAllPagedAsync(paginationParams);

        return Ok(result);
    }
}
```

**Ejemplo de llamada:**

```http
GET /api/funkos? pageNumber=2&pageSize=20&sortBy=nombre&direction=asc
```

**Respuesta:**

```json
{
  "content": [
    {
      "id": 21,
      "nombre": "Batman",
      "precio": 29.99,
      "cantidad":  10,
      "categoria": "DC",
      "imagen": "batman.jpg"
    },
    {
      "id": 22,
      "nombre": "Iron Man",
      "precio": 34.99,
      "cantidad":  5,
      "categoria":  "Marvel",
      "imagen":  "ironman.jpg"
    }
    // ...   más funkos
  ],
  "totalPages": 5,
  "totalElements": 100,
  "pageSize": 20,
  "pageNumber": 2,
  "totalPageElements": 20,
  "empty": false,
  "first":  false,
  "last": false,
  "sortBy": "nombre",
  "direction": "asc"
}
```

---

## Enlaces de Paginación en Headers

Incluir enlaces de paginación en los **headers** sigue el estándar **HATEOAS** y tiene ventajas:  

✅ **Conformidad con estándares REST**
✅ **Separación de preocupaciones**:   El body solo contiene datos
✅ **Eficiencia**:  Headers se transfieren primero
✅ **Mejor cacheabilidad**
✅ **Flexibilidad para el cliente**

---

### Servicio de Enlaces de Paginación

```csharp
namespace FunkosApi.Services. Pagination;

public interface IPaginationLinksService
{
    string CreateLinkHeader<T>(PagedList<T> pagedList, HttpRequest request);
}

public class PaginationLinksService :   IPaginationLinksService
{
    public string CreateLinkHeader<T>(PagedList<T> pagedList, HttpRequest request)
    {
        var links = new List<string>();

        // Link a la primera página
        if (pagedList.PageNumber > 1)
        {
            links.Add(CreateLink(request, 1, pagedList.PageSize, "first"));
        }

        // Link a la página anterior
        if (pagedList.HasPrevious)
        {
            links.Add(CreateLink(request, pagedList.PageNumber - 1, pagedList. PageSize, "prev"));
        }

        // Link a la página siguiente
        if (pagedList.HasNext)
        {
            links.Add(CreateLink(request, pagedList.PageNumber + 1, pagedList. PageSize, "next"));
        }

        // Link a la última página
        if (pagedList.PageNumber < pagedList.TotalPages)
        {
            links.Add(CreateLink(request, pagedList.TotalPages, pagedList.PageSize, "last"));
        }

        return string.Join(", ", links);
    }

    private string CreateLink(HttpRequest request, int pageNumber, int pageSize, string rel)
    {
        var scheme = request.Scheme;
        var host = request.Host. Value;
        var path = request.Path.Value;

        var queryParams = new Dictionary<string, string>
        {
            ["pageNumber"] = pageNumber.ToString(),
            ["pageSize"] = pageSize.ToString()
        };

        // Agregar otros parámetros de query si existen
        foreach (var key in request.Query.Keys)
        {
            if (key != "pageNumber" && key != "pageSize")
            {
                queryParams[key] = request.Query[key].ToString();
            }
        }

        var queryString = string.Join("&", queryParams.Select(kvp => $"{kvp.Key}={kvp.Value}"));
        var url = $"{scheme}://{host}{path}?{queryString}";

        return $"<{url}>; rel=\"{rel}\"";
    }
}
```

**Registrar en Program.cs:**

```csharp
builder.Services.AddScoped<IPaginationLinksService, PaginationLinksService>();
```

---

### Uso en el Controlador

```csharp
[ApiController]
[Route("api/[controller]")]
public class FunkosController :  ControllerBase
{
    private readonly IFunkoService _service;
    private readonly IPaginationLinksService _paginationLinksService;

    public FunkosController(IFunkoService service, IPaginationLinksService paginationLinksService)
    {
        _service = service;
        _paginationLinksService = paginationLinksService;
    }

    [HttpGet]
    public async Task<ActionResult<PageResponse<FunkoResponseDto>>> GetAll(
        [FromQuery] PaginationParams paginationParams)
    {
        var pagedFunkos = await _service.GetAllPagedAsync(paginationParams);

        // Crear link header
        var linkHeader = _paginationLinksService.CreateLinkHeader(pagedFunkos, Request);

        // Agregar header a la respuesta
        Response. Headers.Add("Link", linkHeader);

        return Ok(pagedFunkos. ToPageResponse(paginationParams.SortBy, paginationParams.Direction));
    }
}
```

**Respuesta (headers):**

```http
HTTP/1.1 200 OK
Content-Type: application/json
Link: <https://localhost:5001/api/funkos?pageNumber=1&pageSize=10>; rel="first", <https://localhost:5001/api/funkos?pageNumber=1&pageSize=10>; rel="prev", <https://localhost:5001/api/funkos? pageNumber=3&pageSize=10>; rel="next", <https://localhost:5001/api/funkos? pageNumber=10&pageSize=10>; rel="last"
```

---

## Filtrado Dinámico con Specifications

El **patrón Specification** permite construir consultas dinámicas y reutilizables.  

### Patrón Specification

```csharp
namespace FunkosApi.Specifications;

public interface ISpecification<T>
{
    Expression<Func<T, bool>> Criteria { get; }
    List<Expression<Func<T, object>>> Includes { get; }
    Expression<Func<T, object>>?   OrderBy { get; }
    Expression<Func<T, object>>?  OrderByDescending { get; }
}

public abstract class BaseSpecification<T> :   ISpecification<T>
{
    public Expression<Func<T, bool>>?   Criteria { get; }
    public List<Expression<Func<T, object>>> Includes { get; } = new();
    public Expression<Func<T, object>>?   OrderBy { get; private set; }
    public Expression<Func<T, object>>?   OrderByDescending { get; private set; }

    protected BaseSpecification(Expression<Func<T, bool>>?   criteria = null)
    {
        Criteria = criteria;
    }

    protected void AddInclude(Expression<Func<T, object>> includeExpression)
    {
        Includes.Add(includeExpression);
    }

    protected void ApplyOrderBy(Expression<Func<T, object>> orderByExpression)
    {
        OrderBy = orderByExpression;
    }

    protected void ApplyOrderByDescending(Expression<Func<T, object>> orderByDescExpression)
    {
        OrderByDescending = orderByDescExpression;
    }
}
```

---

### Implementación de Specifications

```csharp
namespace FunkosApi.Specifications;

public class FunkosSpecification :   BaseSpecification<Funko>
{
    public FunkosSpecification(FunkoFilterParams filterParams) 
        : base(CreateCriteria(filterParams))
    {
        // Includes (eager loading)
        // AddInclude(f => f.Categoria);

        // Ordenación
        if (!  string.IsNullOrWhiteSpace(filterParams.SortBy))
        {
            ApplySorting(filterParams.SortBy, filterParams.Direction);
        }
    }

    private static Expression<Func<Funko, bool>> CreateCriteria(FunkoFilterParams filterParams)
    {
        Expression<Func<Funko, bool>> criteria = f => f.Activo;

        // Filtro por nombre
        if (!string.IsNullOrWhiteSpace(filterParams.Nombre))
        {
            var nombre = filterParams.Nombre. ToLower();
            var nombreFilter = (Expression<Func<Funko, bool>>)(f => f.Nombre.ToLower().Contains(nombre));
            criteria = CombineExpressions(criteria, nombreFilter);
        }

        // Filtro por categoría
        if (!string.IsNullOrWhiteSpace(filterParams.Categoria))
        {
            var categoria = filterParams.Categoria;
            var categoriaFilter = (Expression<Func<Funko, bool>>)(f => f.Categoria == categoria);
            criteria = CombineExpressions(criteria, categoriaFilter);
        }

        // Filtro por rango de precio
        if (filterParams.PrecioMin. HasValue)
        {
            var precioMin = filterParams.PrecioMin.Value;
            var precioMinFilter = (Expression<Func<Funko, bool>>)(f => f.Precio >= precioMin);
            criteria = CombineExpressions(criteria, precioMinFilter);
        }

        if (filterParams.PrecioMax.HasValue)
        {
            var precioMax = filterParams.PrecioMax.Value;
            var precioMaxFilter = (Expression<Func<Funko, bool>>)(f => f.Precio <= precioMax);
            criteria = CombineExpressions(criteria, precioMaxFilter);
        }

        return criteria;
    }

    private static Expression<Func<T, bool>> CombineExpressions<T>(
        Expression<Func<T, bool>> first,
        Expression<Func<T, bool>> second)
    {
        var parameter = Expression.Parameter(typeof(T));

        var leftVisitor = new ReplaceExpressionVisitor(first. Parameters[0], parameter);
        var left = leftVisitor.Visit(first.Body);

        var rightVisitor = new ReplaceExpressionVisitor(second.Parameters[0], parameter);
        var right = rightVisitor.Visit(second.Body);

        return Expression. Lambda<Func<T, bool>>(Expression.AndAlso(left!, right! ), parameter);
    }

    private void ApplySorting(string sortBy, string direction)
    {
        var isAscending = direction. Equals("asc", StringComparison.OrdinalIgnoreCase);

        switch (sortBy.ToLowerInvariant())
        {
            case "nombre":
                if (isAscending) ApplyOrderBy(f => f. Nombre);
                else ApplyOrderByDescending(f => f.Nombre);
                break;
            case "precio":
                if (isAscending) ApplyOrderBy(f => f. Precio);
                else ApplyOrderByDescending(f => f.Precio);
                break;
            case "cantidad":
                if (isAscending) ApplyOrderBy(f => f.Cantidad);
                else ApplyOrderByDescending(f => f.Cantidad);
                break;
            case "categoria":
                if (isAscending) ApplyOrderBy(f => f.Categoria);
                else ApplyOrderByDescending(f => f.Categoria);
                break;
            default:
                if (isAscending) ApplyOrderBy(f => f.Id);
                else ApplyOrderByDescending(f => f.Id);
                break;
        }
    }
}

// Visitor para combinar expresiones
internal class ReplaceExpressionVisitor :   ExpressionVisitor
{
    private readonly Expression _oldValue;
    private readonly Expression _newValue;

    public ReplaceExpressionVisitor(Expression oldValue, Expression newValue)
    {
        _oldValue = oldValue;
        _newValue = newValue;
    }

    public override Expression?   Visit(Expression?   node)
    {
        return node == _oldValue ? _newValue :  base.Visit(node);
    }
}
```

---

### Uso en el Repositorio

```csharp
public async Task<PagedList<Funko>> GetAllPagedAsync(
    ISpecification<Funko> specification,
    PaginationParams paginationParams,
    CancellationToken cancellationToken = default)
{
    var query = ApplySpecification(specification);

    return await PagedList<Funko>.CreateAsync(
        query,
        paginationParams.PageNumber,
        paginationParams.PageSize,
        cancellationToken);
}

private IQueryable<Funko> ApplySpecification(ISpecification<Funko> spec)
{
    var query = _context.Funkos.AsQueryable();

    if (spec.Criteria is not null)
    {
        query = query.Where(spec. Criteria);
    }

    query = spec.Includes.Aggregate(query, (current, include) => current.Include(include));

    if (spec.OrderBy is not null)
    {
        query = query.OrderBy(spec.OrderBy);
    }
    else if (spec.OrderByDescending is not null)
    {
        query = query.OrderByDescending(spec.OrderByDescending);
    }

    return query;
}
```

---

### Uso en el Servicio

```csharp
public async Task<PageResponse<FunkoResponseDto>> GetAllPagedAsync(
    FunkoFilterParams filterParams,
    PaginationParams paginationParams,
    CancellationToken cancellationToken = default)
{
    var specification = new FunkosSpecification(filterParams);

    var pagedFunkos = await _repository.GetAllPagedAsync(
        specification,
        paginationParams,
        cancellationToken);

    return pagedFunkos.ToPageResponse(paginationParams.SortBy, paginationParams.Direction);
}
```

---

### Uso en el Controlador

```csharp
[HttpGet]
public async Task<ActionResult<PageResponse<FunkoResponseDto>>> GetAll(
    [FromQuery] FunkoFilterParams filterParams,
    [FromQuery] PaginationParams paginationParams)
{
    var result = await _service.GetAllPagedAsync(filterParams, paginationParams);

    var linkHeader = _paginationLinksService.CreateLinkHeader(result, Request);
    Response.Headers.Add("Link", linkHeader);

    return Ok(result);
}
```

---

## Ejemplo Completo Integrado

### 1.  Parámetros de Consulta

```csharp
public record FunkoFilterParams
{
    public string? Nombre { get.  init; }
    public string?   Categoria { get. init; }
    public decimal?  PrecioMin { get. init; }
    public decimal?  PrecioMax { get. init; }
    public string SortBy { get. init; } = "Id";
    public string Direction { get. init; } = "asc";
}
```

---

### 2.  Specifications de Funkos

```csharp
public class FunkosSpecification :  BaseSpecification<Funko>
{
    public FunkosSpecification(FunkoFilterParams filterParams)
        : base(BuildCriteria(filterParams))
    {
        ApplySorting(filterParams.SortBy, filterParams.Direction);
    }

    private static Expression<Func<Funko, bool>> BuildCriteria(FunkoFilterParams filter)
    {
        Expression<Func<Funko, bool>> criteria = f => f. Activo;

        if (!string.IsNullOrWhiteSpace(filter.Nombre))
        {
            var nombre = filter.Nombre.ToLower();
            criteria = criteria.And(f => f.Nombre.ToLower().Contains(nombre));
        }

        if (!string.IsNullOrWhiteSpace(filter. Categoria))
        {
            criteria = criteria.And(f => f.Categoria == filter.Categoria);
        }

        if (filter.PrecioMin.HasValue)
        {
            criteria = criteria.And(f => f.Precio >= filter.PrecioMin.Value);
        }

        if (filter.PrecioMax.HasValue)
        {
            criteria = criteria.And(f => f.Precio <= filter.PrecioMax.Value);
        }

        return criteria;
    }

    // Métodos de ayuda para combinar expresiones
}

// Extension para combinar expresiones LINQ
public static class ExpressionExtensions
{
    public static Expression<Func<T, bool>> And<T>(
        this Expression<Func<T, bool>> first,
        Expression<Func<T, bool>> second)
    {
        var parameter = Expression.Parameter(typeof(T));

        var leftVisitor = new ReplaceExpressionVisitor(first.Parameters[0], parameter);
        var left = leftVisitor.Visit(first.Body);

        var rightVisitor = new ReplaceExpressionVisitor(second.Parameters[0], parameter);
        var right = rightVisitor.Visit(second.Body);

        return Expression.Lambda<Func<T, bool>>(Expression.AndAlso(left!, right!), parameter);
    }
}
```

---

### 3.  Repositorio

```csharp
public async Task<PagedList<Funko>> GetAllPagedAsync(
    ISpecification<Funko> specification,
    PaginationParams paginationParams,
    CancellationToken cancellationToken = default)
{
    var query = ApplySpecification(specification);

    return await PagedList<Funko>.CreateAsync(
        query,
        paginationParams.PageNumber,
        paginationParams.PageSize,
        cancellationToken);
}
```

---

### 4.  Servicio

```csharp
public async Task<PagedList<Funko>> GetAllPagedAsync(
    FunkoFilterParams filterParams,
    PaginationParams paginationParams,
    CancellationToken cancellationToken = default)
{
    var specification = new FunkosSpecification(filterParams);

    return await _repository.GetAllPagedAsync(
        specification,
        paginationParams,
        cancellationToken);
}
```

---

### 5.  Controlador

```csharp
/// <summary>
/// Obtiene funkos con filtrado, paginación y ordenación
/// </summary>
[HttpGet]
[ProducesResponseType(typeof(PageResponse<FunkoResponseDto>), StatusCodes.Status200OK)]
public async Task<ActionResult<PageResponse<FunkoResponseDto>>> GetAll(
    [FromQuery] string?   nombre,
    [FromQuery] string?   categoria,
    [FromQuery] decimal?  precioMin,
    [FromQuery] decimal?   precioMax,
    [FromQuery] int pageNumber = 1,
    [FromQuery] int pageSize = 10,
    [FromQuery] string sortBy = "Id",
    [FromQuery] string direction = "asc")
{
    var filterParams = new FunkoFilterParams
    {
        Nombre = nombre,
        Categoria = categoria,
        PrecioMin = precioMin,
        PrecioMax = precioMax,
        SortBy = sortBy,
        Direction = direction
    };

    var paginationParams = new PaginationParams
    {
        PageNumber = pageNumber,
        PageSize = pageSize,
        SortBy = sortBy,
        Direction = direction
    };

    var pagedFunkos = await _service.GetAllPagedAsync(filterParams, paginationParams);

    // Agregar links de paginación
    var linkHeader = _paginationLinksService.CreateLinkHeader(pagedFunkos, Request);
    Response.Headers.Add("Link", linkHeader);

    var response = pagedFunkos.ToPageResponse(sortBy, direction);

    return Ok(response);
}
```

**Ejemplo de llamada:**

```http
GET /api/funkos?nombre=iron&categoria=Marvel&precioMin=20&precioMax=50&pageNumber=2&pageSize=10&sortBy=precio&direction=desc
```

---

## Optimización con IQueryable

### Uso de IQueryable

`IQueryable` permite construir consultas que se ejecutan en la base de datos (no en memoria). 

```csharp
public async Task<PagedList<Funko>> GetAllPagedAsync(
    Expression<Func<Funko, bool>>?  filter = null,
    Func<IQueryable<Funko>, IOrderedQueryable<Funko>>?  orderBy = null,
    PaginationParams?   paginationParams = null,
    CancellationToken cancellationToken = default)
{
    IQueryable<Funko> query = _context.Funkos;

    if (filter is not null)
    {
        query = query.Where(filter);
    }

    if (orderBy is not null)
    {
        query = orderBy(query);
    }

    paginationParams ??= new PaginationParams();

    return await PagedList<Funko>.CreateAsync(
        query,
        paginationParams.PageNumber,
        paginationParams.PageSize,
        cancellationToken);
}
```

---

## Testing de Paginación y Filtrado

### Test del Repositorio

```csharp
[Test]
public async Task GetAllPagedAsync_ReturnsPaginatedResults()
{
    // Arrange
    var paginationParams = new PaginationParams { PageNumber = 1, PageSize = 5 };

    // Act
    var result = await _repository.GetAllPagedAsync(paginationParams);

    // Assert
    result.Items.Should().HaveCountLessOrEqualTo(5);
    result.PageNumber.Should().Be(1);
    result.PageSize.Should().Be(5);
}

[Test]
public async Task GetAllPagedAsync_WithFilter_ReturnsFilteredResults()
{
    // Arrange
    var filterParams = new FunkoFilterParams { Categoria = "Marvel" };
    var specification = new FunkosSpecification(filterParams);
    var paginationParams = new PaginationParams { PageNumber = 1, PageSize = 10 };

    // Act
    var result = await _repository.GetAllPagedAsync(specification, paginationParams);

    // Assert
    result.Items.Should().AllSatisfy(f => f. Categoria. Should().Be("Marvel"));
}
```

---

### Test del Controlador

```csharp
[Test]
public async Task GetAll_WithPagination_ReturnsPagedResponse()
{
    // Arrange
    var paginationParams = new PaginationParams { PageNumber = 1, PageSize = 10 };

    _serviceMock
        .Setup(s => s. GetAllPagedAsync(It. IsAny<FunkoFilterParams>(), paginationParams, default))
        .ReturnsAsync(new PagedList<Funko>(new List<Funko>(), 0, 1, 10));

    // Act
    var result = await _controller.GetAll(null, null, null, null, 1, 10, "Id", "asc");

    // Assert
    var okResult = result.Result. Should().BeOfType<OkObjectResult>().Subject;
    var response = okResult.Value. Should().BeAssignableTo<PageResponse<FunkoResponseDto>>().Subject;

    response. PageNumber.Should().Be(1);
    response.PageSize. Should().Be(10);
}
```

---

## Buenas Prácticas

✅ **Limitar `PageSize` máximo**:  Prevenir consultas muy grandes

✅ **Índices en BD**:  Crear índices en columnas usadas para ordenar/filtrar

✅ **AsNoTracking()**:  Usar en consultas de solo lectura para mejorar rendimiento

✅ **Proyecciones**:  Usar `.Select()` para devolver solo campos necesarios

✅ **Caching**:  Cachear resultados de consultas frecuentes

✅ **Validación**:  Validar parámetros de paginación y filtrado

✅ **Documentación Swagger**:  Documentar parámetros de query claramente

✅ **Tests**:  Testear casos límite (página vacía, filtros sin resultados, etc.)

---

## Práctica de Clase

**Objetivo:** Implementar paginación, filtrado y ordenación completa.  

**Tareas:**

1. ✅ Crear `PageResponse<T>`, `PagedList<T>` y `PaginationParams`
2. ✅ Implementar paginación en repositorio, servicio y controlador
3. ✅ Crear `FunkoFilterParams` con filtros (nombre, categoría, rango de precio)
4. ✅ Implementar patrón Specification para filtrado dinámico
5. ✅ Implementar ordenación por múltiples campos
6. ✅ Crear servicio `IPaginationLinksService` para links en headers
7. ✅ Configurar negociación de contenido (XML/JSON)
8. ✅ Testear todas las funcionalidades

**Criterios de evaluación:**

- ✅ Paginación funciona correctamente
- ✅ Filtros funcionan de forma combinada
- ✅ Ordenación funciona correctamente
- ✅ Links de paginación en headers
- ✅ Negociación de contenido (XML/JSON)
- ✅ Tests completos (unitarios e integración)
- ✅ Documentación Swagger clara

---

## Proyecto del Curso

Puedes encontrar el proyecto con paginación, filtrado y ordenación en el repositorio de GitHub.