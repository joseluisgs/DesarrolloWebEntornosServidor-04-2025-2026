# Programación Reactiva en .NET

---

- [Programación Reactiva en .NET](#programación-reactiva-en-net)
  - [Introducción a la Programación Reactiva](#introducción-a-la-programación-reactiva)
  - [System.Reactive (Rx.NET)](#systemreactive-rxnet)
  - [IAsyncEnumerable: Streams Asíncronos Nativos](#iasyncenumerable-streams-asíncronos-nativos)
  - [Comparación: IObservable vs IAsyncEnumerable](#comparación-iobservable-vs-iasyncenumerable)
  - [Notificaciones en Tiempo Real con Observables](#notificaciones-en-tiempo-real-con-observables)
  - [Testing de código reactivo](#testing-de-código-reactivo)
  - [Entity Framework Core con programación reactiva](#entity-framework-core-con-programación-reactiva)

---

## Introducción a la Programación Reactiva

La [programación reactiva](https://www.reactivemanifesto.org/es) es un paradigma que se centra en el manejo de flujos de datos y la propagación de cambios. En lugar de esperar a que se complete una operación, reaccionamos cuando los datos están disponibles. 

![Observer Pattern](../images/observer. png)

**Programación Secuencial vs Programación Reactiva:**

```csharp
// ❌ Programación Secuencial (bloqueante)
public List<Producto> ObtenerProductosSecuencial()
{
    var productos = _database.GetProductos(); // Bloquea
    var procesados = ProcesarProductos(productos); // Bloquea
    return procesados;
}

// ✅ Programación Reactiva (no bloqueante)
public IObservable<Producto> ObtenerProductosReactivo()
{
    return Observable.Create<Producto>(async observer =>
    {
        var productos = await _database.GetProductosAsync();
        foreach (var producto in productos)
        {
            observer.OnNext(producto); // Emite cada producto
        }
        observer. OnCompleted();
    });
}
```

**Ventajas de la programación reactiva:**

✅ **Mejor rendimiento**: No bloquea hilos mientras espera datos. 

✅ **Escalabilidad**: Puede manejar múltiples flujos de datos simultáneamente.

✅ **Tiempo real**: Ideal para aplicaciones que reaccionan a eventos en tiempo real.

---

## System.Reactive (Rx.NET)

**Rx.NET** (Reactive Extensions for .NET) es la implementación oficial de ReactiveX para .NET, equivalente a RxJava. 

**Instalación:**

```bash
dotnet add package System.Reactive
```

**Conceptos fundamentales:**

1. **IObservable<T>**: Representa una fuente de datos que puede emitir valores (productor).
2. **IObserver<T>**: Representa un consumidor de datos (observador).
3. **Operadores**: Métodos para transformar, filtrar y combinar flujos. 

**Ejemplo básico:**

```csharp
using System. Reactive. Linq;
using System. Reactive.Subjects;

// Crear un observable simple
var observable = Observable.Range(1, 10);

// Suscribirse y procesar los valores
observable
    .Where(x => x % 2 == 0)
    .Select(x => x * 2)
    .Subscribe(
        onNext: valor => Console.WriteLine($"Valor: {valor}"),
        onError: error => Console.WriteLine($"Error: {error}"),
        onCompleted: () => Console.WriteLine("Completado")
    );

// Salida:
// Valor: 4
// Valor: 8
// Valor: 12
// Valor: 16
// Valor: 20
// Completado
```

**Crear observables de diferentes formas:**

```csharp
// 1. Observable. Return - emite un solo valor
var single = Observable.Return(42);

// 2. Observable.Range - emite una secuencia de números
var range = Observable.Range(1, 5);

// 3. Observable.Interval - emite valores periódicamente
var interval = Observable. Interval(TimeSpan.FromSeconds(1));

// 4. Observable.Timer - emite después de un delay
var timer = Observable.Timer(TimeSpan.FromSeconds(2));

// 5. Observable. Create - crear observable personalizado
var custom = Observable.Create<int>(observer =>
{
    observer.OnNext(1);
    observer.OnNext(2);
    observer.OnNext(3);
    observer.OnCompleted();
    
    return Disposable.Empty;
});

// 6. Observable.FromAsync - desde una tarea asíncrona
var fromAsync = Observable.FromAsync(async () =>
{
    await Task.Delay(1000);
    return "Resultado";
});
```

**Operadores comunes:**

```csharp
var numeros = Observable.Range(1, 10);

// Where - filtrar
var pares = numeros.Where(n => n % 2 == 0);

// Select - transformar
var cuadrados = numeros.Select(n => n * n);

// Take - tomar los primeros N elementos
var primerosCinco = numeros.Take(5);

// Skip - saltar los primeros N elementos
var saltarTres = numeros.Skip(3);

// Distinct - eliminar duplicados
var unicos = Observable.Range(1, 5)
    .SelectMany(n => Observable.Return(n).Repeat(2))
    .Distinct();

// Throttle - emitir solo después de un período de inactividad
var throttled = interval.Throttle(TimeSpan.FromMilliseconds(500));

// Buffer - agrupar elementos en lotes
var buffered = numeros.Buffer(3); // Grupos de 3

// Scan - acumulador (como Aggregate pero emite valores intermedios)
var suma = numeros.Scan(0, (acc, n) => acc + n);

// Merge - combinar múltiples observables
var merged = Observable.Merge(numeros, Observable.Range(100, 5));

// Zip - combinar elementos por posición
var zipped = numeros.Zip(Observable.Range(100, 10), (a, b) => $"{a}-{b}");

// CombineLatest - combinar últimos valores emitidos
var combined = numeros.CombineLatest(
    Observable.Range(100, 5),
    (a, b) => a + b
);

// Catch - manejar errores
var conManejo = Observable.Throw<int>(new Exception("Error"))
    . Catch<int, Exception>(ex => Observable.Return(-1));
```

**Ejemplo completo con operadores:**

```csharp
public class ServicioProductosReactivo
{
    public IObservable<Producto> ObtenerProductosTransformados()
    {
        return Observable. Create<Producto>(async observer =>
        {
            try
            {
                // Simular obtención de datos
                var productos = await ObtenerProductosAsync();

                foreach (var producto in productos)
                {
                    observer.OnNext(producto);
                    await Task.Delay(100); // Simular procesamiento
                }

                observer.OnCompleted();
            }
            catch (Exception ex)
            {
                observer.OnError(ex);
            }
        })
        .Where(p => p.Precio > 100)
        .Select(p => new Producto
        {
            Id = p.Id,
            Nombre = p.Nombre. ToUpper(),
            Precio = p.Precio * 1.21m // Con IVA
        })
        . Distinct(p => p.Id);
    }

    // Uso
    public async Task EjecutarAsync()
    {
        var subscription = ObtenerProductosTransformados()
            .Subscribe(
                producto => Console.WriteLine($"Procesado: {producto.Nombre} - {producto.Precio:C}"),
                error => Console.WriteLine($"❌ Error: {error.Message}"),
                () => Console.WriteLine("✅ Procesamiento completado")
            );

        // La suscripción es IDisposable, se puede cancelar
        await Task.Delay(5000);
        subscription.Dispose(); // Cancelar suscripción
    }
}
```

**Subjects (Hot Observables):**

Los **Subjects** son tanto observables como observadores, permitiendo emitir valores manualmente. 

```csharp
// 1. Subject<T> - básico
var subject = new Subject<int>();

subject.Subscribe(x => Console.WriteLine($"Observador 1: {x}"));

subject.OnNext(1);
subject.OnNext(2);

subject.Subscribe(x => Console.WriteLine($"Observador 2: {x}"));

subject.OnNext(3); // Ambos observadores reciben este valor

// 2. BehaviorSubject<T> - guarda el último valor emitido
var behaviorSubject = new BehaviorSubject<int>(0); // Valor inicial

behaviorSubject.Subscribe(x => Console.WriteLine($"Observador 1: {x}")); // Recibe 0

behaviorSubject.OnNext(1);
behaviorSubject.OnNext(2);

behaviorSubject.Subscribe(x => Console.WriteLine($"Observador 2: {x}")); // Recibe 2 (último valor)

// 3. ReplaySubject<T> - almacena todos los valores emitidos
var replaySubject = new ReplaySubject<int>();

replaySubject.OnNext(1);
replaySubject.OnNext(2);

replaySubject.Subscribe(x => Console.WriteLine($"Observador:  {x}")); // Recibe 1 y 2

replaySubject.OnNext(3);

// 4. AsyncSubject<T> - solo emite el último valor cuando se completa
var asyncSubject = new AsyncSubject<int>();

asyncSubject.Subscribe(x => Console.WriteLine($"Valor final: {x}"));

asyncSubject.OnNext(1);
asyncSubject.OnNext(2);
asyncSubject.OnNext(3);

asyncSubject.OnCompleted(); // Solo aquí se emite el 3
```

---

## IAsyncEnumerable: Streams Asíncronos Nativos

**`IAsyncEnumerable<T>`** es la alternativa nativa de .NET a los observables, introducida en C# 8.0.  Es más simple y está integrada directamente en el lenguaje.

```csharp
// Crear un IAsyncEnumerable
public async IAsyncEnumerable<int> GenerarNumerosAsync()
{
    for (int i = 1; i <= 10; i++)
    {
        await Task.Delay(500); // Simular operación asíncrona
        yield return i;
    }
}

// Consumir con await foreach
public async Task Consumir Async()
{
    await foreach (var numero in GenerarNumerosAsync())
    {
        Console.WriteLine($"Número: {numero}");
    }
}
```

**Operaciones con IAsyncEnumerable:**

```csharp
public class ServicioProductosAsync
{
    // Producir datos asíncronamente
    public async IAsyncEnumerable<Producto> ObtenerProductosStreamAsync()
    {
        var productos = await _context.Productos.ToListAsync();

        foreach (var producto in productos)
        {
            await Task.Delay(100); // Simular procesamiento
            yield return producto;
        }
    }

    // Filtrar y transformar
    public async IAsyncEnumerable<ProductoDto> ObtenerProductosTransformadosAsync()
    {
        await foreach (var producto in ObtenerProductosStreamAsync())
        {
            if (producto.Precio > 100)
            {
                yield return new ProductoDto
                {
                    Nombre = producto.Nombre. ToUpper(),
                    PrecioConIva = producto.Precio * 1.21m
                };
            }
        }
    }

    // Uso
    public async Task ProcesarAsync()
    {
        await foreach (var producto in ObtenerProductosTransformadosAsync())
        {
            Console.WriteLine($"{producto.Nombre}:  {producto.PrecioConIva:C}");
        }
    }
}
```

**Operadores LINQ con IAsyncEnumerable:**

```csharp
// Requiere el paquete System.Linq. Async
// dotnet add package System.Linq. Async

using System.Linq;

public async Task EjemploLinqAsync()
{
    var stream = GenerarNumerosAsync();

    // Where
    var pares = stream.Where(n => n % 2 == 0);

    // Select
    var cuadrados = stream.Select(n => n * n);

    // Take
    var primerosCinco = stream.Take(5);

    // Skip
    var saltarTres = stream.Skip(3);

    // ToListAsync
    var lista = await stream.ToListAsync();

    // CountAsync
    var cantidad = await stream.CountAsync();

    // FirstOrDefaultAsync
    var primero = await stream.FirstOrDefaultAsync();

    // AnyAsync
    var hayPares = await stream.AnyAsync(n => n % 2 == 0);

    // SumAsync
    var suma = await stream.SumAsync();
}
```

---

## Comparación: IObservable vs IAsyncEnumerable

| **Característica**   | **IObservable<T>** (Rx.NET)               | **IAsyncEnumerable<T>**                |
| :------------------- | :---------------------------------------- | :------------------------------------- |
| **Push vs Pull**     | Push (el productor empuja datos)          | Pull (el consumidor solicita datos)    |
| **Cancelación**      | IDisposable                               | CancellationToken                      |
| **Backpressure**     | Soporte limitado                          | Soporte nativo                         |
| **Hot/Cold streams** | Soporta ambos                             | Solo cold streams                      |
| **Operadores**       | Muy amplia librería de operadores         | LINQ estándar + System.Linq.Async      |
| **Complejidad**      | Mayor curva de aprendizaje                | Más simple, integrado en C#            |
| **Uso recomendado**  | Eventos UI, notificaciones en tiempo real | Streams de datos, APIs, bases de datos |

**Cuándo usar cada uno:**

✅ **IObservable (Rx.NET)**: 
- Eventos de UI
- WebSockets
- Notificaciones en tiempo real
- Múltiples suscriptores
- Operadores complejos de transformación

✅ **IAsyncEnumerable**:
- Lectura de bases de datos
- Procesamiento de archivos grandes
- APIs que devuelven streams
- Simplicidad y código más limpio

---

## Notificaciones en Tiempo Real con Observables

Ejemplo completo de un sistema reactivo con notificaciones en tiempo real:

```csharp
using System. Reactive.Subjects;
using System.Reactive. Linq;

public record Producto(Guid Id, string Nombre, decimal Precio, string Categoria);

public class ProductoRepository
{
    private readonly List<Producto> _productos = new();
    private readonly BehaviorSubject<List<Producto>> _productosSubject = new(new List<Producto>());
    private readonly Subject<string> _notificacionesSubject = new();

    // Exponer solo como IObservable para evitar emisiones externas
    public IObservable<List<Producto>> ProductosObservable => _productosSubject. AsObservable();
    public IObservable<string> NotificacionesObservable => _notificacionesSubject.AsObservable();

    public void Agregar(Producto producto)
    {
        _productos.Add(producto);
        _productosSubject.OnNext(_productos);
        _notificacionesSubject. OnNext($"✅ Producto agregado: {producto.Nombre}");
    }

    public void Actualizar(Guid id, Producto productoActualizado)
    {
        var index = _productos. FindIndex(p => p.Id == id);
        if (index >= 0)
        {
            _productos[index] = productoActualizado;
            _productosSubject.OnNext(_productos);
            _notificacionesSubject.OnNext($"🔄 Producto actualizado:  {productoActualizado. Nombre}");
        }
    }

    public void Eliminar(Guid id)
    {
        var producto = _productos.FirstOrDefault(p => p.Id == id);
        if (producto != null)
        {
            _productos.Remove(producto);
            _productosSubject.OnNext(_productos);
            _notificacionesSubject.OnNext($"❌ Producto eliminado: {producto.Nombre}");
        }
    }
}

// Uso
public class Program
{
    public static async Task Main()
    {
        var repo = new ProductoRepository();

        // Suscribirse a cambios en la lista
        repo.ProductosObservable
            .Subscribe(productos =>
            {
                Console. WriteLine($"\n📋 Lista actualizada ({productos.Count} productos):");
                foreach (var p in productos)
                {
                    Console.WriteLine($"  - {p.Nombre}:  {p.Precio:C}");
                }
            });

        // Suscribirse a notificaciones
        repo.NotificacionesObservable
            .Subscribe(notificacion => Console.WriteLine($"🔔 {notificacion}"));

        // Operaciones
        Console.WriteLine("=== Sistema de Notificaciones en Tiempo Real ===\n");

        var producto1 = new Producto(Guid.NewGuid(), "Laptop", 1200m, "Electrónica");
        repo.Agregar(producto1);
        await Task.Delay(2000);

        var producto2 = new Producto(Guid.NewGuid(), "Mouse", 25m, "Accesorios");
        repo.Agregar(producto2);
        await Task.Delay(2000);

        repo.Actualizar(producto1.Id, producto1 with { Precio = 1100m });
        await Task. Delay(2000);

        repo.Eliminar(producto2.Id);
        await Task.Delay(2000);
    }
}
```

**Salida:**

```
=== Sistema de Notificaciones en Tiempo Real ===

🔔 ✅ Producto agregado:  Laptop

📋 Lista actualizada (1 productos):
  - Laptop: $1,200.00

🔔 ✅ Producto agregado: Mouse

📋 Lista actualizada (2 productos):
  - Laptop: $1,200.00
  - Mouse: $25.00

🔔 🔄 Producto actualizado:  Laptop

📋 Lista actualizada (2 productos):
  - Laptop: $1,100.00
  - Mouse: $25.00

🔔 ❌ Producto eliminado: Mouse

📋 Lista actualizada (1 productos):
  - Laptop: $1,100.00
```

---

## Testing de código reactivo

```csharp
using Microsoft.Reactive.Testing;
using NUnit.Framework;
using System. Reactive. Linq;

[TestFixture]
public class ProductoServiceReactivoTests
{
    [Test]
    public void ObtenerProductos_DebeEmitirTodosLosValores()
    {
        // Arrange
        var productos = new[]
        {
            new Producto(Guid.NewGuid(), "Producto 1", 100m, "Cat1"),
            new Producto(Guid.NewGuid(), "Producto 2", 200m, "Cat2")
        };

        var observable = Observable.FromEnumerable(productos);

        var resultados = new List<Producto>();

        // Act
        observable.Subscribe(p => resultados.Add(p));

        // Assert
        resultados. Should().HaveCount(2);
        resultados.Should().Contain(p => p.Nombre == "Producto 1");
    }

    [Test]
    public void ObtenerProductos_ConFiltro_DebeFiltrarCorrectamente()
    {
        // Arrange
        var productos = Observable.Range(1, 10)
            .Select(i => new Producto(Guid.NewGuid(), $"Producto {i}", i * 100m, "Cat"));

        // Act
        var resultados = new List<Producto>();
        productos
            .Where(p => p. Precio > 500m)
            .Subscribe(p => resultados.Add(p));

        // Assert
        resultados. Should().HaveCount(5);
        resultados.Should().AllSatisfy(p => p. Precio. Should().BeGreaterThan(500m));
    }
}
```

---

## Entity Framework Core con programación reactiva

```csharp
public class ProductoRepositoryEF
{
    private readonly ApplicationDbContext _context;

    public ProductoRepositoryEF(ApplicationDbContext context)
    {
        _context = context;
    }

    // Con IAsyncEnumerable (recomendado con EF Core)
    public async IAsyncEnumerable<Producto> ObtenerProductosStreamAsync()
    {
        await foreach (var producto in _context. Productos.AsAsyncEnumerable())
        {
            yield return producto;
        }
    }

    // Con IObservable
    public IObservable<Producto> ObtenerProductosObservable()
    {
        return Observable.FromAsync(async () => await _context.Productos.ToListAsync())
            .SelectMany(productos => productos.ToObservable());
    }
}
```

---

