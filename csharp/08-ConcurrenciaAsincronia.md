# Programación Concurrente y Asíncrona en . NET

---

- [Programación Concurrente y Asíncrona en . NET](#programación-concurrente-y-asíncrona-en--net)
  - [Introducción a la Sincronía y Asincronía](#introducción-a-la-sincronía-y-asincronía)
    - [¿Qué es la Sincronía?](#qué-es-la-sincronía)
    - [¿Qué es la Asincronía?](#qué-es-la-asincronía)
    - [¿Por qué es importante programar de manera asíncrona?](#por-qué-es-importante-programar-de-manera-asíncrona)
  - [Async/Await:   El modelo asíncrono de . NET](#asyncawait---el-modelo-asíncrono-de--net)
  - [Task y Task\<T\>: Futuros y Promesas](#task-y-taskt-futuros-y-promesas)
  - [Hilos y ThreadPool](#hilos-y-threadpool)
  - [Sección crítica y sincronización](#sección-crítica-y-sincronización)
  - [Colecciones concurrentes](#colecciones-concurrentes)
  - [Parallel LINQ (PLINQ)](#parallel-linq-plinq)
  - [Channels para comunicación entre tareas](#channels-para-comunicación-entre-tareas)

---

## Introducción a la Sincronía y Asincronía

La programación concurrente permite la ejecución de múltiples procesos o tareas de manera simultánea o intercalada, mejorando el rendimiento y la eficiencia. 

### ¿Qué es la Sincronía? 

La sincronía se refiere a la ejecución secuencial de tareas.   Cuando una tarea se ejecuta de manera síncrona, el programa espera a que esa tarea termine antes de pasar a la siguiente.

```csharp
public void ProcesoSincrono()
{
    Tarea1(); // Espera a que termine
    Tarea2(); // Espera a que termine
    Tarea3(); // Espera a que termine
}
```

### ¿Qué es la Asincronía? 

La asincronía permite que las tareas se ejecuten de manera concurrente.   Cuando una tarea se ejecuta de manera asíncrona, el programa no espera a que esa tarea termine antes de continuar.

```csharp
public async Task ProcesoAsincrono()
{
    var tarea1 = Tarea1Async(); // No espera
    var tarea2 = Tarea2Async(); // No espera
    var tarea3 = Tarea3Async(); // No espera

    await Task.WhenAll(tarea1, tarea2, tarea3); // Espera a que todas terminen
}
```

![Sync vs Async](../images/syncvsasync.png)

### ¿Por qué es importante programar de manera asíncrona? 

1. **Rendimiento mejorado**: Mejor utilización de recursos del sistema. 
2. **Experiencia del usuario**: Interfaces de usuario más responsivas.
3. **Escalabilidad**: Mejor manejo de cargas simultáneas. 

---

## Async/Await:   El modelo asíncrono de . NET

**`async`** y **`await`** son las palabras clave fundamentales para programación asíncrona en C#.  Son el equivalente directo a las corrutinas de Kotlin o `async/await` de JavaScript.

```csharp
// Método asíncrono básico
public async Task<string> ObtenerDatosAsync()
{
    // await suspende la ejecución hasta que la tarea se complete
    await Task.Delay(1000); // Simula una operación de 1 segundo
    return "Datos obtenidos";
}

// Llamada al método asíncrono
public async Task EjemploUso()
{
    Console.WriteLine("Inicio");
    
    string datos = await ObtenerDatosAsync();
    
    Console.WriteLine($"Resultado: {datos}");
    Console.WriteLine("Fin");
}
```

**Reglas importantes de async/await:**

```csharp
// ✅ BIEN: Método asíncrono que devuelve Task
public async Task MetodoAsync()
{
    await Task.Delay(100);
}

// ✅ BIEN: Método asíncrono que devuelve Task<T>
public async Task<int> MetodoConResultadoAsync()
{
    await Task.Delay(100);
    return 42;
}

// ✅ BIEN: Método asíncrono void (solo para event handlers)
public async void Button_Click(object sender, EventArgs e)
{
    await HacerAlgoAsync();
}

// ❌ MAL: Bloquear con . Result o .Wait() puede causar deadlocks
public void MetodoSincronoMalo()
{
    var resultado = MetodoAsync().Result; // ❌ NO HACER ESTO
}

// ✅ BIEN: Usar await en su lugar
public async Task MetodoSincronoBueno()
{
    await MetodoAsync();
}
```

**Ejemplo completo de operaciones asíncronas:**

```csharp
public class ServicioProductos
{
    // Simular llamada a API externa
    public async Task<Producto> ObtenerProductoAsync(int id)
    {
        Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] Obteniendo producto {id}.. .");
        
        await Task.Delay(2000); // Simular latencia de red
        
        return new Producto 
        { 
            Id = id, 
            Nombre = $"Producto {id}", 
            Precio = id * 100m 
        };
    }

    // Simular guardado en base de datos
    public async Task GuardarProductoAsync(Producto producto)
    {
        Console.WriteLine($"[{DateTime.Now:HH: mm:ss}] Guardando {producto. Nombre}...");
        
        await Task.Delay(1000); // Simular escritura en BD
        
        Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] ✅ {producto.Nombre} guardado");
    }

    // Procesamiento secuencial (lento)
    public async Task ProcesarSecuencialAsync()
    {
        var inicio = DateTime.Now;

        var producto1 = await ObtenerProductoAsync(1);
        var producto2 = await ObtenerProductoAsync(2);
        var producto3 = await ObtenerProductoAsync(3);

        var duracion = DateTime.Now - inicio;
        Console.WriteLine($"Tiempo secuencial: {duracion.TotalSeconds}s"); // ~6 segundos
    }

    // Procesamiento paralelo (rápido)
    public async Task ProcesarParaleloAsync()
    {
        var inicio = DateTime.Now;

        // Iniciar todas las tareas al mismo tiempo
        var tarea1 = ObtenerProductoAsync(1);
        var tarea2 = ObtenerProductoAsync(2);
        var tarea3 = ObtenerProductoAsync(3);

        // Esperar a que todas terminen
        var productos = await Task.WhenAll(tarea1, tarea2, tarea3);

        var duracion = DateTime.Now - inicio;
        Console.WriteLine($"Tiempo paralelo: {duracion.TotalSeconds}s"); // ~2 segundos
    }
}
```

---

## Task y Task\<T\>: Futuros y Promesas

**`Task`** representa una operación asíncrona que no devuelve valor (equivalente a `Future<Void>` de Java).

**`Task<T>`** representa una operación asíncrona que devuelve un valor de tipo `T` (equivalente a `Future<T>` de Java o `CompletableFuture<T>`).

```csharp
// Task sin valor de retorno
public async Task RealizarOperacionAsync()
{
    await Task.Delay(1000);
    Console.WriteLine("Operación completada");
}

// Task con valor de retorno
public async Task<int> CalcularAsync(int a, int b)
{
    await Task.Delay(500);
    return a + b;
}

// Uso
var resultado = await CalcularAsync(5, 3); // resultado = 8
```

**Operaciones comunes con Task:**

```csharp
public class EjemplosTask
{
    // Task. Run - Ejecutar código en un hilo del pool
    public async Task EjemploTaskRun()
    {
        var resultado = await Task.Run(() =>
        {
            // Operación intensiva en CPU
            Thread.Sleep(2000);
            return 42;
        });

        Console.WriteLine($"Resultado: {resultado}");
    }

    // Task. WhenAll - Esperar a que TODAS las tareas terminen
    public async Task EjemploWhenAll()
    {
        var tareas = new[]
        {
            ObtenerDatoAsync(1),
            ObtenerDatoAsync(2),
            ObtenerDatoAsync(3)
        };

        string[] resultados = await Task.WhenAll(tareas);
        
        Console.WriteLine($"Resultados: {string.Join(", ", resultados)}");
    }

    // Task.WhenAny - Esperar a que CUALQUIERA termine
    public async Task EjemploWhenAny()
    {
        var tarea1 = Task. Delay(1000).ContinueWith(_ => "Rápida");
        var tarea2 = Task.Delay(3000).ContinueWith(_ => "Lenta");

        var tareaCompletada = await Task.WhenAny(tarea1, tarea2);
        var resultado = await tareaCompletada;

        Console.WriteLine($"Primera en completar: {resultado}");
    }

    // Task.Delay - Equivalente asíncrono de Thread.Sleep
    public async Task EjemploDelay()
    {
        Console.WriteLine("Inicio");
        await Task.Delay(2000); // No bloquea el hilo
        Console.WriteLine("Fin (después de 2 segundos)");
    }

    // ContinueWith - Encadenar operaciones
    public async Task EjemploContinueWith()
    {
        var resultado = await Task.Run(() => 10)
            .ContinueWith(tarea => tarea.Result * 2)
            .ContinueWith(tarea => tarea.Result + 5);

        Console.WriteLine($"Resultado: {resultado}"); // 25
    }

    // ConfigureAwait - Control del contexto de sincronización
    public async Task EjemploConfigureAwait()
    {
        // ConfigureAwait(false) evita capturar el contexto de sincronización
        // Mejora el rendimiento en librerías
        await Task.Delay(1000).ConfigureAwait(false);
        
        // El código después de esto puede ejecutarse en un hilo diferente
    }

    private async Task<string> ObtenerDatoAsync(int id)
    {
        await Task. Delay(1000);
        return $"Dato {id}";
    }
}
```

**Manejo de errores con Task:**

```csharp
public class ManejoErroresAsync
{
    public async Task EjemploManejoErrores()
    {
        try
        {
            var resultado = await OperacionConErrorAsync();
        }
        catch (InvalidOperationException ex)
        {
            Console.WriteLine($"Error capturado: {ex.Message}");
        }
    }

    public async Task<int> OperacionConErrorAsync()
    {
        await Task. Delay(1000);
        throw new InvalidOperationException("Algo salió mal");
    }

    // Capturar errores de múltiples tareas
    public async Task EjemploErroresMultiples()
    {
        try
        {
            await Task.WhenAll(
                TareaConErrorAsync("Tarea 1"),
                TareaConErrorAsync("Tarea 2"),
                TareaConErrorAsync("Tarea 3")
            );
        }
        catch (Exception ex)
        {
            // Solo captura la primera excepción
            Console. WriteLine($"Primera excepción: {ex.Message}");
        }
    }

    // Para capturar todas las excepciones
    public async Task EjemploTodasLasExcepciones()
    {
        var tareas = new[]
        {
            TareaConErrorAsync("Tarea 1"),
            TareaConErrorAsync("Tarea 2"),
            TareaConErrorAsync("Tarea 3")
        };

        try
        {
            await Task.WhenAll(tareas);
        }
        catch
        {
            // Acceder a todas las excepciones
            foreach (var tarea in tareas. Where(t => t. IsFaulted))
            {
                Console.WriteLine($"Error:  {tarea.Exception?. InnerException?.Message}");
            }
        }
    }

    private async Task TareaConErrorAsync(string nombre)
    {
        await Task.Delay(1000);
        throw new Exception($"Error en {nombre}");
    }
}
```

---

## Hilos y ThreadPool

Aunque `async/await` es la forma recomendada, a veces necesitas trabajar directamente con hilos. 

```csharp
public class EjemplosHilos
{
    // Thread básico
    public void EjemploThread()
    {
        var hilo = new Thread(() =>
        {
            Console.WriteLine($"Ejecutando en hilo: {Thread. CurrentThread.ManagedThreadId}");
            Thread.Sleep(2000);
            Console.WriteLine("Hilo terminado");
        });

        hilo.Start();
        hilo.Join(); // Esperar a que termine
    }

    // ThreadPool
    public void EjemploThreadPool()
    {
        ThreadPool.QueueUserWorkItem(_ =>
        {
            Console. WriteLine($"Ejecutando en ThreadPool: {Thread.CurrentThread. ManagedThreadId}");
            Thread.Sleep(2000);
        });

        // El hilo principal no espera automáticamente
        Thread.Sleep(3000);
    }

    // Pasar datos al hilo
    public void EjemploDatosHilo()
    {
        var hilo = new Thread(obj =>
        {
            var datos = (string)obj! ;
            Console.WriteLine($"Datos recibidos: {datos}");
        });

        hilo.Start("Hola desde el hilo principal");
        hilo.Join();
    }
}
```

---

## Sección crítica y sincronización

Cuando múltiples hilos acceden a recursos compartidos, necesitamos sincronización para evitar condiciones de carrera.

```csharp
public class EjemplosSincronizacion
{
    private int _contador = 0;
    private readonly object _lock = new();
    private readonly SemaphoreSlim _semaforo = new(3); // Máximo 3 hilos concurrentes
    private readonly ReaderWriterLockSlim _rwLock = new();

    // 1. lock (equivalente a synchronized de Java)
    public void IncrementarConLock()
    {
        lock (_lock)
        {
            _contador++;
            Console.WriteLine($"Contador: {_contador}");
        }
    }

    // 2. Monitor (más control que lock)
    public void IncrementarConMonitor()
    {
        Monitor.Enter(_lock);
        try
        {
            _contador++;
        }
        finally
        {
            Monitor.Exit(_lock);
        }
    }

    // 3. SemaphoreSlim (para limitar concurrencia)
    public async Task OperacionConSemaforo()
    {
        await _semaforo.WaitAsync();
        try
        {
            Console.WriteLine($"Hilo {Thread.CurrentThread.ManagedThreadId} ejecutando");
            await Task.Delay(2000);
        }
        finally
        {
            _semaforo.Release();
        }
    }

    // 4. ReaderWriterLockSlim (múltiples lectores, un escritor)
    private string _dato = string.Empty;

    public string Leer()
    {
        _rwLock.EnterReadLock();
        try
        {
            return _dato;
        }
        finally
        {
            _rwLock. ExitReadLock();
        }
    }

    public void Escribir(string valor)
    {
        _rwLock.EnterWriteLock();
        try
        {
            _dato = valor;
        }
        finally
        {
            _rwLock.ExitWriteLock();
        }
    }

    // 5. Interlocked para operaciones atómicas
    private long _contadorAtomico = 0;

    public void IncrementarAtomico()
    {
        Interlocked.Increment(ref _contadorAtomico);
    }
}
```

---

## Colecciones concurrentes

.  NET proporciona colecciones thread-safe específicas para programación concurrente. 

```csharp
using System.Collections.Concurrent;

public class EjemplosColeccionesConcurrentes
{
    // ConcurrentBag - colección sin orden
    public void EjemploConcurrentBag()
    {
        var bag = new ConcurrentBag<int>();

        Parallel.For(0, 10, i =>
        {
            bag.Add(i);
        });

        Console.WriteLine($"Elementos:  {bag.Count}");
    }

    // ConcurrentQueue - cola FIFO thread-safe
    public void EjemploConcurrentQueue()
    {
        var cola = new ConcurrentQueue<string>();

        cola.Enqueue("Primero");
        cola.Enqueue("Segundo");

        if (cola.TryDequeue(out var elemento))
        {
            Console.WriteLine($"Desencolado: {elemento}");
        }
    }

    // ConcurrentStack - pila LIFO thread-safe
    public void EjemploConcurrentStack()
    {
        var pila = new ConcurrentStack<int>();

        pila.Push(1);
        pila.Push(2);

        if (pila.TryPop(out var elemento))
        {
            Console.WriteLine($"Desapilado: {elemento}");
        }
    }

    // ConcurrentDictionary - diccionario thread-safe
    public void EjemploConcurrentDictionary()
    {
        var diccionario = new ConcurrentDictionary<int, string>();

        // AddOrUpdate - agregar o actualizar atómicamente
        diccionario. AddOrUpdate(
            key: 1,
            addValue: "Nuevo",
            updateValueFactory: (key, oldValue) => "Actualizado"
        );

        // GetOrAdd - obtener o agregar atómicamente
        var valor = diccionario.GetOrAdd(2, key => $"Valor {key}");

        // TryRemove
        if (diccionario.TryRemove(1, out var removido))
        {
            Console. WriteLine($"Removido: {removido}");
        }
    }

    // BlockingCollection - productor-consumidor
    public async Task EjemploProductorConsumidor()
    {
        var coleccion = new BlockingCollection<int>(boundedCapacity: 5);

        // Productor
        var productor = Task.Run(() =>
        {
            for (int i = 0; i < 10; i++)
            {
                coleccion.Add(i);
                Console.WriteLine($"Producido: {i}");
                Thread.Sleep(100);
            }
            coleccion.CompleteAdding();
        });

        // Consumidor
        var consumidor = Task.Run(() =>
        {
            foreach (var item in coleccion. GetConsumingEnumerable())
            {
                Console.WriteLine($"Consumido: {item}");
                Thread.Sleep(200);
            }
        });

        await Task.WhenAll(productor, consumidor);
    }
}
```

---

## Parallel LINQ (PLINQ)

PLINQ permite ejecutar consultas LINQ en paralelo automáticamente.

```csharp
public class EjemplosPLINQ
{
    public void ProcesamientoParalelo()
    {
        var numeros = Enumerable.Range(1, 1000000);

        // Procesamiento secuencial
        var resultadoSecuencial = numeros
            .Where(n => n % 2 == 0)
            .Select(n => n * n)
            .Sum();

        // Procesamiento paralelo con AsParallel()
        var resultadoParalelo = numeros
            .AsParallel()
            .Where(n => n % 2 == 0)
            .Select(n => n * n)
            .Sum();

        Console.WriteLine($"Secuencial:  {resultadoSecuencial}");
        Console.WriteLine($"Paralelo:  {resultadoParalelo}");
    }

    public void EjemploForEachParalelo()
    {
        var productos = Enumerable.Range(1, 100)
            .Select(i => new Producto { Id = i, Nombre = $"Producto {i}" });

        // Parallel.ForEach
        Parallel.ForEach(productos, producto =>
        {
            // Procesar cada producto en paralelo
            ProcesarProducto(producto);
        });
    }

    private void ProcesarProducto(Producto producto)
    {
        Thread.Sleep(10);
        Console.WriteLine($"Procesado: {producto.Nombre}");
    }
}
```

---

## Channels para comunicación entre tareas

**Channels** es una API moderna para comunicación asíncrona entre productores y consumidores (equivalente a Kotlin Channels).

```csharp
using System.Threading.Channels;

public class EjemplosChannels
{
    public async Task EjemploBasico()
    {
        var channel = Channel.CreateUnbounded<int>();

        // Productor
        var productor = Task.Run(async () =>
        {
            for (int i = 0; i < 10; i++)
            {
                await channel.Writer.WriteAsync(i);
                Console.WriteLine($"Producido: {i}");
                await Task.Delay(100);
            }
            channel.Writer.Complete();
        });

        // Consumidor
        var consumidor = Task.Run(async () =>
        {
            await foreach (var item in channel.Reader.ReadAllAsync())
            {
                Console.WriteLine($"Consumido: {item}");
                await Task.Delay(200);
            }
        });

        await Task.WhenAll(productor, consumidor);
    }
}
```

---
