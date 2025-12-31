# Patrones y Arquitecturas en . NET

---

- [Patrones y Arquitecturas en . NET](#patrones-y-arquitecturas-en--net)
  - [Principios SOLID](#principios-solid)
  - [Patrones de Diseño](#patrones-de-diseño)
  - [Arquitecturas Software](#arquitecturas-software)
  - [Arquitecturas en aplicaciones .NET modernas](#arquitecturas-en-aplicaciones-net-modernas)
  - [APIs Web en ASP.NET Core](#apis-web-en-aspnet-core)

---

## Principios SOLID

Los cinco principios SOLID son un conjunto de reglas y mejores prácticas para el diseño de software orientado a objetos que aplican perfectamente en C# y .NET.

![Solid](../images/solid-principles.jpg)

Los principios son:

1. **Principio de responsabilidad única (Single Responsibility Principle, SRP)**: Una clase debe tener una, y solo una, razón para cambiar. Esto significa que una clase debe tener solo una tarea o responsabilidad.

    ```csharp
    // ❌ MAL - Múltiples responsabilidades
    public class Informe
    {
        public void GenerarInforme()
        {
            // Lógica para generar el informe
        }

        public void ImprimirInforme()
        {
            // Lógica para imprimir el informe
        }

        public void EnviarPorEmail()
        {
            // Lógica para enviar por email
        }
    }

    // ✅ BIEN - Una sola responsabilidad por clase
    public class Informe
    {
        public void Generar()
        {
            // Lógica para generar el informe
        }
    }

    public class ImprimirInforme
    {
        public void Imprimir(Informe informe)
        {
            // Lógica para imprimir el informe
        }
    }

    public class EmailService
    {
        public void EnviarInforme(Informe informe, string destinatario)
        {
            // Lógica para enviar por email
        }
    }
    ```

2. **Principio abierto/cerrado (Open/Closed Principle, OCP)**: Las entidades de software (clases, módulos, funciones, etc.) deben estar abiertas para la extensión, pero cerradas para la modificación.

    ```csharp
    // ❌ MAL - Necesita modificarse para añadir nuevas formas
    public class CalculadorArea
    {
        public double Calcular(object forma)
        {
            if (forma is Circulo c)
                return Math.PI * c.Radio * c.Radio;
            else if (forma is Cuadrado cu)
                return cu.Lado * cu.Lado;
            else
                throw new NotSupportedException();
        }
    }

    // ✅ BIEN - Abierto para extensión, cerrado para modificación
    public abstract class Forma
    {
        public abstract double CalcularArea();
    }

    public class Circulo :  Forma
    {
        public double Radio { get; set; }

        public override double CalcularArea()
        {
            return Math.PI * Radio * Radio;
        }
    }

    public class Cuadrado : Forma
    {
        public double Lado { get; set; }

        public override double CalcularArea()
        {
            return Lado * Lado;
        }
    }

    // Para añadir una nueva forma, solo extendemos, no modificamos
    public class Triangulo : Forma
    {
        public double Base { get; set; }
        public double Altura { get; set; }

        public override double CalcularArea()
        {
            return (Base * Altura) / 2;
        }
    }
    ```

3. **Principio de sustitución de Liskov (Liskov Substitution Principle, LSP)**: Los objetos de una superclase deben poder ser reemplazados por objetos de una subclase sin afectar la corrección del programa. 

    ```csharp
    // ❌ MAL - Violación del LSP
    public class Pajaro
    {
        public virtual void Volar()
        {
            Console.WriteLine("El pájaro vuela");
        }
    }

    public class Pinguino : Pajaro
    {
        public override void Volar()
        {
            throw new NotSupportedException("Los pingüinos no pueden volar");
        }
    }

    // ✅ BIEN - Respeta el LSP
    public abstract class Pajaro
    {
        public abstract void Moverse();
    }

    public interface IPajaroVolador
    {
        void Volar();
    }

    public class Paloma : Pajaro, IPajaroVolador
    {
        public override void Moverse()
        {
            Volar();
        }

        public void Volar()
        {
            Console.WriteLine("La paloma vuela");
        }
    }

    public class Pinguino :  Pajaro
    {
        public override void Moverse()
        {
            Console.WriteLine("El pingüino nada");
        }
    }
    ```

4. **Principio de segregación de interfaces (Interface Segregation Principle, ISP)**: Los clientes no deben ser forzados a depender de interfaces que no usan. 

    ```csharp
    // ❌ MAL - Interfaz demasiado grande
    public interface ITrabajador
    {
        void Trabajar();
        void Comer();
        void Dormir();
        void Cobrar();
    }

    // ✅ BIEN - Interfaces segregadas
    public interface ITrabajable
    {
        void Trabajar();
    }

    public interface IAlimentable
    {
        void Comer();
    }

    public interface IDescansable
    {
        void Dormir();
    }

    public interface IRemunerado
    {
        void Cobrar();
    }

    // Un empleado humano implementa todas
    public class Empleado : ITrabajable, IAlimentable, IDescansable, IRemunerado
    {
        public void Trabajar() { /* ...  */ }
        public void Comer() { /* ... */ }
        public void Dormir() { /* ... */ }
        public void Cobrar() { /* ... */ }
    }

    // Un robot solo implementa las que necesita
    public class Robot :  ITrabajable
    {
        public void Trabajar() { /* ... */ }
    }
    ```

5. **Principio de inversión de dependencias (Dependency Inversion Principle, DIP)**: Los módulos de alto nivel no deben depender de los módulos de bajo nivel.  Ambos deben depender de abstracciones.

    ```csharp
    // ❌ MAL - Dependencia directa de implementación concreta
    public class Aplicacion
    {
        private MySqlDatabase _database = new MySqlDatabase();

        public void GuardarDatos(string datos)
        {
            _database.Guardar(datos);
        }
    }

    // ✅ BIEN - Inversión de dependencias con inyección
    public interface IDatabase
    {
        void Guardar(string datos);
    }

    public class MySqlDatabase :  IDatabase
    {
        public void Guardar(string datos)
        {
            Console.WriteLine($"Guardando en MySQL: {datos}");
        }
    }

    public class PostgreSqlDatabase : IDatabase
    {
        public void Guardar(string datos)
        {
            Console.WriteLine($"Guardando en PostgreSQL: {datos}");
        }
    }

    public class Aplicacion
    {
        private readonly IDatabase _database;

        // Inyección de dependencias
        public Aplicacion(IDatabase database)
        {
            _database = database;
        }

        public void GuardarDatos(string datos)
        {
            _database.Guardar(datos);
        }
    }

    // Uso con inyección de dependencias
    var app = new Aplicacion(new PostgreSqlDatabase());
    app.GuardarDatos("Datos importantes");
    ```

---

## Patrones de Diseño

Los patrones de diseño son soluciones probadas a problemas comunes en el desarrollo de software. En .NET, estos patrones se implementan aprovechando las características modernas de C#.

**Patrones principales:**

1. **Patrones de creación**: Controlan cómo se crean los objetos. 
   - **Singleton**: Garantiza una única instancia de una clase. 
   - **Factory Method**:  Crea objetos sin especificar la clase exacta.
   - **Abstract Factory**: Crea familias de objetos relacionados.
   - **Builder**: Construye objetos complejos paso a paso.

2. **Patrones estructurales**: Definen cómo se componen clases y objetos.
   - **Adapter**: Permite que interfaces incompatibles trabajen juntas. 
   - **Decorator**:  Añade funcionalidad a objetos dinámicamente.
   - **Proxy**: Proporciona un sustituto o placeholder de otro objeto.
   - **Composite**: Compone objetos en estructuras de árbol.

3. **Patrones de comportamiento**: Definen cómo los objetos interactúan y se comunican.
   - **Observer**: Define una dependencia uno-a-muchos entre objetos.
   - **Strategy**: Define una familia de algoritmos intercambiables.
   - **Template Method**: Define el esqueleto de un algoritmo. 
   - **Command**: Encapsula una petición como un objeto.

**Ejemplo de patrón Strategy con C# moderno:**

```csharp
// Estrategia de cálculo de precios
public interface IEstrategiaDescuento
{
    decimal CalcularDescuento(decimal precioBase);
}

public class DescuentoNormal : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal precioBase) => precioBase * 0.05m;
}

public class DescuentoVIP : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal precioBase) => precioBase * 0.20m;
}

public class DescuentoEmpleado : IEstrategiaDescuento
{
    public decimal CalcularDescuento(decimal precioBase) => precioBase * 0.30m;
}

public class CarritoCompra(IEstrategiaDescuento estrategiaDescuento)
{
    private readonly IEstrategiaDescuento _estrategiaDescuento = estrategiaDescuento;

    public decimal CalcularTotal(decimal precioBase)
    {
        var descuento = _estrategiaDescuento.CalcularDescuento(precioBase);
        return precioBase - descuento;
    }
}

// Uso
var carritoNormal = new CarritoCompra(new DescuentoNormal());
var totalNormal = carritoNormal. CalcularTotal(1000m); // 950

var carritoVIP = new CarritoCompra(new DescuentoVIP());
var totalVIP = carritoVIP. CalcularTotal(1000m); // 800
```

Puedes aprender más en [Refactoring Guru](https://refactoring.guru/es/design-patterns).

---

## Arquitecturas Software

Una arquitectura de software define la estructura organizativa fundamental de un sistema.  En .NET, las arquitecturas más comunes son:

![Arquitect](../images/arquitecturas.jpeg)

1. **Arquitectura monolítica**:  Todos los componentes en un solo bloque.
   - **Ventajas**: Simple de desarrollar y desplegar inicialmente.
   - **Desventajas**: Difícil de escalar y mantener a largo plazo.

2. **Arquitectura en capas (Layered Architecture)**: Divide la aplicación en capas lógicas.
   ```
   ┌─────────────────────────┐
   │  Presentation Layer     │  (Controllers, Views)
   ├─────────────────────────┤
   │  Application/Service    │  (Business Logic)
   ├─────────────────────────┤
   │  Domain Layer           │  (Entities, Interfaces)
   ├─────────────────────────┤
   │  Infrastructure Layer   │  (Data Access, External Services)
   └─────────────────────────┘
   ```

3. **Clean Architecture (Arquitectura Limpia)**: Separa las preocupaciones en círculos concéntricos.
   ```
   ┌─────────────────────────────────────┐
   │  External (UI, DB, Web)             │
   │  ┌───────────────────────────────┐  │
   │  │  Infrastructure               │  │
   │  │  ┌─────────────────────────┐ │  │
   │  │  │  Application (Use Cases)│ │  │
   │  │  │  ┌───────────────────┐  │ │  │
   │  │  │  │  Domain (Entities)│  │ │  │
   │  │  │  └───────────────────┘  │ │  │
   │  │  └─────────────────────────┘ │  │
   │  └───────────────────────────────┘  │
   └─────────────────────────────────────┘
   ```

4. **Microservicios**: Servicios independientes que se comunican entre sí.
   - Cada microservicio es autónomo y se puede desplegar independientemente.
   - Comunicación mediante HTTP/REST, gRPC o mensajería asíncrona.

---

## Arquitecturas en aplicaciones .NET modernas

**Ejemplo de Clean Architecture en ASP.NET Core:**

```
MiAplicacion/
├── src/
│   ├── MiAplicacion. Domain/           # Entidades y lógica de negocio pura
│   │   ├── Entities/
│   │   │   ├── Producto.cs
│   │   │   └── Pedido.cs
│   │   ├── Interfaces/
│   │   │   └── IProductoRepository.cs
│   │   └── ValueObjects/
│   │       └── Precio.cs
│   │
│   ├── MiAplicacion.Application/      # Casos de uso y lógica de aplicación
│   │   ├── DTOs/
│   │   │   └── ProductoDto.cs
│   │   ├── Services/
│   │   │   └── ProductoService.cs
│   │   └── Interfaces/
│   │       └── IProductoService.cs
│   │
│   ├── MiAplicacion.Infrastructure/   # Implementaciones de acceso a datos
│   │   ├── Data/
│   │   │   ├── ApplicationDbContext.cs
│   │   │   └── Repositories/
│   │   │       └── ProductoRepository.cs
│   │   └── ExternalServices/
│   │       └── EmailService.cs
│   │
│   └── MiAplicacion.Api/              # API REST (Presentation Layer)
│       ├── Controllers/
│       │   └── ProductosController.cs
│       ├── Program.cs
│       └── appsettings.json
│
└── tests/
    ├── MiAplicacion. UnitTests/
    └── MiAplicacion.IntegrationTests/
```

**Configuración de inyección de dependencias (Program.cs):**

```csharp
using MiAplicacion.Application. Interfaces;
using MiAplicacion.Application.Services;
using MiAplicacion.Domain.Interfaces;
using MiAplicacion.Infrastructure.Data;
using MiAplicacion. Infrastructure.Data.Repositories;
using Microsoft.EntityFrameworkCore;

var builder = WebApplication.CreateBuilder(args);

// Configurar servicios
builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Configurar DbContext
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(builder.Configuration. GetConnectionString("DefaultConnection")));

// Inyección de dependencias - Repositorios
builder.Services.AddScoped<IProductoRepository, ProductoRepository>();

// Inyección de dependencias - Servicios
builder.Services.AddScoped<IProductoService, ProductoService>();

// Configurar AutoMapper
builder.Services.AddAutoMapper(AppDomain.CurrentDomain.GetAssemblies());

// Configurar FluentValidation
builder.Services. AddValidatorsFromAssemblyContaining<ProductoDtoValidator>();

var app = builder.Build();

// Configurar pipeline HTTP
if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();

app.Run();
```

**Ejemplo de arquitectura de microservicios en . NET:**

![netflix](../images/netflix.gif)

```
Sistema de E-commerce/
├── ApiGateway/                    # API Gateway (Ocelot/YARP)
│   └── gateway. json
│
├── Servicios/
│   ├── ProductoCatalogo. Api/      # Microservicio de catálogo
│   │   ├── Controllers/
│   │   └── Program.cs
│   │
│   ├── Pedidos.Api/               # Microservicio de pedidos
│   │   ├── Controllers/
│   │   └── Program.cs
│   │
│   ├── Inventario.Api/            # Microservicio de inventario
│   │   ├── Controllers/
│   │   └── Program. cs
│   │
│   └── Notificaciones.Api/        # Microservicio de notificaciones
│       ├── Controllers/
│       └── Program. cs
│
└── Infrastructure/
    ├── MessageBus/                # RabbitMQ/Azure Service Bus
    └── SharedKernel/              # Código compartido entre servicios
```

**Comunicación entre microservicios con Refit:**

```csharp
// Definir el cliente del microservicio
public interface IInventarioApi
{
    [Get("/api/inventario/{productoId}")]
    Task<int> ObtenerStock(int productoId);

    [Post("/api/inventario/reservar")]
    Task<bool> ReservarStock([Body] ReservaRequest request);
}

// Registrar en Program.cs
builder.Services.AddRefitClient<IInventarioApi>()
    .ConfigureHttpClient(c => c.BaseAddress = new Uri("http://inventario-api:5000"));

// Uso en el servicio de pedidos
public class PedidoService(IInventarioApi inventarioApi)
{
    public async Task<Result<Pedido>> CrearPedido(CrearPedidoRequest request)
    {
        // Verificar stock en el microservicio de inventario
        var stockDisponible = await inventarioApi. ObtenerStock(request.ProductoId);

        if (stockDisponible < request.Cantidad)
            return Result.Failure<Pedido>("Stock insuficiente");

        // Reservar stock
        await inventarioApi.ReservarStock(new ReservaRequest
        {
            ProductoId = request.ProductoId,
            Cantidad = request.Cantidad
        });

        // Crear pedido... 
        return Result.Success(pedido);
    }
}
```

---

## APIs Web en ASP.NET Core

Una API web es un conjunto de reglas y protocolos que permite a diferentes aplicaciones comunicarse entre sí a través de HTTP.

![apis](../images/apis.gif)

**Tipos principales de APIs Web:**

1. **API REST**: Usa métodos HTTP estándar (GET, POST, PUT, DELETE) para operaciones CRUD. 

2. **API GraphQL**:  Lenguaje de consulta que permite a los clientes solicitar exactamente los datos que necesitan.

3. **API WebSocket**: Comunicación bidireccional en tiempo real mediante conexiones persistentes.

4. **gRPC**: Framework de RPC de alto rendimiento que usa Protocol Buffers.

**Ejemplo de API REST con ASP.NET Core Minimal APIs:**

```csharp
using Microsoft.AspNetCore.Mvc;
using MiAplicacion.Application. Interfaces;
using MiAplicacion.Application.DTOs;

var builder = WebApplication.CreateBuilder(args);

// Configurar servicios
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();
// ...  otras configuraciones

var app = builder.Build();

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

// Definir endpoints
var productosGroup = app.MapGroup("/api/productos")
    .WithTags("Productos");

// GET:  Obtener todos los productos
productosGroup.MapGet("/", async (IProductoService service) =>
{
    var productos = await service.ObtenerTodosAsync();
    return Results.Ok(productos);
})
.WithName("ObtenerProductos")
.WithOpenApi();

// GET: Obtener producto por ID
productosGroup.MapGet("/{id:int}", async (int id, IProductoService service) =>
{
    var resultado = await service.ObtenerPorIdAsync(id);
    
    return resultado. Match(
        onSuccess: producto => Results.Ok(producto),
        onFailure:  error => Results.NotFound(new { error })
    );
})
.WithName("ObtenerProductoPorId")
.WithOpenApi();

// POST: Crear producto
productosGroup.MapPost("/", async ([FromBody] CrearProductoDto dto, IProductoService service) =>
{
    var resultado = await service.CrearAsync(dto);
    
    return resultado. Match(
        onSuccess: producto => Results.Created($"/api/productos/{producto.Id}", producto),
        onFailure: error => Results.BadRequest(new { error })
    );
})
.WithName("CrearProducto")
.WithOpenApi();

// PUT: Actualizar producto
productosGroup.MapPut("/{id:int}", async (int id, [FromBody] ActualizarProductoDto dto, IProductoService service) =>
{
    var resultado = await service.ActualizarAsync(id, dto);
    
    return resultado.Match(
        onSuccess: producto => Results.Ok(producto),
        onFailure: error => Results.BadRequest(new { error })
    );
})
.WithName("ActualizarProducto")
.WithOpenApi();

// DELETE: Eliminar producto
productosGroup.MapDelete("/{id:int}", async (int id, IProductoService service) =>
{
    var resultado = await service. EliminarAsync(id);
    
    return resultado.IsSuccess
        ? Results.NoContent()
        : Results.NotFound(new { error = resultado.Error });
})
.WithName("EliminarProducto")
.WithOpenApi();

app.Run();
```

**WebSockets nativos en ASP.NET Core:**

```csharp
using System. Net.WebSockets;
using System.Text;

var builder = WebApplication.CreateBuilder(args);
var app = builder.Build();

app.UseWebSockets();

app.Map("/ws", async context =>
{
    if (context.WebSockets.IsWebSocketRequest)
    {
        using var webSocket = await context.WebSockets.AcceptWebSocketAsync();
        await Echo(webSocket);
    }
    else
    {
        context. Response.StatusCode = 400;
    }
});

async Task Echo(WebSocket webSocket)
{
    var buffer = new byte[1024 * 4];
    var result = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), CancellationToken.None);

    while (!result.CloseStatus.HasValue)
    {
        // Echo el mensaje de vuelta
        await webSocket.SendAsync(
            new ArraySegment<byte>(buffer, 0, result.Count),
            result.MessageType,
            result.EndOfMessage,
            CancellationToken. None);

        result = await webSocket.ReceiveAsync(new ArraySegment<byte>(buffer), CancellationToken.None);
    }

    await webSocket.CloseAsync(result.CloseStatus.Value, result.CloseStatusDescription, CancellationToken.None);
}

app.Run();
```

---

