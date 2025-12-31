# Manual de Programación en C# y .NET 10 - 2º DAW

---

### Introducción

Bienvenido a un viaje a través de los cimientos del desarrollo de software moderno con **.NET 10** y **C# 14**. En esta guía, no solo exploraremos los conceptos teóricos, sino que también nos ensuciaremos las manos con el código y las herramientas que te permitirán cerrar la brecha entre una idea y una aplicación funcional en el mundo real.

Este manual cubre **programación en C# aplicada al desarrollo de software**. Desde las bases de la gestión de proyectos hasta las sutilezas de los paradigmas de programación más avanzados POO y Funcional, cada capítulo está diseñado para guiarte en la creación de software robusto, eficiente y listo para producción.  A través de la práctica con **NUnit**, la automatización con **dotnet CLI** y la inmersión en la programación **reactiva** con observables y colecciones asíncronas, o consultas de APIs externas y bases de datos, descubrirás cómo la calidad del código, el rendimiento y la estabilidad son los pilares de cualquier aplicación exitosa.  Prepárate para dominar el arte de construir, probar y lanzar tus propias creaciones al mundo.

---

- [Manual de Programación en C# y .NET 10 - 2º DAW](#manual-de-programación-en-c-y-net-10---2º-daw)
    - [Introducción](#introducción)
    - [1. Introducción al lenguaje de programación C#](#1-introducción-al-lenguaje-de-programación-c)
      - [1.1. ¿Qué es C# y por qué usarlo?](#11-qué-es-c-y-por-qué-usarlo)
      - [1.2. El proceso de compilación y ejecución: El papel del CLR](#12-el-proceso-de-compilación-y-ejecución-el-papel-del-clr)
    - [2. Fundamentos de la programación en C#](#2-fundamentos-de-la-programación-en-c)
      - [2.1.  Sintaxis básica, tipos de datos y estructuras de control](#21--sintaxis-básica-tipos-de-datos-y-estructuras-de-control)
      - [2.2. Arrays:  Tipos compuestos](#22-arrays--tipos-compuestos)
      - [2.3. Modificadores de acceso:  `public`, `private` y `protected`](#23-modificadores-de-acceso--public-private-y-protected)
      - [2.4. Modificadores de comportamiento: `static` y `readonly`/`const`](#24-modificadores-de-comportamiento-static-y-readonlyconst)
    - [3. Programación orientada a objetos (POO)](#3-programación-orientada-a-objetos-poo)
      - [3.1. Clases, objetos, propiedades y métodos](#31-clases-objetos-propiedades-y-métodos)
      - [3.2. Constructores, propiedades y Primary Constructors](#32-constructores-propiedades-y-primary-constructors)
      - [3.3. Principios de la POO](#33-principios-de-la-poo)
      - [3.4. Interfaces y clases abstractas](#34-interfaces-y-clases-abstractas)
      - [3.5. Clases `abstract` y `sealed`](#35-clases-abstract-y-sealed)
      - [3.6. Clases vs.  `Records`](#36-clases-vs--records)
      - [3.7. El uso de tipos anulables y operadores null](#37-el-uso-de-tipos-anulables-y-operadores-null)
    - [3.8. El uso de genéricos](#38-el-uso-de-genéricos)
      - [¿Por qué son necesarios los genéricos?](#por-qué-son-necesarios-los-genéricos)
      - [Clases y métodos genéricos](#clases-y-métodos-genéricos)
      - [Restricciones de tipos (Generic Constraints)](#restricciones-de-tipos-generic-constraints)
    - [4. Manejo de errores y excepciones](#4-manejo-de-errores-y-excepciones)
      - [4.1. Tipos de excepciones en . NET](#41-tipos-de-excepciones-en--net)
      - [4.2. Manejo de excepciones:  `try-catch-finally`](#42-manejo-de-excepciones--try-catch-finally)
      - [4.3. El uso de `using` y `IDisposable`](#43-el-uso-de-using-y-idisposable)
      - [4.4. Creación de excepciones personalizadas](#44-creación-de-excepciones-personalizadas)
    - [5. Programación funcional](#5-programación-funcional)
      - [5.1. Funciones de primera clase y funciones de orden superior](#51-funciones-de-primera-clase-y-funciones-de-orden-superior)
      - [5.2. Expresiones lambda, delegados y `Func`/`Action`](#52-expresiones-lambda-delegados-y-funcaction)
      - [5.3. Inmutabilidad y ausencia de efectos secundarios](#53-inmutabilidad-y-ausencia-de-efectos-secundarios)
      - [5.4. Composición de funciones](#54-composición-de-funciones)
    - [6. Colecciones y LINQ](#6-colecciones-y-linq)
      - [6.1. Estructuras de datos:  Listas, conjuntos y diccionarios](#61-estructuras-de-datos--listas-conjuntos-y-diccionarios)
      - [6.2. LINQ:  Procesamiento de datos de forma declarativa](#62-linq--procesamiento-de-datos-de-forma-declarativa)
        - [Operaciones comunes con LINQ](#operaciones-comunes-con-linq)
      - [6.3. LINQ paralelo con `AsParallel()`](#63-linq-paralelo-con-asparallel)
      - [6.4. Analogía con SQL](#64-analogía-con-sql)
    - [7. E/S (Entrada/Salida) y manipulación de ficheros](#7-es-entradasalida-y-manipulación-de-ficheros)
      - [7.1. Entrada y salida por consola](#71-entrada-y-salida-por-consola)
      - [7.2. Lectura y escritura de ficheros de texto](#72-lectura-y-escritura-de-ficheros-de-texto)
        - [API de `System.IO` con `using`](#api-de-systemio-con-using)
        - [API Moderna con `File` y LINQ](#api-moderna-con-file-y-linq)
      - [7.3. Manejo de datos JSON con System.Text.Json](#73-manejo-de-datos-json-con-systemtextjson)
    - [8. Bases de datos](#8-bases-de-datos)
      - [8.1. Entity Framework Core:  Acceso a datos con PostgreSQL](#81-entity-framework-core--acceso-a-datos-con-postgresql)
      - [8.2. Configuración del DbContext y modelos](#82-configuración-del-dbcontext-y-modelos)
      - [8.3. Ejecución de consultas:  CRUD con EF Core](#83-ejecución-de-consultas--crud-con-ef-core)
    - [8.4. MongoDB: Manejo de bases de datos NoSQL con API Tipada](#84-mongodb-manejo-de-bases-de-datos-nosql-con-api-tipada)
      - [Conexión con API Tipada y Configuración](#conexión-con-api-tipada-y-configuración)
      - [CRUD con API Tipada](#crud-con-api-tipada)
    - [9. Concurrencia y programación asíncrona](#9-concurrencia-y-programación-asíncrona)
      - [9.1. Introducción a `Task` y `async`/`await`](#91-introducción-a-task-y-asyncawait)
        - [**`Task.WhenAll` - Esperar a que TODAS las tareas se completen**](#taskwhenall---esperar-a-que-todas-las-tareas-se-completen)
        - [**`Task.WhenAny` - Esperar a que AL MENOS UNA tarea se complete**](#taskwhenany---esperar-a-que-al-menos-una-tarea-se-complete)
        - [**Combinando `WhenAll` y `WhenAny` - Patrones avanzados**](#combinando-whenall-y-whenany---patrones-avanzados)
        - [**Manejo de excepciones con múltiples tareas**](#manejo-de-excepciones-con-múltiples-tareas)
      - [9. 2. Configuración de contexto con `ConfigureAwait`](#9-2-configuración-de-contexto-con-configureawait)
      - [9.3. Operaciones paralelas con `Task` y `Parallel`](#93-operaciones-paralelas-con-task-y-parallel)
    - [10. Programación Reactiva:   Observables vs Colecciones Asíncronas](#10-programación-reactiva---observables-vs-colecciones-asíncronas)
      - [10.1. Introducción a la programación reactiva en .  NET](#101-introducción-a-la-programación-reactiva-en---net)
      - [10.2. `IAsyncEnumerable<T>`: Colecciones asíncronas nativas](#102-iasyncenumerablet-colecciones-asíncronas-nativas)
        - [Ejemplo:   Lectura asíncrona de fichero con `IAsyncEnumerable`](#ejemplo---lectura-asíncrona-de-fichero-con-iasyncenumerable)
      - [10.3. `IObservable<T>` y System.Reactive (Rx.  NET)](#103-iobservablet-y-systemreactive-rx--net)
        - [Ejemplo: Flujo caliente con `Subject`](#ejemplo-flujo-caliente-con-subject)
      - [10.4. Diferencias clave:   Observables vs Colecciones Asíncronas](#104-diferencias-clave---observables-vs-colecciones-asíncronas)
      - [10.5. Operadores de Rx.NET](#105-operadores-de-rxnet)
    - [11. Railway Oriented Programming](#11-railway-oriented-programming)
      - [11.1. El problema de la gestión de errores con excepciones](#111-el-problema-de-la-gestión-de-errores-con-excepciones)
      - [11.2. Introducción a Railway Oriented Programming](#112-introducción-a-railway-oriented-programming)
      - [11.3. La clase `Result` de CSharpFunctionalExtensions](#113-la-clase-result-de-csharpfunctionalextensions)
      - [11.4. Encadenamiento de operaciones y manejo de errores](#114-encadenamiento-de-operaciones-y-manejo-de-errores)
      - [11.5. Más operadores clave de `Result`](#115-más-operadores-clave-de-result)
      - [11.6. Beneficios y casos de uso en aplicaciones . NET](#116-beneficios-y-casos-de-uso-en-aplicaciones--net)
    - [12. Clientes REST con HttpClient y Refit](#12-clientes-rest-con-httpclient-y-refit)
      - [12.1. Configuración con Refit:    Interfaces y modelos de datos](#121-configuración-con-refit----interfaces-y-modelos-de-datos)
      - [12.2. Operaciones CRUD con Refit](#122-operaciones-crud-con-refit)
        - [12.2.1. CREATE:  Creación de un producto (`[Post]`)](#1221-create--creación-de-un-producto-post)
        - [12.2.2. READ: Lectura de productos (`[Get]`)](#1222-read-lectura-de-productos-get)
        - [12.2.3. UPDATE:    Actualización de un producto (`[Put]`)](#1223-update----actualización-de-un-producto-put)
        - [12.2.4. DELETE:  Eliminación de un producto (`[Delete]`)](#1224-delete--eliminación-de-un-producto-delete)
      - [12.3. Manejo de respuestas y errores](#123-manejo-de-respuestas-y-errores)
    - [13. Pruebas y herramientas](#13-pruebas-y-herramientas)
      - [13.1. Pruebas unitarias](#131-pruebas-unitarias)
        - [13.1.1. NUnit para pruebas unitarias](#1311-nunit-para-pruebas-unitarias)
        - [13.1.2. Moq para la creación de objetos simulados (mocks)](#1312-moq-para-la-creación-de-objetos-simulados-mocks)
        - [13.1.3. FluentAssertions para aserciones expresivas](#1313-fluentassertions-para-aserciones-expresivas)
    - [13.2. Gestión de proyectos y documentación](#132-gestión-de-proyectos-y-documentación)
        - [13.2.1. dotnet CLI y archivos . csproj](#1321-dotnet-cli-y-archivos--csproj)
        - [13.2.2. Documentación XML para el código](#1322-documentación-xml-para-el-código)
    - [14. Dockerización de aplicaciones .NET](#14-dockerización-de-aplicaciones-net)
      - [14.1. ¿Qué es Docker?](#141-qué-es-docker)
      - [14.2. Docker Compose](#142-docker-compose)
      - [14.3. Despliegue de aplicaciones .  NET](#143-despliegue-de-aplicaciones---net)
        - [14.3.1. Despliegue simple](#1431-despliegue-simple)
        - [14.3.2. Despliegue con un Dockerfile multi-etapa](#1432-despliegue-con-un-dockerfile-multi-etapa)
      - [14.4. Testcontainers y Docker](#144-testcontainers-y-docker)

---

### 1. Introducción al lenguaje de programación C#

#### 1.1. ¿Qué es C# y por qué usarlo?

C# (pronunciado "C Sharp") es un lenguaje de programación de propósito general, orientado a objetos y de tipado estático, desarrollado por Microsoft como parte de la plataforma .NET.  Está diseñado para ser moderno, seguro y eficiente, con un equilibrio perfecto entre productividad y rendimiento.  Sus principales características son:

- **Orientado a objetos y funcional**: C# soporta programación orientada a objetos pura, pero también incorpora características funcionales avanzadas como expresiones lambda, LINQ y pattern matching.
- **Multiplataforma**: Gracias a **.NET**, el código C# puede ejecutarse en Windows, macOS, Linux, dispositivos móviles (iOS/Android con . NET MAUI) y en la web (con Blazor).
- **Seguro y tipado**: El sistema de tipos fuerte y las características de seguridad nula (nullable reference types) ayudan a prevenir errores comunes en tiempo de compilación.
- **Alto rendimiento**: Con . NET 10, el runtime ha sido optimizado para ofrecer rendimientos comparables a lenguajes de bajo nivel. 
- **Ecosistema rico**: Cuenta con un enorme ecosistema de librerías, frameworks (ASP.NET Core, Entity Framework, etc.) y herramientas de desarrollo de clase mundial. 
- **Evolución constante**: C# evoluciona rápidamente, con nuevas características en cada versión que mejoran la expresividad y productividad del desarrollador.

#### 1.2. El proceso de compilación y ejecución: El papel del CLR

Comprender este proceso es fundamental para entender cómo funciona . NET.  C# utiliza un proceso de compilación en dos etapas, similar al modelo de Java pero con optimizaciones adicionales. 

1. **Compilación**: El código fuente, escrito en un archivo con extensión `.cs`, es procesado por el **compilador de C# (Roslyn)**. Este paso verifica la sintaxis, realiza análisis de tipos y transforma el código legible para humanos en un formato intermedio llamado **CIL (Common Intermediate Language)**, anteriormente conocido como MSIL.  Este código intermedio se almacena en archivos con extensión `.dll` o `.exe`. El CIL no es específico de una plataforma, lo que permite la portabilidad de las aplicaciones . NET. 

   **Ejemplo**:
   Supongamos que tienes un archivo `HolaMundo.cs`:

   ````csharp name=HolaMundo.cs
   namespace MiAplicacion;

   class HolaMundo
   {
       static void Main(string[] args)
       {
           Console.WriteLine("¡Hola, mundo!");
       }
   }
   ````

   Para compilarlo, usas el comando del CLI de .NET: 

   ```bash
   dotnet build
   ```

   Esto creará un archivo `MiAplicacion.dll` en la carpeta `bin/Debug/net10. 0/`.

2. **Ejecución**: El archivo compilado (`.dll` o `.exe`) contiene código CIL que es interpretado y ejecutado por el **Common Language Runtime (CLR)**. El CLR es el corazón de .NET, equivalente a la JVM de Java.  En tiempo de ejecución, el CLR utiliza un compilador **Just-In-Time (JIT)** llamado **RyuJIT** que convierte el CIL a código máquina nativo específico del sistema operativo y arquitectura del procesador.  Este proceso de compilación JIT ocurre solo la primera vez que se ejecuta un método, y el código nativo se almacena en caché para ejecuciones posteriores.

   **Ejemplo**:
   Para ejecutar el programa compilado, usas:

   ```bash
   dotnet run
   ```

   El CLR cargará el CIL, lo compilará a código nativo y lo ejecutará, mostrando la salida: 

   ```
   ¡Hola, mundo!
   ```

En resumen, el proceso de dos etapas de C# (compilación a CIL y luego compilación JIT a código nativo por el CLR) es lo que le otorga su independencia de plataforma y su alto rendimiento, haciéndolo un lenguaje robusto y versátil para el desarrollo moderno. 

---

### 2. Fundamentos de la programación en C#

#### 2.1.  Sintaxis básica, tipos de datos y estructuras de control

- **Tipos de datos**:  C# es un lenguaje fuertemente tipado con un rico sistema de tipos. 
  - **Tipos por valor (Value Types)**: Almacenan el valor directamente en la pila (stack) y **no pueden ser nulos por defecto**. Incluyen tipos primitivos como `int`, `double`, `bool`, `char`, y estructuras (`struct`).
    ```csharp
    int edad = 30;
    double precio = 19.99;
    bool esActivo = true;
    ```
  - **Tipos por valor anulables (Nullable Value Types)**: Para permitir que un tipo por valor sea nulo, se usa `?` después del tipo.
    ```csharp
    int? edadNula = null;
    double? altura = 1.75;
    ```
  - **Tipos por referencia (Reference Types)**: Almacenan una referencia al objeto en el heap y **pueden ser nulos**. El más común es `string`, que es una clase.
    ```csharp
    string nombre = "Juan";
    string? apellido = null; // En C# 10+ con nullable reference types habilitado
    ```

- **Estructuras de control**:  Dirigen el flujo de tu código.
  - **Condicional `if-else`**:
    ```csharp
    int nota = 7;
    if (nota >= 5)
    {
        Console.WriteLine("Aprobado");
    }
    else
    {
        Console.WriteLine("Suspendido");
    }
    // Salida: Aprobado
    ```
  
  - **Expresiones `switch` modernas**:  C# ofrece switch expressions muy poderosas con pattern matching. 
    ```csharp
    int dia = 3;
    string nombreDia = dia switch
    {
        1 => "Lunes",
        2 => "Martes",
        3 => "Miércoles",
        _ => "Día no válido"
    };
    Console.WriteLine(nombreDia); // Salida: Miércoles
    ```

  - **Bucle `for`**:
    ```csharp
    for (int i = 0; i < 3; i++)
    {
        Console.WriteLine($"Iteración número:  {i}");
    }
    // Salida: Iteración número: 0, Iteración número: 1, Iteración número: 2
    ```

  - **Bucle `foreach`**: Ideal para iterar sobre colecciones de forma simple.
    ```csharp
    string[] nombres = { "Ana", "Pedro", "Luis" };
    foreach (string nombre in nombres)
    {
        Console.WriteLine($"Hola, {nombre}");
    }
    // Salida: Hola, Ana; Hola, Pedro; Hola, Luis
    ```

  - **Bucle `while`**:
    ```csharp
    int contador = 0;
    while (contador < 2)
    {
        Console. WriteLine($"Bucle while, iteración: {contador}");
        contador++;
    }
    // Salida: Bucle while, iteración: 0; Bucle while, iteración:  1
    ```

  - **Bucle `do-while`**:
    ```csharp
    int contador = 5;
    do
    {
        Console.WriteLine($"El contador es: {contador}");
        contador++;
    } while (contador < 3);
    // Salida: El contador es: 5
    ```

#### 2.2. Arrays:  Tipos compuestos

Los arrays te permiten almacenar múltiples valores del mismo tipo.  Una vez creados, su tamaño es fijo.

- **Arrays unidimensionales**:
  ```csharp
  // Creación e inicialización
  int[] numeros = new int[3];
  numeros[0] = 10;
  
  // Inicialización con valores
  string[] frutas = { "Manzana", "Naranja" };
  Console.WriteLine(frutas[0]); // Salida: Manzana
  ```

- **Arrays multidimensionales**:
  ```csharp
  // Matriz rectangular 2x3
  int[,] matriz = {
      { 1, 2, 3 },
      { 4, 5, 6 }
  };
  Console.WriteLine(matriz[0, 1]); // Salida: 2
  ```

- **Arrays escalonados (Jagged Arrays)**:
  ```csharp
  // Array de arrays
  int[][] matrizEscalonada = new int[2][];
  matrizEscalonada[0] = new int[] { 1, 2, 3 };
  matrizEscalonada[1] = new int[] { 4, 5 };
  Console.WriteLine(matrizEscalonada[1][0]); // Salida: 4
  ```

#### 2.3. Modificadores de acceso:  `public`, `private` y `protected`

Estos modificadores controlan la **visibilidad** de los miembros de una clase, un concepto fundamental para la **encapsulación**.

- `public`: Accesible desde cualquier lugar.
- `private`: Solo accesible dentro de la misma clase (es el valor por defecto para miembros).
- `protected`: Accesible en la misma clase y en clases derivadas. 
- `internal`: Accesible solo dentro del mismo ensamblado (. dll).
- `protected internal`: Combinación de `protected` e `internal`.
- `private protected`: Accesible solo en la clase o en derivadas dentro del mismo ensamblado. 

#### 2.4. Modificadores de comportamiento: `static` y `readonly`/`const`

- **`static`**: El miembro pertenece al **tipo**, no a una instancia individual.  Hay una única copia compartida. 
  ```csharp
  public class Contador
  {
      public static int TotalObjetos = 0;
      
      public Contador()
      {
          TotalObjetos++;
      }
  }
  
  var c1 = new Contador();
  var c2 = new Contador();
  Console.WriteLine(Contador.TotalObjetos); // Salida: 2
  ```

- **`const`**: Define una constante en tiempo de compilación.  Debe ser inicializada en la declaración y es implícitamente `static`.
  ```csharp
  public const double Pi = 3.14159;
  ```

- **`readonly`**: Define un campo que solo puede ser asignado en la declaración o en el constructor. Puede tener diferentes valores para diferentes instancias.
  ```csharp
  public class Configuracion
  {
      public readonly string RutaBase;
      
      public Configuracion(string ruta)
      {
          RutaBase = ruta;
      }
  }
  ```

---

### 3. Programación orientada a objetos (POO)

La POO es un paradigma de programación que utiliza el concepto de "objetos" para modelar el mundo real.  En lugar de escribir una serie de instrucciones secuenciales, se organizan los datos y el comportamiento en unidades lógicas. 

#### 3.1. Clases, objetos, propiedades y métodos

- **Clase**: Es el "plano" o la plantilla a partir de la cual se crean los objetos.  Define la estructura de los objetos, incluyendo sus **propiedades** (las características o datos) y **métodos** (el comportamiento o las acciones).
- **Objeto**: Es una instancia de una clase. Cada objeto tiene su propia copia de las propiedades definidas en la clase.
- **Propiedad**: En C#, las propiedades son una característica del lenguaje que proporciona un mecanismo flexible para leer, escribir o calcular valores de campos privados.  Son la forma idiomática de exponer datos en C#, reemplazando los getters y setters de Java.
- **Método**:  Una función que pertenece a una clase y define el comportamiento de un objeto. 

#### 3.2. Constructores, propiedades y Primary Constructors

Estos componentes son la base para el manejo y la **encapsulación** de los datos de un objeto.

- **Constructores**: Métodos especiales que se ejecutan cuando se crea un nuevo objeto. Su propósito principal es **inicializar las propiedades** del objeto. 

- **Propiedades**: En C#, las propiedades reemplazan el concepto de getters y setters de Java.  Utilizan los accesorios `get` y `set` para controlar el acceso a los campos privados.

- **Primary Constructors** (C# 12+): Una nueva característica que permite definir parámetros de constructor directamente en la declaración de la clase, reduciendo el código repetitivo.

**Ejemplo con sintaxis moderna de C# 14:**

````csharp name=Coche.cs
namespace MiAplicacion;

// Clase con file-scoped namespace (C# 10+)
public class Coche(string marca, string modelo)
{
    // Propiedades auto-implementadas con inicialización
    public string Marca { get; init; } = marca;
    public string Modelo { get; init; } = modelo;
    public int Velocidad { get; private set; } = 0;

    // Propiedad con lógica en el setter
    public int VelocidadMaxima { get; set; } = 200;

    // Métodos con lógica de negocio
    public void Acelerar(int incremento)
    {
        Velocidad += incremento;
        if (Velocidad > VelocidadMaxima)
            Velocidad = VelocidadMaxima;
            
        Console.WriteLine($"El coche {Marca} ha acelerado a {Velocidad} km/h.");
    }

    public void Frenar(int decremento)
    {
        Velocidad -= decremento;
        if (Velocidad < 0)
            Velocidad = 0;
            
        Console.WriteLine($"El coche {Marca} ha frenado a {Velocidad} km/h.");
    }
}

// Uso en otra clase
public class Concesionario
{
    public static void Main(string[] args)
    {
        // Crear un objeto usando el constructor
        var miCoche = new Coche("Ford", "Focus");

        // Usar las propiedades para obtener el estado
        Console.WriteLine($"Mi coche es un {miCoche.Marca} {miCoche.Modelo}");
        
        // Usar métodos para cambiar el estado
        miCoche.Acelerar(50);
        miCoche.Frenar(10);
        
        // La propiedad Velocidad tiene un setter privado
        Console.WriteLine($"Velocidad actual: {miCoche.Velocidad}");
    }
}
````

**Ejemplo con Primary Constructor (C# 12+)**:

````csharp
// Primary Constructor:  los parámetros se declaran en la definición de la clase
public class Producto(string nombre, decimal precio, string categoria)
{
    // Las propiedades pueden inicializarse directamente con los parámetros del primary constructor
    public string Nombre { get; init; } = nombre;
    public decimal Precio { get; init; } = precio;
    public string Categoria { get; init; } = categoria;

    // El cuerpo del constructor primario se ejecuta implícitamente
    public override string ToString() => $"{Nombre} - {Precio:C} ({Categoria})";
}
````

#### 3.3. Principios de la POO

Estos cuatro pilares son fundamentales para diseñar software de forma eficiente, escalable y mantenible. 

- **3.3.1. Encapsulamiento**: Agrupar datos y métodos en una clase, ocultando la implementación interna mediante modificadores de acceso y propiedades. 

  **Ejemplo**:
  ````csharp
  public class CuentaBancaria(decimal saldoInicial)
  {
      // Campo privado, no accesible directamente
      private decimal _saldo = saldoInicial;

      // Propiedad de solo lectura
      public decimal Saldo => _saldo;

      // Método para modificar el saldo de forma segura
      public void Depositar(decimal monto)
      {
          if (monto > 0)
              _saldo += monto;
      }
  }
  ````

- **3.3.2. Herencia**: Permite a una clase derivada heredar miembros de una clase base usando `:`.

  **Ejemplo**:
  ````csharp
  public class Vehiculo
  {
      protected int Ruedas { get; set; }
      
      public void Pitar()
      {
          Console. WriteLine("¡Pi-pi!");
      }
  }

  // La clase Coche hereda de Vehiculo
  public class Coche :  Vehiculo
  {
      public Coche()
      {
          Ruedas = 4;
      }
  }
  ````

- **3.3.3. Polimorfismo**: Permite que objetos de diferentes clases sean tratados como objetos de una clase base común.  Se logra mediante la **sobrescritura de métodos** (`virtual`/`override`).

  **Ejemplo**:
  ````csharp
  public class Animal
  {
      public virtual void Sonido()
      {
          Console.WriteLine("El animal hace un sonido.");
      }
  }

  public class Gato : Animal
  {
      public override void Sonido()
      {
          Console.WriteLine("El gato maúlla.");
      }
  }

  public class Perro : Animal
  {
      public override void Sonido()
      {
          Console.WriteLine("El perro ladra.");
      }
  }

  // Uso polimórfico
  Animal miAnimal = new Gato();
  miAnimal.Sonido(); // Salida: El gato maúlla. 

  miAnimal = new Perro();
  miAnimal.Sonido(); // Salida: El perro ladra.
  ````

- **3.3.4. Abstracción**:  Mostrar solo los detalles esenciales, ocultando la complejidad mediante clases abstractas e interfaces.

#### 3.4. Interfaces y clases abstractas

- **Clases abstractas**: No se pueden instanciar directamente y pueden contener métodos `abstract` (sin implementación) y métodos concretos. 

  **Ejemplo**: 
  ````csharp
  public abstract class FiguraGeometrica
  {
      public abstract double CalcularArea(); // Método abstracto
      
      public void MostrarMensaje() // Método concreto
      {
          Console.WriteLine("Esto es una figura geométrica.");
      }
  }

  public class Circulo(double radio) : FiguraGeometrica
  {
      private double Radio { get; } = radio;

      public override double CalcularArea()
      {
          return Math.PI * Radio * Radio;
      }
  }
  ````

- **Interfaces**: Definen un contrato que las clases deben cumplir. En C# 8+, pueden tener implementaciones por defecto.

  **Ejemplo**:
  ````csharp
  public interface IVolador
  {
      void Despegar();
      void Aterrizar();
  }

  public class Avion : IVolador
  {
      public void Despegar()
      {
          Console.WriteLine("El avión está despegando.");
      }

      public void Aterrizar()
      {
          Console. WriteLine("El avión está aterrizando.");
      }
  }
  ````

#### 3.5. Clases `abstract` y `sealed`

- **Clases `abstract`**: Ya explicadas, su propósito es ser heredadas. 

- **Clases `sealed`**: Impiden que otras clases hereden de ellas. Es el equivalente a `final` en Java.

  **Ejemplo**: 
  ````csharp
  public sealed class ClaseNoHeredable
  {
      // Esta clase no puede ser heredada
  }

  // La siguiente línea no compilaría: 
  // public class Derivada : ClaseNoHeredable { }
  ````

#### 3.6. Clases vs.  `Records`

- **Clases**: Son tipos de referencia mutables por defecto, adecuados para objetos con comportamiento complejo.

- **`Records`** (C# 9+): Son tipos de referencia inmutables por defecto, diseñados principalmente para almacenar datos.  El compilador genera automáticamente `Equals`, `GetHashCode`, `ToString`, y soportan comparación por valor.

  **Ejemplo**: 
  ````csharp
  // Record con sintaxis posicional
  public record PersonaRecord(string Nombre, int Edad);

  // Uso
  var persona1 = new PersonaRecord("Juan", 30);
  var persona2 = new PersonaRecord("Juan", 30);

  Console.WriteLine(persona1 == persona2); // Salida: True (comparación por valor)
  Console.WriteLine(persona1); // Salida: PersonaRecord { Nombre = Juan, Edad = 30 }

  // Los records soportan expresiones 'with' para crear copias modificadas
  var persona3 = persona1 with { Edad = 31 };
  ````

#### 3.7. El uso de tipos anulables y operadores null

C# tiene un rico conjunto de características para manejar valores nulos de forma segura: 

- **Tipos anulables**: Se declaran con `?` después del tipo (para tipos por valor) o habilitando nullable reference types (para tipos por referencia).

- **Operador de coalescencia nula (`??`)**: Devuelve el operando izquierdo si no es nulo; de lo contrario, devuelve el operando derecho. 
  ````csharp
  string? nombre = null;
  string nombreFinal = nombre ?? "Invitado";
  Console.WriteLine(nombreFinal); // Salida: Invitado
  ````

- **Operador de asignación de coalescencia nula (`??=`)**: Asigna el valor del operando derecho solo si el izquierdo es nulo.
  ````csharp
  string? apellido = null;
  apellido ??= "Desconocido";
  Console. WriteLine(apellido); // Salida: Desconocido
  ````

- **Operador de propagación nula (`?.`)**: Accede a miembros solo si el objeto no es nulo.
  ````csharp
  string? texto = null;
  int? longitud = texto?.Length;
  Console.WriteLine(longitud); // Salida: (vacío, es null)
  ````

- **Operador de asignación condicional nula (`??=` con `?. `)**: Se pueden combinar. 
  ````csharp
  string? mensaje = obtenerMensaje()?.ToUpper() ?? "SIN MENSAJE";
  ````

### 3.8. El uso de genéricos

Los **genéricos** permiten escribir código reutilizable que funciona con diferentes tipos de datos mientras mantiene la seguridad de tipos en tiempo de compilación.

#### ¿Por qué son necesarios los genéricos?

Sin genéricos, las colecciones almacenarían objetos del tipo `object`, perdiendo información de tipo y requiriendo conversiones explícitas. 

**Con genéricos:**
````csharp
List<string> listaString = new();
listaString.Add("Hola");
// listaString. Add(123); // Error de compilación
string s = listaString[0]; // No se necesita conversión
````

#### Clases y métodos genéricos

**Clase genérica:**
````csharp
public class Caja<T>
{
    private T _contenido;

    public Caja(T contenido)
    {
        _contenido = contenido;
    }

    public T Contenido => _contenido;
}

// Uso
var cajaDeTexto = new Caja<string>("Un mensaje");
string mensaje = cajaDeTexto. Contenido;

var cajaDeNumero = new Caja<int>(100);
int numero = cajaDeNumero.Contenido;
````

**Método genérico:**
````csharp
public class Utilidades
{
    public static void ImprimirArray<T>(T[] array)
    {
        foreach (var elemento in array)
        {
            Console.Write($"{elemento} ");
        }
        Console.WriteLine();
    }
}

// Uso
int[] numeros = { 1, 2, 3 };
string[] nombres = { "Ana", "Luis", "Carlos" };

Utilidades.ImprimirArray(numeros);
Utilidades.ImprimirArray(nombres);
````

#### Restricciones de tipos (Generic Constraints)

Puedes restringir los tipos que pueden ser usados como parámetros genéricos usando la palabra clave `where`.

````csharp
// T debe ser un tipo por referencia
public class Contenedor<T> where T : class
{
    public T?  Valor { get; set; }
}

// T debe derivar de Number
public T SumarNumeros<T>(T a, T b) where T : INumber<T>
{
    return a + b;
}

// Múltiples restricciones
public class Repositorio<T> where T : class, new()
{
    public T Crear() => new T();
}
````

**Restricciones comunes:**
- `where T : struct` - T debe ser un tipo por valor
- `where T : class` - T debe ser un tipo por referencia
- `where T : new()` - T debe tener un constructor sin parámetros
- `where T : BaseClass` - T debe derivar de BaseClass
- `where T : IInterface` - T debe implementar IInterface

---

### 4. Manejo de errores y excepciones

En . NET, una **excepción** es un objeto que representa un error o una situación excepcional que ocurre durante la ejecución de un programa.

#### 4.1. Tipos de excepciones en . NET

A diferencia de Java, C# no tiene excepciones checked (comprobadas). Todas las excepciones derivan de `System.Exception` y no es obligatorio declararlas en la firma del método.

- **Excepciones comunes del sistema**:
  - `ArgumentException`: Se lanza cuando un argumento de método no es válido. 
  - `ArgumentNullException`: Especialización para argumentos nulos.
  - `InvalidOperationException`: La operación no es válida en el estado actual del objeto.
  - `NullReferenceException`: Se intenta acceder a un miembro de un objeto nulo. 
  - `IndexOutOfRangeException`: Índice de array fuera de los límites. 

**Ejemplo**:
````csharp
int[] numeros = { 1, 2, 3 };
Console.WriteLine(numeros[5]); // Lanza IndexOutOfRangeException
````

#### 4.2. Manejo de excepciones:  `try-catch-finally`

La estructura es similar a Java, pero C# no requiere capturar excepciones específicas si no se desea.

**Ejemplo completo:**
````csharp
try
{
    int resultado = 10 / 0; // Lanza DivideByZeroException
    Console.WriteLine(resultado);
}
catch (DivideByZeroException ex)
{
    Console.WriteLine($"Error: No se puede dividir entre cero.  {ex.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"Ocurrió un error inesperado: {ex.Message}");
}
finally
{
    Console.WriteLine("Este bloque siempre se ejecuta.");
}
````

**Filtros de excepción (Exception Filters)**:
C# permite agregar condiciones a los bloques catch: 

````csharp
try
{
    // código que puede fallar
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.NotFound)
{
    Console.WriteLine("Recurso no encontrado");
}
catch (HttpRequestException ex) when (ex.StatusCode == HttpStatusCode.Unauthorized)
{
    Console.WriteLine("No autorizado");
}
````

#### 4.3. El uso de `using` y `IDisposable`

En . NET, la declaración `using` garantiza que los recursos que implementan `IDisposable` se liberen correctamente, incluso si ocurre una excepción.  Es el equivalente al `try-with-resources` de Java.

**Ejemplo tradicional:**
````csharp
using (var reader = new StreamReader("archivo.txt"))
{
    string contenido = reader.ReadToEnd();
    Console.WriteLine(contenido);
} // El reader se cierra automáticamente aquí
````

**Sintaxis moderna con `using` declaration** (C# 8+):
````csharp
using var reader = new StreamReader("archivo. txt");
string contenido = reader.ReadToEnd();
Console.WriteLine(contenido);
// El reader se cierra automáticamente al final del alcance (scope)
````

#### 4.4. Creación de excepciones personalizadas

Para crear una excepción personalizada, hereda de `Exception` o de una excepción más específica. 

**Ejemplo:**
````csharp
public class SaldoInsuficienteException : Exception
{
    public decimal SaldoActual { get; }
    public decimal MontoSolicitado { get; }

    public SaldoInsuficienteException(string mensaje, decimal saldoActual, decimal montoSolicitado)
        : base(mensaje)
    {
        SaldoActual = saldoActual;
        MontoSolicitado = montoSolicitado;
    }
}

public class CuentaBancaria(decimal saldoInicial)
{
    private decimal _saldo = saldoInicial;

    public void Retirar(decimal monto)
    {
        if (monto > _saldo)
        {
            throw new SaldoInsuficienteException(
                "No hay suficiente saldo para el retiro",
                _saldo,
                monto
            );
        }
        
        _saldo -= monto;
        Console.WriteLine($"Retiro exitoso. Saldo actual: {_saldo:C}");
    }
}

// Uso
var cuenta = new CuentaBancaria(100);
try
{
    cuenta.Retirar(150);
}
catch (SaldoInsuficienteException ex)
{
    Console.WriteLine($"Error: {ex.Message}");
    Console.WriteLine($"Saldo:  {ex.SaldoActual:C}, Solicitado: {ex.MontoSolicitado:C}");
}
````

---

### 5. Programación funcional

La **programación funcional** en C# se ha fortalecido significativamente con cada versión del lenguaje. C# 14 y . NET 10 ofrecen herramientas avanzadas para escribir código funcional expresivo y seguro.

#### 5.1. Funciones de primera clase y funciones de orden superior

En C#, los métodos pueden ser tratados como valores mediante **delegados**, permitiendo: 
- Asignarlos a variables
- Pasarlos como parámetros
- Devolverlos como resultados

**Funciones de orden superior**:  Son funciones que operan sobre otras funciones.

**Ejemplo:**
````csharp
// Predicate<T> es un delegado que toma un T y devuelve un bool
public static Predicate<int> EsMayorQue(int numero)
{
    return n => n > numero;
}

// Uso de la función de orden superior
var mayorQueDiez = EsMayorQue(10);
Console.WriteLine(mayorQueDiez(15)); // Salida: True
Console.WriteLine(mayorQueDiez(5));  // Salida: False
````

#### 5.2. Expresiones lambda, delegados y `Func`/`Action`

**Delegados** son tipos que representan referencias a métodos.  . NET proporciona delegados genéricos predefinidos: 

- **`Func<T1, T2, .. ., TResult>`**: Representa un método que devuelve un valor. 
- **`Action<T1, T2, ... >`**: Representa un método que no devuelve valor (void).
- **`Predicate<T>`**: Representa un método que toma un T y devuelve bool.

**Ejemplos:**
````csharp
// Func que toma dos ints y devuelve un int
Func<int, int, int> suma = (a, b) => a + b;
Console.WriteLine(suma(5, 3)); // Salida: 8

// Action que toma un string y no devuelve nada
Action<string> imprimir = mensaje => Console.WriteLine(mensaje);
imprimir("¡Hola desde Action!");

// Predicate que verifica si un número es par
Predicate<int> esPar = n => n % 2 == 0;
Console.WriteLine(esPar(4)); // Salida: True
````

**Expresiones lambda con bloques:**
````csharp
Func<int, int, string> compararNumeros = (a, b) =>
{
    if (a > b)
        return $"{a} es mayor que {b}";
    else if (a < b)
        return $"{a} es menor que {b}";
    else
        return "Son iguales";
};

Console.WriteLine(compararNumeros(10, 5));
````

#### 5.3. Inmutabilidad y ausencia de efectos secundarios

La inmutabilidad es un pilar de la programación funcional. En C#, puedes lograrla mediante:

- **Propiedades de solo lectura** (`{ get; }`  o `{ get; init; }`)
- **Records inmutables**
- **Colecciones inmutables** del namespace `System.Collections.Immutable`

**Código imperativo (mutable):**
````csharp
var nombres = new List<string> { "Ana", "Juan" };
nombres.Add("Pedro"); // Modifica la lista original
````

**Código funcional (inmutable):**
````csharp
using System.Collections.Immutable;

var nombresInmutables = ImmutableList. Create("Ana", "Juan");
var nuevosNombres = nombresInmutables.Add("Pedro");

Console.WriteLine(nombresInmutables. Count); // Salida: 2
Console.WriteLine(nuevosNombres.Count);     // Salida: 3
````

**Funciones puras**: Dado el mismo input, siempre producen el mismo output sin efectos secundarios.

````csharp
// Función pura
public static int Sumar(int a, int b) => a + b;

// Función impura (tiene efectos secundarios)
int contador = 0;
public int IncrementarContador()
{
    contador++; // Modifica estado externo
    return contador;
}
````

#### 5.4. Composición de funciones

C# permite componer funciones para crear operaciones más complejas de forma elegante, especialmente con LINQ.

**Ejemplo:**
````csharp
var productos = new List<Producto>
{
    new("Laptop", 1200, "Electrónica"),
    new("Ratón", 25, "Electrónica"),
    new("Libro", 15, "Hogar")
};

var resultado = productos
    .Where(p => p.Categoria == "Electrónica")  // Filtrado
    .Select(p => p.Nombre)                      // Mapeo
    .OrderBy(nombre => nombre)                  // Ordenación
    .ToList();                                  // Materialización

resultado.ForEach(Console.WriteLine);
// Salida: Laptop, Ratón
````

**Composición manual de funciones:**
````csharp
// Función que compone dos funciones
public static Func<T, TResult> Componer<T, TIntermediate, TResult>(
    Func<T, TIntermediate> func1,
    Func<TIntermediate, TResult> func2)
{
    return x => func2(func1(x));
}

// Uso
Func<int, int> duplicar = x => x * 2;
Func<int, int> sumarDiez = x => x + 10;

var duplicarYSumarDiez = Componer(duplicar, sumarDiez);
Console.WriteLine(duplicarYSumarDiez(5)); // Salida: 20 (5*2 + 10)
````

---

### 6. Colecciones y LINQ

El **Framework de Colecciones** de . NET proporciona estructuras de datos dinámicas para agrupar objetos.  **LINQ (Language Integrated Query)** es una de las características más poderosas de C#, que te permite procesar esas colecciones de manera declarativa, concisa y expresiva, integrando capacidades de consulta directamente en el lenguaje.

#### 6.1. Estructuras de datos:  Listas, conjuntos y diccionarios

Las colecciones son la base para almacenar y organizar datos. A diferencia de los arrays, su tamaño puede cambiar dinámicamente.  Las tres interfaces principales son:

- **`List<T>`**: Una colección **ordenada** que permite **elementos duplicados**.  Se accede a los elementos por su índice. 

  - **Cuándo usarla**:  Cuando el orden de los elementos es importante y los duplicados son aceptables (ej., una lista de la compra, el historial de navegación).
  
  ```csharp
  var frutas = new List<string>();
  frutas.Add("Manzana");
  frutas.Add("Naranja");
  frutas.Add("Manzana"); // Duplicado permitido
  Console.WriteLine(string.Join(", ", frutas)); 
  // Salida:  Manzana, Naranja, Manzana
  ```

- **`HashSet<T>`**: Una colección que **no permite elementos duplicados** y no garantiza un orden específico.  Ofrece rendimiento O(1) para operaciones de búsqueda, inserción y eliminación.

  - **Cuándo usarla**: Cuando necesitas una colección de elementos únicos (ej., nombres de usuario únicos, palabras únicas de un texto).
  
  ```csharp
  var nombresUnicos = new HashSet<string>();
  nombresUnicos.Add("Ana");
  nombresUnicos.Add("Juan");
  nombresUnicos.Add("Ana"); // Ignorado por ser duplicado
  Console.WriteLine(string.Join(", ", nombresUnicos)); 
  // Salida: Ana, Juan (el orden puede variar)
  ```

- **`Dictionary<TKey, TValue>`**: Una estructura que almacena pares de **clave-valor**, donde cada clave es única. 

  - **Cuándo usarla**: Cuando necesitas buscar un valor a partir de una clave única (ej., el DNI de una persona para obtener sus datos, el código de un producto para ver su precio).
  
  ```csharp
  var notas = new Dictionary<string, int>();
  notas["Juan"] = 9;
  notas["Ana"] = 10;
  Console.WriteLine(notas["Juan"]); // Salida: 9
  
  // Acceso seguro con TryGetValue
  if (notas.TryGetValue("Pedro", out int notaPedro))
  {
      Console.WriteLine(notaPedro);
  }
  else
  {
      Console.WriteLine("Pedro no encontrado");
  }
  ```

**Otras colecciones útiles:**

- **`Queue<T>`**: Cola FIFO (First In, First Out)
- **`Stack<T>`**: Pila LIFO (Last In, First Out)
- **`SortedSet<T>`**: Conjunto ordenado
- **`SortedDictionary<TKey, TValue>`**: Diccionario ordenado por claves
- **Colecciones inmutables**: `ImmutableList<T>`, `ImmutableHashSet<T>`, etc.

#### 6.2. LINQ:  Procesamiento de datos de forma declarativa

**LINQ** permite escribir consultas sobre colecciones usando una sintaxis declarativa similar a SQL.  El código se enfoca en el "qué" se quiere lograr, en lugar del "cómo" (el enfoque imperativo de los bucles `for`).

LINQ se puede usar de dos formas: 
1. **Sintaxis de métodos** (Method Syntax): Usando métodos de extensión
2. **Sintaxis de consultas** (Query Syntax): Similar a SQL

Nos enfocaremos en la **sintaxis de métodos** por ser más común y expresiva en código moderno.

**Ejemplo de datos para las consultas:**

```csharp
public record Producto(string Nombre, decimal Precio, string Categoria);

var productos = new List<Producto>
{
    new("Laptop", 1200m, "Electrónica"),
    new("Ratón", 25m, "Electrónica"),
    new("Libro", 15m, "Hogar"),
    new("Teclado", 75m, "Electrónica"),
    new("Mesa", 200m, "Hogar")
};
```

##### Operaciones comunes con LINQ

- **`Where()`**: Filtra elementos que cumplen una condición. 

  ```csharp
  // Filtra los productos de la categoría "Electrónica"
  var electronica = productos
      .Where(p => p. Categoria == "Electrónica")
      .ToList();
      
  Console.WriteLine($"Productos de electrónica: {electronica.Count}");
  // Salida: Productos de electrónica:  3
  ```

- **`Select()`**: Transforma cada elemento en el flujo a otro tipo (proyección).

  ```csharp
  // Obtiene solo los nombres de los productos
  var nombres = productos
      .Select(p => p.Nombre)
      .ToList();
      
  Console.WriteLine(string.Join(", ", nombres));
  // Salida: Laptop, Ratón, Libro, Teclado, Mesa
  
  // Proyección a tipo anónimo
  var resumen = productos
      .Select(p => new { p.Nombre, PrecioConIVA = p.Precio * 1.21m })
      .ToList();
  ```

- **`OrderBy()` / `OrderByDescending()`**: Ordena los elementos. 

  ```csharp
  var productosOrdenados = productos
      .OrderBy(p => p. Precio)
      .ToList();
      
  // Ordenar por múltiples criterios
  var ordenadoMultiple = productos
      .OrderBy(p => p.Categoria)
      .ThenByDescending(p => p. Precio)
      .ToList();
  ```

- **`GroupBy()`**: Agrupa elementos por una propiedad común.  Devuelve un `IEnumerable<IGrouping<TKey, TElement>>`.

  ```csharp
  var productosPorCategoria = productos
      .GroupBy(p => p.Categoria);
      
  foreach (var grupo in productosPorCategoria)
  {
      Console.WriteLine($"\nCategoría: {grupo.Key}");
      foreach (var producto in grupo)
      {
          Console.WriteLine($"  - {producto.Nombre}: {producto.Precio:C}");
      }
  }
  
  // Proyectar los grupos a un tipo específico
  var resumenPorCategoria = productos
      .GroupBy(p => p. Categoria)
      .Select(g => new
      {
          Categoria = g.Key,
          Cantidad = g.Count(),
          PrecioTotal = g.Sum(p => p.Precio)
      })
      .ToList();
  ```

- **`First()` / `FirstOrDefault()` / `Single()` / `SingleOrDefault()`**: Obtiene elementos específicos.

  ```csharp
  // First: devuelve el primero o lanza excepción si no hay
  var primerProducto = productos.First();
  
  // FirstOrDefault: devuelve el primero o default(T) si no hay
  var productoBarato = productos
      .FirstOrDefault(p => p.Precio < 10);
  
  // Single: devuelve el único elemento o lanza excepción
  var laptop = productos. Single(p => p.Nombre == "Laptop");
  
  // SingleOrDefault: devuelve el único o default(T)
  var productoNoExiste = productos
      .SingleOrDefault(p => p. Nombre == "Tablet");
  ```

- **`Any()` / `All()` / `Contains()`**: Operaciones de verificación booleana.

  ```csharp
  // ¿Hay algún producto caro?
  bool hayProductosCautos = productos.Any(p => p.Precio > 1000);
  
  // ¿Todos los productos tienen precio positivo?
  bool todosPreciosPositivos = productos.All(p => p.Precio > 0);
  
  // ¿Contiene un producto específico?
  bool contieneRaton = productos.Any(p => p.Nombre == "Ratón");
  ```

- **`Aggregate()` / `Sum()` / `Average()` / `Min()` / `Max()` / `Count()`**: Operaciones de agregación.

  ```csharp
  // Suma de precios
  decimal precioTotal = productos. Sum(p => p.Precio);
  Console.WriteLine($"Precio total: {precioTotal:C}");
  
  // Precio promedio
  decimal precioPromedio = productos.Average(p => p.Precio);
  
  // Precio máximo y mínimo
  decimal precioMaximo = productos.Max(p => p.Precio);
  decimal precioMinimo = productos. Min(p => p.Precio);
  
  // Contar productos
  int cantidadElectronica = productos
      .Count(p => p. Categoria == "Electrónica");
  
  // Aggregate personalizado:  concatenar nombres
  string todosLosNombres = productos
      .Select(p => p.Nombre)
      .Aggregate((acumulado, nombre) => $"{acumulado}, {nombre}");
  ```

- **`Take()` / `Skip()` / `TakeWhile()` / `SkipWhile()`**: Operaciones de particionado.

  ```csharp
  // Toma los 3 primeros productos
  var primerosTres = productos.Take(3).ToList();
  
  // Omite los 2 primeros y toma el resto
  var saltarDos = productos.Skip(2).ToList();
  
  // Paginación
  int pageSize = 2;
  int pageNumber = 1; // Segunda página
  var paginaProductos = productos
      .Skip(pageNumber * pageSize)
      .Take(pageSize)
      .ToList();
  ```

- **`Distinct()` / `DistinctBy()`**: Elimina duplicados.

  ```csharp
  var categorias = productos
      .Select(p => p.Categoria)
      .Distinct()
      .ToList();
  // Salida: ["Electrónica", "Hogar"]
  
  // DistinctBy (C# 9+): Elimina duplicados por una clave
  var primerosDeCategoria = productos
      . DistinctBy(p => p.Categoria)
      .ToList();
  ```

- **`Join()` / `GroupJoin()`**: Une dos secuencias. 

  ```csharp
  public record Pedido(string ProductoNombre, int Cantidad);
  
  var pedidos = new List<Pedido>
  {
      new("Laptop", 2),
      new("Ratón", 5),
      new("Tablet", 1) // Este producto no existe
  };
  
  // Join:  unión interna
  var pedidosConPrecio = pedidos
      .Join(
          productos,
          pedido => pedido.ProductoNombre,
          producto => producto. Nombre,
          (pedido, producto) => new
          {
              pedido.ProductoNombre,
              pedido.Cantidad,
              producto.Precio,
              Total = pedido. Cantidad * producto.Precio
          }
      )
      .ToList();
  ```

- **`SelectMany()`**: Aplana colecciones anidadas.

  ```csharp
  var categorias = new List<(string Nombre, List<string> Productos)>
  {
      ("Electrónica", new List<string> { "Laptop", "Ratón" }),
      ("Hogar", new List<string> { "Libro", "Mesa" })
  };
  
  // Aplana la estructura para obtener todos los productos
  var todosLosProductos = categorias
      .SelectMany(c => c.Productos)
      .ToList();
  
  Console.WriteLine(string.Join(", ", todosLosProductos));
  // Salida: Laptop, Ratón, Libro, Mesa
  ```

**Encadenamiento complejo (el poder de LINQ):**

```csharp
// Ejemplo completo:  productos de electrónica caros, ordenados por precio
var resultado = productos
    .Where(p => p.Categoria == "Electrónica")      // Filtrado
    .Where(p => p.Precio > 50)                     // Filtrado adicional
    .OrderByDescending(p => p. Precio)              // Ordenación
    .Select(p => new                                // Proyección
    {
        p.Nombre,
        PrecioConDescuento = p.Precio * 0.9m
    })
    .Take(5)                                        // Limitar resultados
    .ToList();                                      // Materialización

resultado.ForEach(r => 
    Console.WriteLine($"{r.Nombre}:  {r.PrecioConDescuento:C}"));
```

#### 6.3. LINQ paralelo con `AsParallel()`

Una de las mayores ventajas de LINQ es la facilidad para procesar colecciones de forma paralela usando **PLINQ (Parallel LINQ)**. Simplemente añade `.AsParallel()` a la cadena de consultas.

```csharp
// Procesamiento paralelo para grandes colecciones
var productosCaros = productos
    .AsParallel()
    .Where(p => p.Precio > 100)
    .Select(p => new { p. Nombre, p.Precio })
    .ToList();

// El orden no está garantizado con AsParallel()
// Si necesitas orden, usa AsOrdered()
var productosOrdenadosParalelos = productos
    .AsParallel()
    .AsOrdered()
    .Where(p => p.Precio > 50)
    .ToList();
```

**⚠️ Advertencia**:  PLINQ solo mejora el rendimiento en colecciones grandes con operaciones costosas. Para colecciones pequeñas, la sobrecarga de la paralelización puede ser contraproducente.

#### 6.4. Analogía con SQL

La filosofía de LINQ es muy similar a la de las consultas **SQL**.  Aquí hay una comparación:

| **LINQ (C#)**                                  | **SQL**                                      | **Descripción**                                    |
|: -----------------------------------------------|:---------------------------------------------|:---------------------------------------------------|
| `productos.AsQueryable()`                      | `FROM productos`                             | Selecciona la fuente de datos.                      |
| `.Where(p => p.Precio > 100)`                  | `WHERE Precio > 100`                         | Filtra los registros.                              |
| `.Select(p => new { p.Nombre, p.Precio })`     | `SELECT Nombre, Precio`                      | Proyecta columnas específicas.                     |
| `.OrderBy(p => p.Precio)`                      | `ORDER BY Precio`                            | Ordena los resultados.                             |
| `.GroupBy(p => p.Categoria)`                   | `GROUP BY Categoria`                         | Agrupa los resultados.                             |
| `.Count()`                                     | `COUNT(*)`                                   | Cuenta los registros.                              |
| `.Sum(p => p.Precio)`                          | `SUM(Precio)`                                | Suma valores.                                      |
| `.Average(p => p.Precio)`                      | `AVG(Precio)`                                | Calcula el promedio.                               |
| `.Join(... )`                                   | `INNER JOIN`                                 | Une dos tablas/colecciones.                        |
| `.Take(10)`                                    | `LIMIT 10` / `TOP 10`                        | Limita el número de resultados.                    |

**Ejemplo de sintaxis de consulta (Query Syntax)** que se parece aún más a SQL:

```csharp
var consulta = from p in productos
               where p.Categoria == "Electrónica"
               orderby p.Precio descending
               select new { p.Nombre, p.Precio };

var resultado = consulta.ToList();
```

El uso de LINQ te permite trabajar con colecciones de una forma **declarativa**, lo que significa que tu código es más legible y se parece a una descripción de lo que quieres, en lugar de una secuencia de pasos.  Este es un cambio fundamental de los bucles tradicionales (imperativos) y un pilar del desarrollo moderno en C#.

---

### 7. E/S (Entrada/Salida) y manipulación de ficheros

La entrada y salida, o E/S, es fundamental para que tu aplicación interactúe con el mundo exterior, ya sea a través de la consola o del sistema de archivos. 

#### 7.1. Entrada y salida por consola

Para mostrar información en la consola, se usa la clase `Console`.

- `Console.WriteLine()`: Imprime una línea de texto con un salto de línea.
- `Console.Write()`: Imprime texto sin un salto de línea. 

**Métodos para crear mensajes de salida en C#:**

1. **Concatenación con `+`**: El método más simple, aunque menos legible. 
   ```csharp
   string nombre = "Ana";
   int edad = 25;
   Console.WriteLine("Hola, mi nombre es " + nombre + " y tengo " + edad + " años.");
   ```

2. **Interpolación de cadenas** (String Interpolation): El método **preferido** en C# moderno.  Se usa el prefijo `$` antes de las comillas.
   ```csharp
   string nombre = "Ana";
   int edad = 25;
   Console.WriteLine($"Hola, mi nombre es {nombre} y tengo {edad} años.");
   
   // Con formato
   decimal precio = 19.99m;
   Console. WriteLine($"El precio es: {precio:C}"); // Formato de moneda
   ```

3. **Raw String Literals** (C# 11+): Para cadenas multilínea sin caracteres de escape.
   ```csharp
   string json = """
       {
           "nombre": "Ana",
           "edad": 25
       }
       """;
   Console.WriteLine(json);
   ```

4. **Interpolación en Raw String Literals**:
   ```csharp
   string nombre = "Ana";
   int edad = 25;
   
   string mensaje = $$"""
       {
           "nombre": "{{nombre}}",
           "edad": {{edad}}
       }
       """;
   Console.WriteLine(mensaje);
   ```

**Entrada de datos por consola:**

```csharp
Console.Write("Introduce tu nombre: ");
string? nombreUsuario = Console.ReadLine();

// Validación segura con nullable reference types
if (! string.IsNullOrWhiteSpace(nombreUsuario))
{
    Console.WriteLine($"Tu nombre es: {nombreUsuario}");
}
```

#### 7.2. Lectura y escritura de ficheros de texto

Para manipular archivos de texto, . NET ofrece APIs en los namespaces `System.IO` y `System.IO.Abstractions`.

##### API de `System.IO` con `using`

**Escritura con `StreamWriter`**:

```csharp
using var writer = new StreamWriter("saludo.txt");
writer.WriteLine("Hola, este es un mensaje.");
writer.WriteLine("Escrito desde C#.");
Console.WriteLine("Archivo 'saludo.txt' creado con éxito.");
// El writer se cierra automáticamente al final del scope
```

**Lectura con `StreamReader`**:

```csharp
try
{
    using var reader = new StreamReader("saludo.txt");
    string? linea;
    Console.WriteLine("Contenido del archivo:");
    
    while ((linea = reader. ReadLine()) != null)
    {
        Console.WriteLine(linea);
    }
}
catch (FileNotFoundException)
{
    Console.WriteLine("El archivo no existe.");
}
catch (IOException ex)
{
    Console.WriteLine($"Error al leer el archivo: {ex.Message}");
}
```

##### API Moderna con `File` y LINQ

La clase estática `File` proporciona métodos de alto nivel para operaciones comunes:

**Escritura con `File.WriteAllText` y `File.WriteAllLines`**:

```csharp
string rutaArchivo = "saludo-moderno.txt";
string contenido = "Hola, este es un mensaje.\nEscrito con la API moderna de C#.";

try
{
    File.WriteAllText(rutaArchivo, contenido);
    Console.WriteLine($"Archivo '{rutaArchivo}' creado con éxito.");
    
    // También puedes escribir líneas
    string[] lineas = { "Línea 1", "Línea 2", "Línea 3" };
    File.WriteAllLines("lineas.txt", lineas);
}
catch (IOException ex)
{
    Console.WriteLine($"Error al escribir:  {ex.Message}");
}
```

**Lectura con `File.ReadAllLines` y LINQ**:

```csharp
try
{
    // Leer todas las líneas en un array
    string[] lineas = File.ReadAllLines(rutaArchivo);
    
    // Procesar con LINQ
    var lineasNoVacias = lineas
        . Where(l => !string.IsNullOrWhiteSpace(l))
        .Select((linea, indice) => $"{indice + 1}:  {linea}");
    
    foreach (var linea in lineasNoVacias)
    {
        Console.WriteLine(linea);
    }
}
catch (FileNotFoundException)
{
    Console.WriteLine("El archivo no existe.");
}
```

**Lectura eficiente con `File.ReadLines` (para archivos grandes)**:

```csharp
// ReadLines devuelve IEnumerable<string> que lee línea por línea
// No carga todo el archivo en memoria
try
{
    var lineasConHola = File.ReadLines("archivo-grande.txt")
        .Where(linea => linea.Contains("hola", StringComparison.OrdinalIgnoreCase))
        .Take(10);
    
    foreach (var linea in lineasConHola)
    {
        Console. WriteLine(linea);
    }
}
catch (IOException ex)
{
    Console.WriteLine($"Error:  {ex.Message}");
}
```

#### 7.3. Manejo de datos JSON con System.Text.Json

El namespace `System.Text.Json` es la API moderna y de alto rendimiento para trabajar con JSON en . NET.  Es preferible a Newtonsoft.Json (Json.NET) en nuevos proyectos.

**Serialización (objeto a JSON)**:

```csharp
using System.Text.Json;

public record Producto(string Nombre, decimal Precio, string Categoria);

var productos = new List<Producto>
{
    new("Teclado", 75.0m, "Electrónica"),
    new("Ratón", 25.0m, "Electrónica")
};

// Serializar a JSON con formato legible
var opciones = new JsonSerializerOptions 
{ 
    WriteIndented = true,
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase
};

string json = JsonSerializer. Serialize(productos, opciones);
Console.WriteLine(json);

// Guardar en archivo
File.WriteAllText("productos.json", json);
```

**Deserialización (JSON a objeto)**:

```csharp
try
{
    string jsonEntrada = File.ReadAllText("productos.json");
    
    var productosDeserializados = JsonSerializer
        .Deserialize<List<Producto>>(jsonEntrada);
    
    if (productosDeserializados != null)
    {
        foreach (var producto in productosDeserializados)
        {
            Console.WriteLine($"{producto.Nombre}: {producto. Precio:C}");
        }
    }
}
catch (JsonException ex)
{
    Console.WriteLine($"Error al deserializar JSON: {ex.Message}");
}
```

**Trabajar con propiedades personalizadas (atributos)**:

```csharp
using System.Text.Json.Serialization;

public record ProductoApi(
    [property: JsonPropertyName("product_name")] string Nombre,
    [property: JsonPropertyName("unit_price")] decimal Precio,
    [property: JsonPropertyName("category")] string Categoria
);

// El JSON se serializará con los nombres especificados
```

---
### 8. Bases de datos

El almacenamiento de datos es un pilar fundamental en la mayoría de las aplicaciones. En el ecosistema .NET 10, **Entity Framework Core** es el ORM (Object-Relational Mapper) estándar para bases de datos relacionales, mientras que el **MongoDB Driver** oficial proporciona acceso a bases de datos NoSQL.

#### 8.1. Entity Framework Core:  Acceso a datos con PostgreSQL

**Entity Framework Core (EF Core)** es un ORM moderno, ligero y extensible que permite trabajar con bases de datos usando objetos . NET.  En lugar de escribir SQL manualmente, defines modelos de datos como clases de C# y EF Core se encarga de traducir las operaciones a consultas SQL.

**Ventajas de EF Core:**
- **Independencia de la base de datos**:  Puedes cambiar de PostgreSQL a SQL Server con mínimos cambios. 
- **LINQ integrado**: Escribe consultas usando LINQ en lugar de SQL.
- **Migraciones**:  Evoluciona el esquema de la base de datos de forma controlada.
- **Tipado fuerte**: Errores detectados en tiempo de compilación.

**Instalación de paquetes NuGet necesarios:**

```bash
dotnet add package Npgsql.EntityFrameworkCore.PostgreSQL
dotnet add package Microsoft.EntityFrameworkCore.Design
```

#### 8.2. Configuración del DbContext y modelos

El **`DbContext`** es la clase principal para interactuar con la base de datos.  Representa una sesión con la base de datos y permite consultar y guardar instancias de las entidades.

**Modelo de datos (Entidad):**

```csharp
namespace MiAplicacion.Models;

public class Producto
{
    public int Id { get; set; }
    public required string Nombre { get; set; }
    public decimal Precio { get; set; }
    public required string Categoria { get; set; }
    public DateTime FechaCreacion { get; set; } = DateTime. UtcNow;
}
```

**Configuración del DbContext:**

```csharp
using Microsoft.EntityFrameworkCore;
using MiAplicacion.Models;

namespace MiAplicacion.Data;

public class ApplicationDbContext : DbContext
{
    public ApplicationDbContext(DbContextOptions<ApplicationDbContext> options)
        : base(options)
    {
    }

    // DbSet representa una tabla en la base de datos
    public DbSet<Producto> Productos => Set<Producto>();

    // Configuración adicional con Fluent API
    protected override void OnModelCreating(ModelBuilder modelBuilder)
    {
        base.OnModelCreating(modelBuilder);

        // Configurar la entidad Producto
        modelBuilder.Entity<Producto>(entity =>
        {
            // Nombre de la tabla
            entity.ToTable("productos");

            // Clave primaria
            entity.HasKey(p => p.Id);

            // Configuración de propiedades
            entity. Property(p => p.Nombre)
                .IsRequired()
                .HasMaxLength(200);

            entity.Property(p => p.Precio)
                .HasPrecision(18, 2);

            entity.Property(p => p.Categoria)
                .IsRequired()
                .HasMaxLength(100);

            // Índice para búsquedas rápidas
            entity.HasIndex(p => p.Categoria);
        });
    }
}
```

**Configuración de la cadena de conexión (appsettings.json):**

```json
{
  "ConnectionStrings": {
    "DefaultConnection": "Host=localhost;Port=5432;Database=tienda_db;Username=postgres;Password=mysecretpassword"
  }
}
```

**Registro del DbContext en Program.cs:**

```csharp
using Microsoft.EntityFrameworkCore;
using MiAplicacion.Data;

var builder = WebApplication.CreateBuilder(args);

// Registrar el DbContext con PostgreSQL
builder.Services.AddDbContext<ApplicationDbContext>(options =>
    options.UseNpgsql(
        builder.Configuration.GetConnectionString("DefaultConnection")
    )
);

var app = builder.Build();

// Asegurar que la base de datos está creada (solo para desarrollo)
using (var scope = app.Services.CreateScope())
{
    var context = scope.ServiceProvider.GetRequiredService<ApplicationDbContext>();
    context.Database.EnsureCreated();
}

app.Run();
```

**Crear y aplicar migraciones:**

```bash
# Crear una migración inicial
dotnet ef migrations add InitialCreate

# Aplicar las migraciones a la base de datos
dotnet ef database update
```

#### 8.3. Ejecución de consultas:  CRUD con EF Core

Una vez configurado el `DbContext`, las operaciones CRUD son muy sencillas y expresivas. 

**Repositorio genérico (patrón Repository):**

```csharp
namespace MiAplicacion. Repositories;

public interface IRepository<T> where T : class
{
    Task<List<T>> GetAllAsync();
    Task<T? > GetByIdAsync(int id);
    Task<T> AddAsync(T entity);
    Task UpdateAsync(T entity);
    Task DeleteAsync(int id);
}

public class ProductoRepository : IRepository<Producto>
{
    private readonly ApplicationDbContext _context;

    public ProductoRepository(ApplicationDbContext context)
    {
        _context = context;
    }

    // 1. CREATE (SAVE)
    public async Task<Producto> AddAsync(Producto producto)
    {
        _context.Productos.Add(producto);
        await _context.SaveChangesAsync();
        return producto;
    }

    // 2. READ (FIND ALL)
    public async Task<List<Producto>> GetAllAsync()
    {
        return await _context.Productos
            .OrderBy(p => p.Nombre)
            .ToListAsync();
    }

    // 3. READ (FIND BY ID)
    public async Task<Producto?> GetByIdAsync(int id)
    {
        return await _context. Productos
            .FirstOrDefaultAsync(p => p.Id == id);
    }

    // 4. UPDATE
    public async Task UpdateAsync(Producto producto)
    {
        _context. Productos.Update(producto);
        await _context.SaveChangesAsync();
    }

    // 5. DELETE
    public async Task DeleteAsync(int id)
    {
        var producto = await GetByIdAsync(id);
        if (producto != null)
        {
            _context.Productos.Remove(producto);
            await _context.SaveChangesAsync();
        }
    }

    // Consultas personalizadas con LINQ
    public async Task<List<Producto>> GetByCategoria(string categoria)
    {
        return await _context. Productos
            .Where(p => p.Categoria == categoria)
            .ToListAsync();
    }

    public async Task<List<Producto>> GetProductosCaros(decimal precioMinimo)
    {
        return await _context.Productos
            .Where(p => p.Precio > precioMinimo)
            .OrderByDescending(p => p. Precio)
            .ToListAsync();
    }
}
```

**Uso del repositorio:**

```csharp
public class ProductoService
{
    private readonly ProductoRepository _repository;

    public ProductoService(ProductoRepository repository)
    {
        _repository = repository;
    }

    public async Task EjemplosDeUso()
    {
        // CREATE
        var nuevoProducto = new Producto
        {
            Nombre = "Laptop Dell",
            Precio = 1200.00m,
            Categoria = "Electrónica"
        };
        await _repository.AddAsync(nuevoProducto);
        Console.WriteLine($"Producto creado con ID: {nuevoProducto.Id}");

        // READ ALL
        var todosLosProductos = await _repository. GetAllAsync();
        Console.WriteLine($"Total de productos: {todosLosProductos.Count}");

        // READ BY ID
        var producto = await _repository.GetByIdAsync(nuevoProducto.Id);
        if (producto != null)
        {
            Console.WriteLine($"Encontrado: {producto.Nombre}");
        }

        // UPDATE
        if (producto != null)
        {
            producto.Precio = 1150.00m;
            await _repository.UpdateAsync(producto);
            Console.WriteLine("Producto actualizado");
        }

        // DELETE
        await _repository.DeleteAsync(nuevoProducto.Id);
        Console.WriteLine("Producto eliminado");

        // CONSULTAS PERSONALIZADAS
        var electronicos = await _repository.GetByCategoria("Electrónica");
        var productosCaros = await _repository. GetProductosCaros(1000m);
    }
}
```

**Consultas LINQ complejas con EF Core:**

```csharp
// Consultas con joins, agrupaciones y proyecciones
public async Task<List<ResumenCategoria>> GetResumenPorCategoria()
{
    return await _context.Productos
        .GroupBy(p => p.Categoria)
        .Select(g => new ResumenCategoria
        {
            Categoria = g.Key,
            Cantidad = g.Count(),
            PrecioPromedio = g.Average(p => p.Precio),
            PrecioTotal = g.Sum(p => p.Precio)
        })
        .OrderByDescending(r => r.PrecioTotal)
        .ToListAsync();
}

// Consulta con paginación
public async Task<PaginatedResult<Producto>> GetProductosPaginados(
    int pageNumber, 
    int pageSize)
{
    var total = await _context.Productos. CountAsync();
    
    var productos = await _context. Productos
        .OrderBy(p => p.Nombre)
        .Skip((pageNumber - 1) * pageSize)
        .Take(pageSize)
        .ToListAsync();

    return new PaginatedResult<Producto>
    {
        Items = productos,
        TotalCount = total,
        PageNumber = pageNumber,
        PageSize = pageSize
    };
}

// Consulta SQL raw (cuando LINQ no es suficiente)
public async Task<List<Producto>> GetProductosConSqlRaw()
{
    return await _context.Productos
        .FromSqlRaw("SELECT * FROM productos WHERE precio > {0}", 100)
        .ToListAsync();
}
```

**Transacciones:**

```csharp
public async Task EjemploTransaccion()
{
    using var transaction = await _context.Database.BeginTransactionAsync();
    
    try
    {
        var producto1 = new Producto 
        { 
            Nombre = "Producto A", 
            Precio = 100m, 
            Categoria = "Test" 
        };
        
        var producto2 = new Producto 
        { 
            Nombre = "Producto B", 
            Precio = 200m, 
            Categoria = "Test" 
        };

        _context.Productos.Add(producto1);
        await _context.SaveChangesAsync();

        _context.Productos.Add(producto2);
        await _context.SaveChangesAsync();

        await transaction.CommitAsync();
        Console.WriteLine("Transacción completada con éxito");
    }
    catch (Exception ex)
    {
        await transaction.RollbackAsync();
        Console.WriteLine($"Error en la transacción: {ex. Message}");
        throw;
    }
}
```

**Seguimiento de cambios (Change Tracking):**

```csharp
// Consulta sin seguimiento (más rápida para consultas de solo lectura)
public async Task<List<Producto>> GetProductosNoTracking()
{
    return await _context.Productos
        . AsNoTracking()
        .ToListAsync();
}

// Actualización sin cargar la entidad primero
public async Task ActualizarPrecioDirecto(int id, decimal nuevoPrecio)
{
    await _context.Productos
        .Where(p => p.Id == id)
        .ExecuteUpdateAsync(setters => setters
            .SetProperty(p => p.Precio, nuevoPrecio));
}

// Eliminación sin cargar la entidad primero
public async Task EliminarPorCategoriaDirecto(string categoria)
{
    await _context.Productos
        .Where(p => p. Categoria == categoria)
        .ExecuteDeleteAsync();
}
```

### 8.4. MongoDB: Manejo de bases de datos NoSQL con API Tipada

**MongoDB** es una base de datos NoSQL orientada a documentos que almacena datos en formato BSON (Binary JSON). El driver oficial de MongoDB para .NET permite trabajar con colecciones tipadas de forma muy elegante.

**Instalación del paquete NuGet:**

```bash
dotnet add package MongoDB.Driver
```

#### Conexión con API Tipada y Configuración

**Modelo de datos:**

```csharp
using MongoDB. Bson;
using MongoDB. Bson.Serialization. Attributes;

namespace MiAplicacion.Models;

public class Producto
{
    [BsonId]
    [BsonRepresentation(BsonType.ObjectId)]
    public string?  Id { get; set; }

    [BsonElement("nombre")]
    [BsonRequired]
    public required string Nombre { get; set; }

    [BsonElement("precio")]
    public decimal Precio { get; set; }

    [BsonElement("categoria")]
    [BsonRequired]
    public required string Categoria { get; set; }

    [BsonElement("fecha_creacion")]
    [BsonDateTimeOptions(Kind = DateTimeKind. Utc)]
    public DateTime FechaCreacion { get; set; } = DateTime.UtcNow;

    [BsonElement("tags")]
    public List<string> Tags { get; set; } = new();
}
```

**Configuración de MongoDB:**

```csharp
namespace MiAplicacion.Configuration;

public class MongoDbSettings
{
    public string ConnectionString { get; set; } = string.Empty;
    public string DatabaseName { get; set; } = string.Empty;
    public string ProductosCollectionName { get; set; } = "productos";
}
```

**appsettings.json:**

```json
{
  "MongoDbSettings": {
    "ConnectionString": "mongodb://localhost:27017",
    "DatabaseName": "tienda_db",
    "ProductosCollectionName":  "productos"
  }
}
```

**Servicio de configuración MongoDB:**

```csharp
using MongoDB.Driver;
using MiAplicacion.Configuration;

namespace MiAplicacion.Services;

public class MongoDbContext
{
    private readonly IMongoDatabase _database;

    public MongoDbContext(IOptions<MongoDbSettings> settings)
    {
        var client = new MongoClient(settings.Value.ConnectionString);
        _database = client.GetDatabase(settings. Value.DatabaseName);
    }

    public IMongoCollection<T> GetCollection<T>(string collectionName)
    {
        return _database.GetCollection<T>(collectionName);
    }
}
```

**Registro en Program.cs:**

```csharp
using MiAplicacion.Configuration;
using MiAplicacion.Services;

var builder = WebApplication.CreateBuilder(args);

// Configurar MongoDB
builder.Services.Configure<MongoDbSettings>(
    builder.Configuration.GetSection("MongoDbSettings")
);

builder.Services.AddSingleton<MongoDbContext>();

var app = builder.Build();
app.Run();
```

#### CRUD con API Tipada

**Repositorio MongoDB:**

```csharp
using MongoDB.Driver;
using MiAplicacion.Models;
using MiAplicacion.Configuration;

namespace MiAplicacion. Repositories;

public class ProductoMongoRepository
{
    private readonly IMongoCollection<Producto> _productos;

    public ProductoMongoRepository(
        MongoDbContext context,
        IOptions<MongoDbSettings> settings)
    {
        _productos = context.GetCollection<Producto>(
            settings.Value.ProductosCollectionName
        );
    }

    // 1. CREATE (INSERT)
    public async Task<Producto> AddAsync(Producto producto)
    {
        await _productos.InsertOneAsync(producto);
        return producto;
    }

    // Insertar múltiples documentos
    public async Task AddManyAsync(List<Producto> productos)
    {
        await _productos.InsertManyAsync(productos);
    }

    // 2. READ (FIND ALL)
    public async Task<List<Producto>> GetAllAsync()
    {
        return await _productos
            .Find(_ => true)
            .SortBy(p => p. Nombre)
            .ToListAsync();
    }

    // 3. READ (FIND BY ID)
    public async Task<Producto?> GetByIdAsync(string id)
    {
        return await _productos
            .Find(p => p.Id == id)
            .FirstOrDefaultAsync();
    }

    // 4. UPDATE (REPLACE)
    public async Task<bool> UpdateAsync(string id, Producto producto)
    {
        var result = await _productos
            .ReplaceOneAsync(p => p.Id == id, producto);
        
        return result.ModifiedCount > 0;
    }

    // UPDATE parcial (solo campos específicos)
    public async Task<bool> UpdatePrecioAsync(string id, decimal nuevoPrecio)
    {
        var update = Builders<Producto>.Update
            .Set(p => p.Precio, nuevoPrecio);

        var result = await _productos
            .UpdateOneAsync(p => p.Id == id, update);

        return result.ModifiedCount > 0;
    }

    // 5. DELETE
    public async Task<bool> DeleteAsync(string id)
    {
        var result = await _productos
            .DeleteOneAsync(p => p.Id == id);

        return result.DeletedCount > 0;
    }

    // Eliminar múltiples documentos
    public async Task<long> DeleteByCategoriaAsync(string categoria)
    {
        var result = await _productos
            .DeleteManyAsync(p => p. Categoria == categoria);

        return result. DeletedCount;
    }

    // CONSULTAS PERSONALIZADAS

    // Buscar por categoría
    public async Task<List<Producto>> GetByCategoriaAsync(string categoria)
    {
        return await _productos
            .Find(p => p.Categoria == categoria)
            .ToListAsync();
    }

    // Buscar productos caros
    public async Task<List<Producto>> GetProductosCarosAsync(decimal precioMinimo)
    {
        var filter = Builders<Producto>.Filter
            . Gt(p => p.Precio, precioMinimo);

        return await _productos
            .Find(filter)
            .SortByDescending(p => p. Precio)
            .ToListAsync();
    }

    // Búsqueda de texto
    public async Task<List<Producto>> BuscarPorNombreAsync(string texto)
    {
        var filter = Builders<Producto>. Filter
            .Regex(p => p.Nombre, new MongoDB.Bson.BsonRegularExpression(texto, "i"));

        return await _productos
            .Find(filter)
            .ToListAsync();
    }

    // Consulta con múltiples filtros
    public async Task<List<Producto>> BusquedaAvanzadaAsync(
        string?  categoria = null,
        decimal? precioMinimo = null,
        decimal? precioMaximo = null)
    {
        var filterBuilder = Builders<Producto>.Filter;
        var filters = new List<FilterDefinition<Producto>>();

        if (!string.IsNullOrEmpty(categoria))
        {
            filters.Add(filterBuilder.Eq(p => p.Categoria, categoria));
        }

        if (precioMinimo.HasValue)
        {
            filters. Add(filterBuilder. Gte(p => p.Precio, precioMinimo.Value));
        }

        if (precioMaximo.HasValue)
        {
            filters.Add(filterBuilder.Lte(p => p.Precio, precioMaximo.Value));
        }

        var filter = filters.Count > 0
            ? filterBuilder.And(filters)
            : filterBuilder.Empty;

        return await _productos
            .Find(filter)
            .ToListAsync();
    }

    // Paginación
    public async Task<List<Producto>> GetProductosPaginadosAsync(
        int pageNumber,
        int pageSize)
    {
        return await _productos
            .Find(_ => true)
            .Skip((pageNumber - 1) * pageSize)
            .Limit(pageSize)
            .ToListAsync();
    }

    // Agregaciones
    public async Task<List<ResumenCategoria>> GetResumenPorCategoriaAsync()
    {
        var pipeline = _productos. Aggregate()
            .Group(p => p.Categoria, g => new ResumenCategoria
            {
                Categoria = g.Key,
                Cantidad = g.Count(),
                PrecioPromedio = g.Average(p => p. Precio),
                PrecioTotal = g.Sum(p => p.Precio)
            })
            .SortByDescending(r => r.PrecioTotal);

        return await pipeline.ToListAsync();
    }

    // Contar documentos
    public async Task<long> CountAsync()
    {
        return await _productos.CountDocumentsAsync(_ => true);
    }

    public async Task<long> CountByCategoriaAsync(string categoria)
    {
        return await _productos
            .CountDocumentsAsync(p => p.Categoria == categoria);
    }

    // Verificar existencia
    public async Task<bool> ExistsAsync(string id)
    {
        var count = await _productos
            .CountDocumentsAsync(p => p.Id == id);
        
        return count > 0;
    }
}

public record ResumenCategoria
{
    public string Categoria { get; init; } = string.Empty;
    public int Cantidad { get; init; }
    public decimal PrecioPromedio { get; init; }
    public decimal PrecioTotal { get; init; }
}
```

**Uso del repositorio:**

```csharp
public class ProductoMongoService
{
    private readonly ProductoMongoRepository _repository;

    public ProductoMongoService(ProductoMongoRepository repository)
    {
        _repository = repository;
    }

    public async Task EjemplosDeUso()
    {
        // CREATE
        var nuevoProducto = new Producto
        {
            Nombre = "Teclado Mecánico",
            Precio = 89.99m,
            Categoria = "Electrónica",
            Tags = new List<string> { "gaming", "rgb", "mecánico" }
        };

        await _repository.AddAsync(nuevoProducto);
        Console.WriteLine($"Producto creado con ID: {nuevoProducto.Id}");

        // READ ALL
        var todos = await _repository.GetAllAsync();
        Console.WriteLine($"Total de productos: {todos.Count}");

        // READ BY ID
        var producto = await _repository. GetByIdAsync(nuevoProducto.Id! );
        if (producto != null)
        {
            Console.WriteLine($"Encontrado: {producto.Nombre}");
        }

        // UPDATE
        if (nuevoProducto.Id != null)
        {
            nuevoProducto.Precio = 79.99m;
            await _repository.UpdateAsync(nuevoProducto.Id, nuevoProducto);
        }

        // UPDATE parcial
        if (nuevoProducto.Id != null)
        {
            await _repository.UpdatePrecioAsync(nuevoProducto.Id, 75.00m);
        }

        // DELETE
        if (nuevoProducto.Id != null)
        {
            await _repository.DeleteAsync(nuevoProducto.Id);
        }

        // CONSULTAS PERSONALIZADAS
        var electronicos = await _repository.GetByCategoriaAsync("Electrónica");
        var caros = await _repository.GetProductosCarosAsync(100m);
        var busqueda = await _repository.BuscarPorNombreAsync("teclado");

        // AGREGACIONES
        var resumen = await _repository.GetResumenPorCategoriaAsync();
        foreach (var categoria in resumen)
        {
            Console.WriteLine(
                $"{categoria.Categoria}: {categoria. Cantidad} productos, " +
                $"Promedio: {categoria.PrecioPromedio:C}"
            );
        }
    }
}
```

---

### 9. Concurrencia y programación asíncrona

La **programación asíncrona** es fundamental en .NET para construir aplicaciones responsivas y escalables. A diferencia de Java que usa `CompletableFuture`, .NET tiene un modelo asíncrono nativo basado en `async`/`await` y la clase `Task`, que es mucho más natural y legible.

#### 9.1. Introducción a `Task` y `async`/`await`

Un **`Task`** representa una operación asíncrona que puede o no devolver un valor. Es el equivalente a `CompletableFuture` de Java, pero con un soporte mucho más profundo en el lenguaje.

- **`Task`**:  Representa una operación asíncrona que no devuelve valor (equivalente a `void`).
- **`Task<T>`**: Representa una operación asíncrona que devuelve un valor de tipo `T`.
- **`async`**: Palabra clave que marca un método como asíncrono. 
- **`await`**: Palabra clave que suspende la ejecución del método hasta que la tarea se complete, liberando el hilo para otras operaciones.

**Diferencia clave con hilos tradicionales:**

Un **hilo (Thread)** es una unidad de ejecución de bajo nivel que consume recursos del sistema. Una **`Task`** es una abstracción de alto nivel que representa trabajo que puede ejecutarse de forma asíncrona, pero no necesariamente en un hilo separado.  Las operaciones de I/O asíncronas (red, disco) pueden completarse sin bloquear ningún hilo.

**Ejemplo básico de async/await:**

```csharp
public class EjemploAsync
{
    // Método asíncrono que simula una operación larga
    public static async Task<string> ObtenerDatosAsync()
    {
        Console.WriteLine($"Iniciando operación en hilo:  {Thread.CurrentThread.ManagedThreadId}");
        
        // Simula una operación de I/O (red, disco, etc.)
        await Task.Delay(2000);
        
        Console.WriteLine($"Operación completada en hilo: {Thread.CurrentThread.ManagedThreadId}");
        return "Datos obtenidos de la API";
    }

    public static async Task Main()
    {
        Console.WriteLine("Inicio del programa principal");
        
        // Inicia la tarea asíncrona
        Task<string> tarea = ObtenerDatosAsync();
        
        // El hilo principal no se bloquea y puede hacer otras cosas
        Console.WriteLine("El hilo principal continúa trabajando.. .");
        
        // Espera el resultado cuando lo necesitas
        string resultado = await tarea;
        Console.WriteLine($"Resultado: {resultado}");
        
        Console.WriteLine("Fin del programa");
    }
}
```

**Beneficios de async/await:**

1. **Código más legible**: Se parece a código síncrono, sin callbacks complejos.
2. **Excepciones naturales**: Las excepciones se propagan de forma natural con try-catch.
3. **Liberación de hilos**: No bloquea hilos mientras espera operaciones de I/O.
4. **Composición**: Fácil de encadenar múltiples operaciones asíncronas.

**Ejemplo con operaciones de I/O reales:**

```csharp
public class ServicioWeb
{
    private readonly HttpClient _httpClient;

    public ServicioWeb(HttpClient httpClient)
    {
        _httpClient = httpClient;
    }

    // Llamada HTTP asíncrona
    public async Task<string> ObtenerProductoAsync(int id)
    {
        try
        {
            string url = $"https://api.example.com/productos/{id}";
            
            // Esta operación no bloquea ningún hilo
            var response = await _httpClient.GetAsync(url);
            response.EnsureSuccessStatusCode();
            
            // Leer el contenido de forma asíncrona
            return await response.Content.ReadAsStringAsync();
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"Error en la petición: {ex.Message}");
            throw;
        }
    }

    // Operación de base de datos asíncrona
    public async Task<List<Producto>> ObtenerProductosAsync()
    {
        await using var context = new ApplicationDbContext();
        
        // EF Core ejecuta la consulta de forma asíncrona
        return await context.Productos
            .Where(p => p.Precio > 100)
            .ToListAsync();
    }

    // Escribir en archivo de forma asíncrona
    public async Task GuardarLogAsync(string mensaje)
    {
        string ruta = "log.txt";
        
        // Operación de I/O asíncrona
        await using var writer = new StreamWriter(ruta, append: true);
        await writer. WriteLineAsync($"{DateTime.Now}: {mensaje}");
    }
}
```

**Antipatrón:  Bloquear con `.Result` o `.Wait()`**

```csharp
// ❌ MAL - Bloquea el hilo y puede causar deadlocks
public string ObtenerDatosMal()
{
    Task<string> tarea = ObtenerDatosAsync();
    return tarea.Result; // NUNCA HAGAS ESTO
}

// ✅ BIEN - Usa async/await
public async Task<string> ObtenerDatosBien()
{
    return await ObtenerDatosAsync();
}
```

**Ejecutar múltiples tareas en paralelo:**

Una de las características más potentes de `Task` es la capacidad de ejecutar múltiples operaciones en paralelo y controlar cómo esperar sus resultados.

##### **`Task.WhenAll` - Esperar a que TODAS las tareas se completen**

`Task.WhenAll` crea una tarea que se completa cuando todas las tareas proporcionadas se han completado.  Es ideal cuando necesitas ejecutar varias operaciones independientes en paralelo y necesitas todos los resultados.

```csharp
public class EjemploParalelismo
{
    private readonly HttpClient _httpClient = new();

    // Simula llamadas a diferentes APIs
    public async Task<string> ObtenerUsuarioAsync(int id)
    {
        await Task.Delay(1000); // Simula latencia de red
        return $"Usuario {id}";
    }

    public async Task<string> ObtenerPedidosAsync(int usuarioId)
    {
        await Task.Delay(1500);
        return $"Pedidos del usuario {usuarioId}";
    }

    public async Task<string> ObtenerDireccionAsync(int usuarioId)
    {
        await Task.Delay(800);
        return $"Dirección del usuario {usuarioId}";
    }

    // Ejecutar todas las llamadas en paralelo con WhenAll
    public async Task<PerfilCompleto> ObtenerPerfilCompletoAsync(int usuarioId)
    {
        Console.WriteLine("Iniciando llamadas en paralelo...");
        var inicio = DateTime.Now;

        // Iniciar todas las tareas en paralelo
        Task<string> tareaUsuario = ObtenerUsuarioAsync(usuarioId);
        Task<string> tareaPedidos = ObtenerPedidosAsync(usuarioId);
        Task<string> tareaDireccion = ObtenerDireccionAsync(usuarioId);

        // Esperar a que TODAS se completen
        await Task.WhenAll(tareaUsuario, tareaPedidos, tareaDireccion);

        var duracion = DateTime.Now - inicio;
        Console.WriteLine($"Todas las operaciones completadas en {duracion. TotalSeconds:F2}s");

        // Obtener los resultados
        return new PerfilCompleto
        {
            Usuario = await tareaUsuario,      // Ya está completada, no espera
            Pedidos = await tareaPedidos,      // Ya está completada
            Direccion = await tareaDireccion   // Ya está completada
        };
    }

    // WhenAll con array de tareas del mismo tipo
    public async Task<List<string>> ObtenerMultiplesUsuariosAsync(int[] ids)
    {
        // Crear un array de tareas
        Task<string>[] tareas = ids
            .Select(id => ObtenerUsuarioAsync(id))
            .ToArray();

        // Esperar a todas y obtener el array de resultados
        string[] resultados = await Task.WhenAll(tareas);

        return resultados.ToList();
    }

    // WhenAll con procesamiento de resultados
    public async Task<decimal> CalcularTotalVentasAsync(int[] tiendaIds)
    {
        var tareas = tiendaIds. Select(async id =>
        {
            await Task.Delay(500); // Simula llamada a API
            return Random.Shared.Next(1000, 5000); // Simula ventas
        });

        // Esperar todas las tareas y obtener resultados
        int[] ventas = await Task.WhenAll(tareas);

        // Procesar resultados
        return ventas.Sum();
    }
}

public record PerfilCompleto
{
    public string Usuario { get; init; } = string.Empty;
    public string Pedidos { get; init; } = string.Empty;
    public string Direccion { get; init; } = string.Empty;
}
```

##### **`Task.WhenAny` - Esperar a que AL MENOS UNA tarea se complete**

`Task.WhenAny` crea una tarea que se completa cuando cualquiera de las tareas proporcionadas se completa. Es útil para escenarios de timeout, redundancia o cuando solo necesitas el resultado más rápido.

```csharp
public class EjemploWhenAny
{
    // Escenario 1: Timeout - cancelar si tarda demasiado
    public async Task<string? > ObtenerDatosConTimeoutAsync()
    {
        var tareaDescarga = DescargarDatosAsync();
        var tareaTimeout = Task.Delay(TimeSpan.FromSeconds(5));

        // Esperar a que se complete la primera tarea
        Task tareaCompletada = await Task.WhenAny(tareaDescarga, tareaTimeout);

        if (tareaCompletada == tareaDescarga)
        {
            Console.WriteLine("Descarga completada a tiempo");
            return await tareaDescarga;
        }
        else
        {
            Console.WriteLine("Timeout: La operación tardó demasiado");
            return null;
        }
    }

    private async Task<string> DescargarDatosAsync()
    {
        await Task.Delay(3000); // Simula descarga
        return "Datos descargados";
    }

    // Escenario 2: Llamadas redundantes - usar el servidor más rápido
    public async Task<string> ObtenerDatosDeMejorServidorAsync()
    {
        Console.WriteLine("Consultando múltiples servidores en paralelo...");

        // Llamar a tres servidores diferentes simultáneamente
        Task<string> servidor1 = ConsultarServidorAsync("Servidor 1", 2000);
        Task<string> servidor2 = ConsultarServidorAsync("Servidor 2", 1500);
        Task<string> servidor3 = ConsultarServidorAsync("Servidor 3", 2500);

        // Esperar a que el primero responda
        Task<string> tareaGanadora = await Task.WhenAny(servidor1, servidor2, servidor3);

        string resultado = await tareaGanadora;
        Console.WriteLine($"Respuesta más rápida de:  {resultado}");

        return resultado;
    }

    private async Task<string> ConsultarServidorAsync(string nombre, int latencia)
    {
        await Task.Delay(latencia);
        return nombre;
    }

    // Escenario 3: Procesar tareas a medida que se completan
    public async Task ProcesarTareasSegunCompletanAsync()
    {
        var tareas = new List<Task<string>>
        {
            TareaDemoradaAsync("Tarea A", 3000),
            TareaDemoradaAsync("Tarea B", 1000),
            TareaDemoradaAsync("Tarea C", 2000),
            TareaDemoradaAsync("Tarea D", 1500)
        };

        Console.WriteLine("Procesando tareas a medida que se completan...\n");

        while (tareas.Count > 0)
        {
            // Esperar a la siguiente tarea que se complete
            Task<string> tareaCompletada = await Task.WhenAny(tareas);

            // Procesar el resultado inmediatamente
            string resultado = await tareaCompletada;
            Console.WriteLine($"✓ Completada: {resultado}");

            // Remover de la lista de pendientes
            tareas.Remove(tareaCompletada);
        }

        Console.WriteLine("\nTodas las tareas han sido procesadas");
    }

    private async Task<string> TareaDemoradaAsync(string nombre, int milisegundos)
    {
        await Task.Delay(milisegundos);
        return $"{nombre} (tardó {milisegundos}ms)";
    }

    // Escenario 4: Retry con múltiples estrategias
    public async Task<string? > IntentarConMultiplesEstrategiasAsync()
    {
        // Intentar diferentes estrategias en paralelo
        Task<string> estrategia1 = IntentarConReintentos(3);
        Task<string> estrategia2 = IntentarConBackoff();
        Task<string> estrategia3 = IntentarConServidorAlternativo();

        while (true)
        {
            // Esperar a que cualquier estrategia tenga éxito
            var tareaCompletada = await Task.WhenAny(estrategia1, estrategia2, estrategia3);

            try
            {
                // Intentar obtener el resultado
                return await tareaCompletada;
            }
            catch
            {
                // Si falló, esperar a las otras
                if (tareaCompletada == estrategia1)
                {
                    if (estrategia2.IsCompleted && estrategia3.IsCompleted)
                        return null; // Todas fallaron
                }
            }
        }
    }

    private async Task<string> IntentarConReintentos(int intentos)
    {
        await Task.Delay(1000);
        throw new Exception("Estrategia 1 falló");
    }

    private async Task<string> IntentarConBackoff()
    {
        await Task.Delay(1500);
        throw new Exception("Estrategia 2 falló");
    }

    private async Task<string> IntentarConServidorAlternativo()
    {
        await Task. Delay(800);
        return "Éxito con servidor alternativo";
    }
}
```

##### **Combinando `WhenAll` y `WhenAny` - Patrones avanzados**

```csharp
public class PatronesAvanzados
{
    // Patrón:  Ejecutar en lotes con límite de concurrencia
    public async Task<List<string>> ProcesarEnLotesAsync(List<int> ids, int concurrenciaMaxima)
    {
        var resultados = new List<string>();
        var lotes = ids. Chunk(concurrenciaMaxima);

        foreach (var lote in lotes)
        {
            Console.WriteLine($"Procesando lote de {lote.Length} elementos...");

            // Procesar cada lote en paralelo
            var tareasLote = lote.Select(id => ProcesarElementoAsync(id));
            var resultadosLote = await Task. WhenAll(tareasLote);

            resultados.AddRange(resultadosLote);
        }

        return resultados;
    }

    private async Task<string> ProcesarElementoAsync(int id)
    {
        await Task.Delay(500);
        return $"Procesado:  {id}";
    }

    // Patrón:  Timeout por tarea individual en un lote
    public async Task<List<string? >> ProcesarConTimeoutIndividualAsync(int[] ids)
    {
        var tareas = ids.Select(async id =>
        {
            var tareaReal = ProcesarElementoAsync(id);
            var tareaTimeout = Task.Delay(TimeSpan.FromSeconds(2));

            var completada = await Task.WhenAny(tareaReal, tareaTimeout);

            if (completada == tareaReal)
            {
                return await tareaReal;
            }
            else
            {
                Console.WriteLine($"Timeout en elemento {id}");
                return null;
            }
        });

        return (await Task.WhenAll(tareas)).ToList();
    }

    // Patrón:  Cancelación de todas las tareas restantes cuando una tiene éxito
    public async Task<string?> PrimeraEnTenerExitoAsync()
    {
        using var cts = new CancellationTokenSource();

        var tareas = new[]
        {
            IntentarOperacionAsync("Intento 1", 3000, cts.Token),
            IntentarOperacionAsync("Intento 2", 1500, cts.Token),
            IntentarOperacionAsync("Intento 3", 2000, cts.Token)
        };

        while (tareas.Any(t => ! t.IsCompleted))
        {
            var completada = await Task.WhenAny(tareas);

            try
            {
                var resultado = await completada;
                
                // Cancelar las demás tareas
                cts.Cancel();
                
                Console.WriteLine($"Éxito: {resultado}");
                return resultado;
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Una tarea falló: {ex. Message}");
            }
        }

        return null; // Todas fallaron
    }

    private async Task<string> IntentarOperacionAsync(string nombre, int delay, CancellationToken ct)
    {
        await Task.Delay(delay, ct);
        
        if (Random.Shared.Next(0, 2) == 0)
            throw new Exception($"{nombre} falló");
            
        return nombre;
    }

    // Patrón:  Progreso de tareas paralelas
    public async Task DescargarArchivosConProgresoAsync(string[] urls)
    {
        var progreso = 0;
        var total = urls.Length;
        var lockObj = new object();

        var tareas = urls.Select(async url =>
        {
            var resultado = await DescargarArchivoAsync(url);

            lock (lockObj)
            {
                progreso++;
                Console.WriteLine($"Progreso: {progreso}/{total} ({progreso * 100.0 / total:F1}%)");
            }

            return resultado;
        });

        await Task.WhenAll(tareas);
        Console.WriteLine("Todas las descargas completadas");
    }

    private async Task<string> DescargarArchivoAsync(string url)
    {
        await Task.Delay(Random.Shared.Next(1000, 3000));
        return $"Descargado: {url}";
    }
}
```

##### **Manejo de excepciones con múltiples tareas**

```csharp
public class ManejoExcepcionesParalelo
{
    // Manejo de excepciones con WhenAll
    public async Task EjemploExcepcionesWhenAllAsync()
    {
        var tareas = new[]
        {
            TareaQueFallaAsync("Tarea 1", 1000, false),
            TareaQueFallaAsync("Tarea 2", 500, true),   // Esta falla
            TareaQueFallaAsync("Tarea 3", 1500, true),  // Esta también falla
            TareaQueFallaAsync("Tarea 4", 800, false)
        };

        try
        {
            await Task.WhenAll(tareas);
        }
        catch (Exception ex)
        {
            // Solo captura la primera excepción
            Console.WriteLine($"Excepción capturada: {ex.Message}");

            // Para obtener TODAS las excepciones, usa la propiedad Exception de la tarea
            foreach (var tarea in tareas. Where(t => t.IsFaulted))
            {
                Console.WriteLine($"  - {tarea.Exception?. InnerException?.Message}");
            }
        }
    }

    // Manejo de excepciones con WhenAny
    public async Task EjemploExcepcionesWhenAnyAsync()
    {
        var tareas = new[]
        {
            TareaQueFallaAsync("Tarea 1", 1000, true),
            TareaQueFallaAsync("Tarea 2", 500, false),
            TareaQueFallaAsync("Tarea 3", 1500, true)
        };

        var tareaCompletada = await Task.WhenAny(tareas);

        try
        {
            string resultado = await tareaCompletada;
            Console.WriteLine($"Éxito: {resultado}");
        }
        catch (Exception ex)
        {
            Console. WriteLine($"La primera tarea en completar falló: {ex.Message}");
        }
    }

    private async Task<string> TareaQueFallaAsync(string nombre, int delay, bool falla)
    {
        await Task.Delay(delay);

        if (falla)
            throw new InvalidOperationException($"{nombre} ha fallado");

        return nombre;
    }

    // Patrón: Continuar a pesar de fallos individuales
    public async Task<List<string>> ProcesarTodosSinDetenerseAsync(int[] ids)
    {
        var resultados = new List<string>();

        var tareas = ids.Select(async id =>
        {
            try
            {
                return await ProcesarConPosibleFalloAsync(id);
            }
            catch (Exception ex)
            {
                Console.WriteLine($"Error en {id}: {ex.Message}");
                return $"Error: {id}";
            }
        });

        var todosLosResultados = await Task.WhenAll(tareas);
        return todosLosResultados.ToList();
    }

    private async Task<string> ProcesarConPosibleFalloAsync(int id)
    {
        await Task.Delay(500);
        
        if (id % 3 == 0)
            throw new Exception($"Error procesando {id}");
            
        return $"Éxito: {id}";
    }
}
```

#### 9. 2. Configuración de contexto con `ConfigureAwait`

Por defecto, cuando usas `await`, el código después del `await` se ejecuta en el mismo contexto de sincronización (SynchronizationContext) que el código antes del `await`. En aplicaciones ASP.NET Core, esto no es necesario y puede causar problemas de rendimiento.

```csharp
public class ConfiguracionContexto
{
    // ❌ Captura el contexto (comportamiento por defecto)
    public async Task<string> ConContextoAsync()
    {
        string resultado = await ObtenerDatosAsync();
        // Continúa en el contexto original
        return resultado. ToUpper();
    }

    // ✅ No captura el contexto (mejor rendimiento en bibliotecas)
    public async Task<string> SinContextoAsync()
    {
        string resultado = await ObtenerDatosAsync().ConfigureAwait(false);
        // Puede continuar en cualquier hilo
        return resultado.ToUpper();
    }

    private async Task<string> ObtenerDatosAsync()
    {
        await Task.Delay(1000);
        return "datos";
    }
}
```

**Regla general:**
- En **bibliotecas**:  Usa `.ConfigureAwait(false)` para mejor rendimiento. 
- En **aplicaciones (UI, ASP.NET)**: No uses `ConfigureAwait` (el comportamiento por defecto es correcto).
- En **ASP.NET Core**: `.ConfigureAwait(false)` es opcional ya que no hay SynchronizationContext.

#### 9.3. Operaciones paralelas con `Task` y `Parallel`

Para operaciones CPU-intensivas (no I/O), puedes usar `Parallel.ForEach` o `Parallel.For`:

```csharp
public class EjemploParallel
{
    // Parallel.ForEach para operaciones CPU-intensivas
    public void ProcesarEnParaleloConParallelForEach(List<int> numeros)
    {
        var resultados = new ConcurrentBag<int>();

        Parallel.ForEach(numeros, numero =>
        {
            // Operación CPU-intensiva
            int resultado = CalculoComplejo(numero);
            resultados.Add(resultado);
        });

        Console.WriteLine($"Procesados {resultados.Count} elementos");
    }

    // PLINQ para operaciones con colecciones
    public List<int> ProcesarConPLINQ(List<int> numeros)
    {
        return numeros
            .AsParallel()
            .WithDegreeOfParallelism(Environment.ProcessorCount)
            .Select(n => CalculoComplejo(n))
            .ToList();
    }

    // Task.Run para operaciones CPU-intensivas
    public async Task<List<int>> ProcesarConTaskRunAsync(List<int> numeros)
    {
        var tareas = numeros.Select(n => Task.Run(() => CalculoComplejo(n)));
        var resultados = await Task.WhenAll(tareas);
        return resultados.ToList();
    }

    private int CalculoComplejo(int numero)
    {
        // Simula cálculo CPU-intensivo
        Thread.SpinWait(10000000);
        return numero * numero;
    }
}
```

**Diferencia clave:**
- **`async`/`await` con I/O**: No consume hilos mientras espera (ideal para operaciones de red, disco, BD).
- **`Task.Run` / `Parallel`**: Usa hilos del thread pool para operaciones CPU-intensivas. 

---
### 10. Programación Reactiva:   Observables vs Colecciones Asíncronas

La **programación reactiva** en .  NET ha evolucionado significativamente.  Mientras que en Java predomina **RxJava**, en .  NET tenemos dos aproximaciones fundamentales:  **`IAsyncEnumerable<T>`** (colecciones asíncronas nativas desde C# 8) y **`IObservable<T>`** con **System.Reactive (Rx. NET)**.

#### 10.1. Introducción a la programación reactiva en .  NET

La programación reactiva se basa en **flujos de datos** que emiten valores a lo largo del tiempo. La diferencia fundamental entre las dos aproximaciones radica en su modelo de obtención de datos:

- **`IAsyncEnumerable<T>` (Pull-based)**: El **consumidor** solicita datos cuando está listo para procesarlos.  Es un modelo de **"tirar"** (pull). El consumidor controla el ritmo. 
  
- **`IObservable<T>` (Push-based)**: El **productor** emite datos cuando están disponibles, sin esperar a que el consumidor los solicite. Es un modelo de **"empujar"** (push). El productor controla el ritmo.

**Instalación de Rx.NET:**

```bash
dotnet add package System.Reactive
```

#### 10.2. `IAsyncEnumerable<T>`: Colecciones asíncronas nativas

`IAsyncEnumerable<T>` es una característica nativa de C# 8+ que permite iterar sobre una secuencia de valores que se producen de forma asíncrona.  Es el equivalente asíncrono de `IEnumerable<T>`.

**Características clave:**
- ✅ **Pull-based**: El consumidor controla cuándo quiere el siguiente elemento. 
- ✅ **Flujo frío**: La producción de datos comienza cuando alguien empieza a iterar.
- ✅ **Un consumidor a la vez**:  Cada iterador tiene su propia secuencia independiente.
- ✅ **Backpressure natural**: El productor solo genera el siguiente elemento cuando se le solicita. 
- ✅ **Ideal para**:  Streaming de datos de BD, lectura de archivos grandes, paginación de APIs.

##### Ejemplo:   Lectura asíncrona de fichero con `IAsyncEnumerable`

```csharp
namespace MiAplicacion. AsyncStreams;

public class LectorFicheroAsync
{
    // Productor:   Lee el fichero línea por línea de forma asíncrona
    public static async IAsyncEnumerable<string> LeerLineasAsync(string rutaArchivo)
    {
        Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] Iniciando lectura del archivo.. .");

        // Comprobar si el archivo existe
        if (!File.Exists(rutaArchivo))
        {
            throw new FileNotFoundException($"El archivo {rutaArchivo} no existe");
        }

        // Usar StreamReader asíncrono
        using var reader = new StreamReader(rutaArchivo);
        
        string?  linea;
        int numeroLinea = 0;

        while ((linea = await reader.ReadLineAsync()) != null)
        {
            numeroLinea++;
            Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] Línea {numeroLinea} leída del archivo");
            
            // Simular procesamiento de la línea
            await Task.Delay(100);
            
            // yield return permite devolver cada elemento de forma "perezosa"
            yield return linea;
        }

        Console.WriteLine($"[{DateTime. Now:HH:mm:ss}] Lectura completada.  Total de líneas:  {numeroLinea}");
    }

    // Productor con filtrado integrado
    public static async IAsyncEnumerable<string> LeerLineasFiltradas(
        string rutaArchivo, 
        string filtro)
    {
        await foreach (var linea in LeerLineasAsync(rutaArchivo))
        {
            if (linea.Contains(filtro, StringComparison.OrdinalIgnoreCase))
            {
                yield return linea;
            }
        }
    }

    // Ejemplo:   Consumidor con await foreach
    public static async Task EjemploConsumoBasico()
    {
        Console.WriteLine("\n=== EJEMPLO 1: Consumo básico (Pull-based) ===\n");

        string rutaArchivo = "datos.txt";

        // Crear archivo de prueba
        await File.WriteAllLinesAsync(rutaArchivo, new[]
        {
            "Línea 1: Hola mundo",
            "Línea 2: Programación reactiva",
            "Línea 3: async/await",
            "Línea 4: IAsyncEnumerable",
            "Línea 5: Flujos fríos"
        });

        // CONSUMIDOR - Pull-based
        await foreach (var linea in LeerLineasAsync(rutaArchivo))
        {
            Console.WriteLine($"[{DateTime.Now:HH: mm:ss}] Consumidor procesando: {linea}");
            
            // El consumidor controla el ritmo
            await Task.Delay(200); // Procesar más lento que el productor
        }

        Console.WriteLine("\n[Fin del consumo]");
    }

    // Ejemplo:  Múltiples consumidores independientes (flujo frío)
    public static async Task EjemploFlujosIndependientes()
    {
        Console.WriteLine("\n=== EJEMPLO 2: Flujos fríos - Cada consumidor tiene su propia secuencia ===\n");

        string rutaArchivo = "datos.txt";

        // Consumidor 1: Procesa todas las líneas
        Console.WriteLine("--- CONSUMIDOR 1 ---");
        await foreach (var linea in LeerLineasAsync(rutaArchivo))
        {
            Console.WriteLine($"[C1] {linea}");
        }

        Console.WriteLine("\n--- CONSUMIDOR 2 (inicia después) ---");
        
        // Consumidor 2: Procesa solo líneas con "async"
        await foreach (var linea in LeerLineasFiltradas(rutaArchivo, "async"))
        {
            Console.WriteLine($"[C2] {linea}");
        }

        // Ambos consumidores leen el archivo INDEPENDIENTEMENTE
    }
}
```

**Operaciones LINQ asíncronas con `IAsyncEnumerable`:**

```csharp
using System.Linq; // Requiere System.Linq. Async NuGet package

public class OperacionesAsyncEnumerable
{
    // Simular consulta a base de datos con paginación
    public static async IAsyncEnumerable<Producto> ObtenerProductosPaginadosAsync(
        int pageSize = 10)
    {
        int pagina = 0;
        bool hayMas = true;

        while (hayMas)
        {
            Console.WriteLine($"Obteniendo página {pagina + 1}...");
            
            // Simular llamada a BD
            await Task.Delay(500);

            var productos = await SimularConsultaBDAsync(pagina, pageSize);

            foreach (var producto in productos)
            {
                yield return producto;
            }

            hayMas = productos.Count == pageSize;
            pagina++;
        }
    }

    private static async Task<List<Producto>> SimularConsultaBDAsync(int pagina, int pageSize)
    {
        await Task.Delay(100);
        
        // Simular que solo hay 25 productos en total
        int inicio = pagina * pageSize;
        int fin = Math.Min(inicio + pageSize, 25);

        if (inicio >= 25) return new List<Producto>();

        return Enumerable.Range(inicio, fin - inicio)
            .Select(i => new Producto($"Producto {i}", i * 10m, "Categoría"))
            .ToList();
    }

    // Uso con operadores LINQ asíncronos
    public static async Task EjemploLinqAsíncrono()
    {
        Console.WriteLine("\n=== EJEMPLO:  LINQ sobre IAsyncEnumerable ===\n");

        // Nota:  Necesitas el paquete System.Linq.Async
        var productosCaros = ObtenerProductosPaginadosAsync()
            .Where(p => p.Precio > 100)
            .Select(p => new { p.Nombre, PrecioConIVA = p.Precio * 1.21m })
            .Take(5);

        await foreach (var producto in productosCaros)
        {
            Console.WriteLine($"{producto.Nombre}: {producto.PrecioConIVA:C}");
        }
    }

    // Conversión a lista (materialización)
    public static async Task<List<Producto>> ConvertirAListaAsync()
    {
        var productos = new List<Producto>();

        await foreach (var producto in ObtenerProductosPaginadosAsync(5))
        {
            productos.Add(producto);
        }

        return productos;
    }

    // Patrón productor-consumidor con Channel
    public static async Task EjemploConChannels()
    {
        var channel = Channel.CreateUnbounded<Producto>();

        // Productor
        var productorTask = Task.Run(async () =>
        {
            await foreach (var producto in ObtenerProductosPaginadosAsync(5))
            {
                await channel.Writer.WriteAsync(producto);
                Console.WriteLine($"Producido: {producto.Nombre}");
            }
            
            channel.Writer.Complete();
        });

        // Consumidor
        var consumidorTask = Task. Run(async () =>
        {
            await foreach (var producto in channel.Reader.ReadAllAsync())
            {
                Console.WriteLine($"Consumido: {producto.Nombre}");
                await Task.Delay(200); // Simular procesamiento lento
            }
        });

        await Task.WhenAll(productorTask, consumidorTask);
    }
}
```

#### 10.3. `IObservable<T>` y System.Reactive (Rx.  NET)

`IObservable<T>` es la interfaz fundamental de Rx.NET para flujos reactivos basados en **push**. El productor emite valores cuando están disponibles, y los observadores reaccionan a esos valores.

**Características clave:**
- ✅ **Push-based**: El productor controla cuándo se emiten los valores.
- ✅ **Flujos fríos y calientes**: Puedes tener ambos tipos de flujos.
- ✅ **Múltiples observadores**: Muchos observadores pueden suscribirse al mismo flujo.
- ✅ **Sin backpressure nativo**: El productor emite datos sin importar si el consumidor está listo.
- ✅ **Ideal para**: Eventos de UI, WebSockets en tiempo real, sensores, notificaciones push.

**Componentes principales:**

- **`IObservable<T>`**: El productor de valores (la fuente).
- **`IObserver<T>`**: El consumidor que reacciona a los valores. 
- **`Subject<T>`**: Actúa como productor y consumidor simultáneamente (puente).

##### Ejemplo: Flujo caliente con `Subject`

```csharp
using System.Reactive. Subjects;
using System.Reactive.Linq;

namespace MiAplicacion. Reactive;

public class SistemaNotificaciones
{
    // Subject es un flujo CALIENTE - las notificaciones se emiten aunque no haya observadores
    private readonly Subject<Notificacion> _notificaciones = new();

    // Exponer el Observable (solo lectura)
    public IObservable<Notificacion> Notificaciones => _notificaciones. AsObservable();

    // Método para emitir notificaciones
    public void EnviarNotificacion(string mensaje, TipoNotificacion tipo)
    {
        var notificacion = new Notificacion
        {
            Mensaje = mensaje,
            Tipo = tipo,
            Timestamp = DateTime.Now
        };

        Console.WriteLine($"[{notificacion.Timestamp:HH:mm:ss}] 📢 Enviando:  {mensaje}");
        
        // Push - emite la notificación a todos los observadores suscritos
        _notificaciones.OnNext(notificacion);
    }

    public void Finalizar()
    {
        _notificaciones.OnCompleted();
    }

    // Ejemplo de uso
    public static async Task EjemploFlujosCalientes()
    {
        Console.WriteLine("\n=== EJEMPLO: Flujos CALIENTES con Subject ===\n");

        var sistema = new SistemaNotificaciones();

        // Enviar notificación ANTES de que haya observadores
        sistema.EnviarNotificacion("Notificación 1 - PERDIDA", TipoNotificacion.Info);

        await Task.Delay(500);

        Console.WriteLine("\n--- Usuario A se conecta ---");
        
        // Usuario A se suscribe
        var suscripcionA = sistema.Notificaciones.Subscribe(
            notificacion => Console.WriteLine($"[Usuario A] ✉️  {notificacion.Mensaje}"),
            error => Console.WriteLine($"[Usuario A] ❌ Error: {error.Message}"),
            () => Console.WriteLine("[Usuario A] ✅ Completado")
        );

        // Enviar notificación 2
        sistema.EnviarNotificacion("Notificación 2 - Usuario A la recibe", TipoNotificacion. Info);

        await Task.Delay(1000);

        Console.WriteLine("\n--- Usuario B se conecta (tarde) ---");
        
        // Usuario B se suscribe DESPUÉS
        var suscripcionB = sistema.Notificaciones.Subscribe(
            notificacion => Console.WriteLine($"[Usuario B] ✉️  {notificacion.Mensaje}"),
            error => Console.WriteLine($"[Usuario B] ❌ Error: {error.Message}"),
            () => Console.WriteLine("[Usuario B] ✅ Completado")
        );

        // Usuario B NO recibe la Notificación 1 ni la 2 (flujo caliente)

        // Enviar notificación 3
        sistema.EnviarNotificacion("Notificación 3 - Ambos la reciben", TipoNotificacion. Advertencia);

        await Task. Delay(500);

        // Usuario A se desconecta
        Console.WriteLine("\n--- Usuario A se desconecta ---");
        suscripcionA.Dispose();

        // Enviar notificación 4
        sistema.EnviarNotificacion("Notificación 4 - Solo Usuario B", TipoNotificacion.Error);

        await Task.Delay(500);

        // Finalizar el flujo
        sistema.Finalizar();

        suscripcionB.Dispose();

        Console.WriteLine("\n[Fin del ejemplo]");
    }
}

public record Notificacion
{
    public required string Mensaje { get; init; }
    public TipoNotificacion Tipo { get; init; }
    public DateTime Timestamp { get; init; }
}

public enum TipoNotificacion
{
    Info,
    Advertencia,
    Error
}
```

**Tipos de Subject:**

```csharp
public class TiposDeSubject
{
    public static async Task EjemplosDeSubjects()
    {
        Console. WriteLine("\n=== TIPOS DE SUBJECT ===\n");

        // 1. PublishSubject - Solo emite valores DESPUÉS de la suscripción
        Console.WriteLine("--- PublishSubject ---");
        var publishSubject = new Subject<int>();
        
        publishSubject.OnNext(1); // Perdido, no hay observadores
        
        publishSubject.Subscribe(x => Console.WriteLine($"Observador 1: {x}"));
        publishSubject.OnNext(2); // Recibido
        publishSubject.OnNext(3); // Recibido
        
        publishSubject.Subscribe(x => Console.WriteLine($"Observador 2: {x}"));
        publishSubject. OnNext(4); // Ambos lo reciben

        await Task.Delay(500);

        // 2. BehaviorSubject - Emite el ÚLTIMO valor a nuevos suscriptores
        Console.WriteLine("\n--- BehaviorSubject ---");
        var behaviorSubject = new BehaviorSubject<int>(0); // Valor inicial
        
        behaviorSubject.OnNext(1);
        behaviorSubject.OnNext(2);
        
        behaviorSubject.Subscribe(x => Console.WriteLine($"Observador tardío: {x}"));
        // Recibe inmediatamente el último valor (2)
        
        behaviorSubject.OnNext(3); // Recibido

        await Task.Delay(500);

        // 3. ReplaySubject - Almacena y reproduce TODOS los valores
        Console.WriteLine("\n--- ReplaySubject ---");
        var replaySubject = new ReplaySubject<int>();
        
        replaySubject.OnNext(1);
        replaySubject.OnNext(2);
        replaySubject.OnNext(3);
        
        replaySubject.Subscribe(x => Console.WriteLine($"Observador tardío: {x}"));
        // Recibe TODOS los valores:  1, 2, 3
        
        replaySubject.OnNext(4); // Recibido

        await Task. Delay(500);

        // 4. ReplaySubject con ventana de tiempo
        Console.WriteLine("\n--- ReplaySubject (ventana de 2 segundos) ---");
        var replayTemporal = new ReplaySubject<int>(TimeSpan.FromSeconds(2));
        
        replayTemporal.OnNext(1);
        await Task.Delay(1000);
        replayTemporal. OnNext(2);
        await Task.Delay(1500); // Total 2. 5s desde el valor 1
        
        replayTemporal.Subscribe(x => Console.WriteLine($"Observador tardío: {x}"));
        // Solo recibe el valor 2 (el 1 está fuera de la ventana de 2s)

        await Task.Delay(500);

        // 5. AsyncSubject - Solo emite el ÚLTIMO valor cuando se completa
        Console.WriteLine("\n--- AsyncSubject ---");
        var asyncSubject = new AsyncSubject<int>();
        
        asyncSubject.Subscribe(x => Console.WriteLine($"Observador:  {x}"));
        
        asyncSubject.OnNext(1);
        asyncSubject. OnNext(2);
        asyncSubject.OnNext(3);
        // Aún no se emite nada
        
        asyncSubject.OnCompleted(); // Ahora emite el último valor (3)
    }
}
```

**Flujos fríos con Observable. Create:**

```csharp
public class FlujosFrios
{
    // Flujo FRÍO - Cada suscriptor obtiene su propia secuencia independiente
    public static IObservable<string> LeerArchivoObservable(string rutaArchivo)
    {
        return Observable.Create<string>(async observer =>
        {
            Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] 🔵 Nueva suscripción - Iniciando lectura");

            try
            {
                using var reader = new StreamReader(rutaArchivo);
                string?  linea;
                int numeroLinea = 0;

                while ((linea = await reader. ReadLineAsync()) != null)
                {
                    numeroLinea++;
                    Console.WriteLine($"[{DateTime. Now:HH:mm:ss}] Emitiendo línea {numeroLinea}");
                    
                    // Push - emitir el valor al observador
                    observer.OnNext(linea);
                    
                    await Task.Delay(100); // Simular procesamiento
                }

                Console.WriteLine($"[{DateTime.Now:HH: mm:ss}] ✅ Lectura completada");
                observer. OnCompleted();
            }
            catch (Exception ex)
            {
                Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] ❌ Error:  {ex.Message}");
                observer.OnError(ex);
            }

            // Retornar disposable para limpiar recursos si se cancela la suscripción
            return Disposable.Empty;
        });
    }

    public static async Task EjemploFlujosFrios()
    {
        Console.WriteLine("\n=== EJEMPLO: Flujos FRÍOS con Observable.Create ===\n");

        string rutaArchivo = "datos-observable.txt";
        await File.WriteAllLinesAsync(rutaArchivo, new[]
        {
            "Línea A1",
            "Línea A2",
            "Línea A3"
        });

        var observable = LeerArchivoObservable(rutaArchivo);

        Console.WriteLine("--- Suscriptor 1 ---");
        var suscripcion1 = observable.Subscribe(
            linea => Console.WriteLine($"[S1] 📖 {linea}"),
            ex => Console.WriteLine($"[S1] Error: {ex.Message}"),
            () => Console.WriteLine("[S1] Completado")
        );

        await Task.Delay(500);

        Console.WriteLine("\n--- Suscriptor 2 (independiente) ---");
        var suscripcion2 = observable. Subscribe(
            linea => Console.WriteLine($"[S2] 📖 {linea}"),
            ex => Console.WriteLine($"[S2] Error:  {ex.Message}"),
            () => Console.WriteLine("[S2] Completado")
        );

        // Cada suscriptor lee el archivo INDEPENDIENTEMENTE (flujo frío)

        await Task.Delay(2000);

        suscripcion1.Dispose();
        suscripcion2.Dispose();
    }
}
```

#### 10.4. Diferencias clave:   Observables vs Colecciones Asíncronas

| **Característica**              | **`IAsyncEnumerable<T>`** (Pull)                     | **`IObservable<T>`** (Push)                          |
|: --------------------------------|:-----------------------------------------------------|:-----------------------------------------------------|
| **Modelo**                      | Pull-based (el consumidor pide datos)                 | Push-based (el productor emite datos)                 |
| **Control de flujo**            | ✅ Consumidor controla el ritmo                       | ⚠️ Productor controla el ritmo                       |
| **Backpressure**                | ✅ Natural (no produce hasta que se solicita)         | ❌ Requiere estrategias adicionales (throttle, buffer)|
| **Tipo de flujo**               | ❄️ Siempre FRÍO                                      | ❄️ FRÍO o 🔥 CALIENTE (según implementación)        |
| **Múltiples consumidores**      | ⚠️ Cada uno tiene su secuencia independiente         | ✅ Múltiples observadores comparten el mismo flujo   |
| **Cancelación**                 | ✅ Con `CancellationToken`                            | ✅ Con `Dispose()` de la suscripción                 |
| **Operadores**                  | ⚠️ LINQ estándar (requiere System.Linq. Async)        | ✅ Rx.NET (cientos de operadores)                    |
| **Composición**                 | ⚠️ Limitada                                          | ✅ Muy potente (merge, zip, concat, etc.)           |
| **Manejo de tiempo**            | ❌ No tiene concepto de tiempo                       | ✅ Operadores temporales (Delay, Throttle, etc.)    |
| **Ideal para**                  | BD, archivos, APIs con paginación, streaming de datos| Eventos UI, WebSockets, sensores, notificaciones    |
| **Complejidad**                 | 🟢 Baja (nativo en C#)                               | 🟡 Media-Alta (requiere Rx.NET)                     |

**Ejemplo comparativo lado a lado:**

```csharp
public class ComparacionPullVsPush
{
    // PULL: IAsyncEnumerable - El consumidor controla el ritmo
    public static async IAsyncEnumerable<int> GeneradorPull()
    {
        for (int i = 1; i <= 5; i++)
        {
            Console.WriteLine($"[PULL] Generando valor {i} (esperando que el consumidor lo pida)");
            await Task.Delay(500);
            yield return i;
        }
    }

    // PUSH:  IObservable - El productor controla el ritmo
    public static IObservable<int> GeneradorPush()
    {
        return Observable.Create<int>(async observer =>
        {
            for (int i = 1; i <= 5; i++)
            {
                Console.WriteLine($"[PUSH] Generando valor {i} (empujando al observador)");
                observer.OnNext(i);
                await Task.Delay(500);
            }
            
            observer.OnCompleted();
            return Disposable.Empty;
        });
    }

    public static async Task Comparar()
    {
        Console. WriteLine("\n=== COMPARACIÓN:  PULL vs PUSH ===\n");

        // Escenario PULL
        Console.WriteLine("--- PULL (IAsyncEnumerable) ---");
        await foreach (var valor in GeneradorPull())
        {
            Console. WriteLine($"[Consumidor PULL] Procesando {valor}");
            await Task. Delay(1000); // El consumidor es más LENTO
            // El productor ESPERA a que el consumidor esté listo
        }

        await Task.Delay(1000);

        // Escenario PUSH
        Console.WriteLine("\n--- PUSH (IObservable) ---");
        var completado = new TaskCompletionSource<bool>();

        GeneradorPush().Subscribe(
            valor =>
            {
                Console.WriteLine($"[Observador PUSH] Procesando {valor}");
                Thread.Sleep(1000); // El observador es más LENTO
                // El productor NO espera, sigue emitiendo
            },
            () =>
            {
                Console.WriteLine("[Observador PUSH] Completado");
                completado.SetResult(true);
            }
        );

        await completado.Task;
    }
}
```

#### 10.5. Operadores de Rx.NET

Rx.NET proporciona cientos de operadores para transformar, filtrar y combinar flujos reactivos. 

```csharp
using System. Reactive.Linq;
using System. Reactive.Subjects;

public class OperadoresRxNet
{
    public static async Task EjemplosOperadores()
    {
        Console.WriteLine("\n=== OPERADORES DE RX.NET ===\n");

        // 1. CREACIÓN DE OBSERVABLES
        Console.WriteLine("--- Operadores de Creación ---");

        // Observable.Range - Emite una secuencia de números
        var range = Observable.Range(1, 5);
        range.Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // Observable. Interval - Emite cada X tiempo
        var intervalSub = Observable.Interval(TimeSpan.FromSeconds(1))
            .Take(3)
            .Subscribe(x => Console.WriteLine($"Tick {x}"));
        await Task.Delay(3500);
        intervalSub. Dispose();

        // Observable.Timer - Emite después de un delay
        Console.WriteLine("\nTimer (espera 1s)...");
        await Observable.Timer(TimeSpan.FromSeconds(1)).ToTask();
        Console.WriteLine("Timer completado");

        // 2. TRANSFORMACIÓN
        Console.WriteLine("\n--- Operadores de Transformación ---");

        // Select (Map) - Transforma cada elemento
        Observable.Range(1, 5)
            .Select(x => x * x)
            .Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // SelectMany (FlatMap) - Aplana observables anidados
        Observable.Range(1, 3)
            .SelectMany(x => Observable.Range(x, 2))
            .Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // 3. FILTRADO
        Console.WriteLine("\n--- Operadores de Filtrado ---");

        // Where - Filtra por condición
        Observable.Range(1, 10)
            .Where(x => x % 2 == 0)
            .Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // Take - Toma los primeros N elementos
        Observable. Range(1, 100)
            .Take(5)
            .Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // Skip - Omite los primeros N elementos
        Observable. Range(1, 10)
            .Skip(5)
            .Subscribe(x => Console.Write($"{x} "));
        Console.WriteLine("\n");

        // DistinctUntilChanged - Elimina duplicados consecutivos
        var subject = new Subject<int>();
        subject. DistinctUntilChanged()
            .Subscribe(x => Console.Write($"{x} "));
        
        subject.OnNext(1);
        subject.OnNext(1); // Ignorado
        subject.OnNext(2);
        subject.OnNext(2); // Ignorado
        subject.OnNext(1); // No ignorado (no es consecutivo)
        Console.WriteLine("\n");

        // 4. COMBINACIÓN
        Console.WriteLine("\n--- Operadores de Combinación ---");

        // Merge - Combina múltiples observables en uno
        var obs1 = Observable.Interval(TimeSpan.FromMilliseconds(500)).Select(x => $"A{x}").Take(3);
        var obs2 = Observable.Interval(TimeSpan.FromMilliseconds(700)).Select(x => $"B{x}").Take(3);

        var mergeSub = obs1.Merge(obs2)
            .Subscribe(x => Console.WriteLine($"Merge: {x}"));
        await Task.Delay(3000);
        mergeSub.Dispose();

        // Zip - Combina elementos en pares
        var numeros = Observable.Range(1, 5);
        var letras = Observable.Range(65, 5).Select(x => (char)x);

        numeros.Zip(letras, (n, l) => $"{n}-{l}")
            .Subscribe(x => Console.WriteLine($"Zip: {x}"));

        await Task.Delay(500);

        // CombineLatest - Combina los últimos valores de cada observable
        var temperature = new Subject<int>();
        var humidity = new Subject<int>();

        var climateSub = temperature
            .CombineLatest(humidity, (t, h) => $"Temp:  {t}°C, Humedad: {h}%")
            .Subscribe(x => Console. WriteLine($"CombineLatest: {x}"));

        temperature.OnNext(20);
        humidity.OnNext(60);    // Emite:  "Temp: 20°C, Humedad: 60%"
        temperature.OnNext(22); // Emite: "Temp: 22°C, Humedad: 60%"
        humidity.OnNext(65);    // Emite: "Temp: 22°C, Humedad: 65%"

        climateSub.Dispose();

        // 5. OPERADORES TEMPORALES
        Console.WriteLine("\n--- Operadores Temporales ---");

        // Throttle - Solo emite si no hay más emisiones en X tiempo
        var clicks = new Subject<int>();
        var throttleSub = clicks
            . Throttle(TimeSpan. FromMilliseconds(500))
            .Subscribe(x => Console.WriteLine($"Throttle: {x}"));

        clicks.OnNext(1);
        await Task.Delay(200);
        clicks.OnNext(2); // Ignorado (dentro de la ventana)
        await Task.Delay(200);
        clicks.OnNext(3); // Ignorado (dentro de la ventana)
        await Task.Delay(600); // Emite el último (3)
        throttleSub. Dispose();

        // Debounce (alias de Throttle)
        var search = new Subject<string>();
        var debounceSub = search
            .Debounce(TimeSpan.FromMilliseconds(300))
            .Subscribe(x => Console.WriteLine($"Buscando: {x}"));

        search.OnNext("H");
        await Task.Delay(100);
        search.OnNext("He");
        await Task.Delay(100);
        search.OnNext("Hel");
        await Task.Delay(400); // Emite "Hel"
        debounceSub.Dispose();

        // Buffer - Agrupa elementos en buffers
        Observable.Range(1, 10)
            .Buffer(3)
            .Subscribe(buffer => Console.WriteLine($"Buffer: [{string.Join(", ", buffer)}]"));

        // Window - Agrupa en observables
        Observable.Range(1, 10)
            .Window(3)
            .Subscribe(async window =>
            {
                var items = await window.ToList();
                Console.WriteLine($"Window: [{string.Join(", ", items)}]");
            });

        await Task.Delay(500);

        // 6. MANEJO DE ERRORES
        Console.WriteLine("\n--- Operadores de Manejo de Errores ---");

        // Catch - Maneja errores y proporciona un observable alternativo
        var faultyObs = Observable.Throw<int>(new Exception("Error simulado"))
            . Catch<int, Exception>(ex =>
            {
                Console.WriteLine($"Capturado: {ex.Message}");
                return Observable.Return(-1); // Observable alternativo
            });

        faultyObs.Subscribe(x => Console.WriteLine($"Resultado: {x}"));

        // Retry - Reintenta N veces en caso de error
        int intentos = 0;
        Observable.Create<int>(observer =>
        {
            intentos++;
            Console.WriteLine($"Intento {intentos}");
            
            if (intentos < 3)
            {
                observer.OnError(new Exception("Fallo temporal"));
            }
            else
            {
                observer.OnNext(42);
                observer.OnCompleted();
            }
            
            return Disposable.Empty;
        })
        .Retry(3)
        .Subscribe(
            x => Console.WriteLine($"Éxito: {x}"),
            ex => Console.WriteLine($"Error final: {ex.Message}")
        );

        // 7. UTILIDADES
        Console.WriteLine("\n--- Operadores de Utilidad ---");

        // Do (Tap) - Efectos secundarios sin modificar el flujo
        Observable.Range(1, 5)
            .Do(x => Console.WriteLine($"Antes: {x}"))
            .Select(x => x * 2)
            .Do(x => Console.WriteLine($"Después: {x}"))
            .Subscribe();

        // Timestamp - Añade marca de tiempo
        Observable. Interval(TimeSpan.FromMilliseconds(500))
            .Take(3)
            .Timestamp()
            .Subscribe(x => Console.WriteLine($"Valor {x. Value} a las {x.Timestamp:HH:mm: ss. fff}"));

        await Task.Delay(2000);
    }

    // Ejemplo práctico: Búsqueda con debounce
    public static async Task EjemploBusquedaConDebounce()
    {
        Console.WriteLine("\n=== BÚSQUEDA CON DEBOUNCE ===\n");

        var busquedaSubject = new Subject<string>();

        var suscripcion = busquedaSubject
            .Where(texto => ! string.IsNullOrWhiteSpace(texto))
            .DistinctUntilChanged()
            . Debounce(TimeSpan.FromMilliseconds(500))
            .SelectMany(async texto =>
            {
                Console.WriteLine($"🔍 Buscando:  {texto}");
                await Task.Delay(300); // Simular llamada a API
                return $"Resultados para '{texto}'";
            })
            .Subscribe(
                resultado => Console.WriteLine($"✅ {resultado}"),
                ex => Console.WriteLine($"❌ Error: {ex.Message}")
            );

        // Simular escritura del usuario
        busquedaSubject.OnNext("H");
        await Task.Delay(100);
        busquedaSubject. OnNext("He");
        await Task.Delay(100);
        busquedaSubject. OnNext("Hel");
        await Task.Delay(100);
        busquedaSubject.OnNext("Hell");
        await Task.Delay(100);
        busquedaSubject.OnNext("Hello");
        // Solo busca después de 500ms sin cambios

        await Task.Delay(1000);
        suscripcion.Dispose();
    }
}
```

**Cuándo usar cada uno:**

```csharp
public class GuiaDeUso
{
    // ✅ Usar IAsyncEnumerable cuando: 
    // - Lees datos de una base de datos con paginación
    // - Procesas archivos grandes línea por línea
    // - El consumidor necesita controlar el ritmo
    // - No necesitas múltiples suscriptores
    public async IAsyncEnumerable<Producto> CasoAsyncEnumerable()
    {
        await using var context = new ApplicationDbContext();
        
        await foreach (var producto in context. Productos.AsAsyncEnumerable())
        {
            yield return producto;
        }
    }

    // ✅ Usar IObservable cuando: 
    // - Manejas eventos de UI
    // - Trabajas con WebSockets o SSE
    // - Necesitas múltiples observadores
    // - Necesitas operadores temporales (throttle, debounce)
    // - El productor controla el ritmo
    public IObservable<Mensaje> CasoObservable()
    {
        return Observable.Create<Mensaje>(async observer =>
        {
            // Simular conexión WebSocket
            while (true)
            {
                var mensaje = await RecibirMensajeWebSocketAsync();
                observer.OnNext(mensaje);
            }
        });
    }

    private Task<Mensaje> RecibirMensajeWebSocketAsync() => Task.FromResult(new Mensaje());
}

public record Mensaje;
```

---

### 11. Railway Oriented Programming

#### 11.1. El problema de la gestión de errores con excepciones

Las **excepciones** son el mecanismo estándar de . NET para manejar situaciones excepcionales, como la falta de memoria o un disco inaccesible. Sin embargo, a menudo se usan para gestionar errores de negocio o de validación, lo que tiene varios inconvenientes:

1. **Interrumpen el flujo normal**:  Las excepciones rompen el control de flujo, haciendo que el código sea más difícil de seguir y de razonar. 
2. **Verbosidad del `try-catch`**: El manejo de excepciones puede llenar el código de bloques `try-catch`, haciéndolo más difícil de leer.
3. **Coste de rendimiento**:  Lanzar y capturar excepciones es una operación costosa en términos de rendimiento. 
4. **No fuerzan el manejo del error**: A diferencia de Java con sus checked exceptions, en C# las excepciones pueden propagarse sin ser capturadas, llevando a bugs en producción.

**Ejemplo del problema:**

```csharp
public class ServicioUsuarioConExcepciones
{
    public Usuario CrearUsuario(string email, string password, int edad)
    {
        // Problema 1: Muchos try-catch anidados
        try
        {
            ValidarEmail(email);
            
            try
            {
                ValidarPassword(password);
                
                try
                {
                    ValidarEdad(edad);
                    
                    // Lógica de negocio
                    var usuario = new Usuario(email, password, edad);
                    
                    try
                    {
                        GuardarEnBaseDeDatos(usuario);
                        return usuario;
                    }
                    catch (DatabaseException ex)
                    {
                        throw new Exception($"Error al guardar:  {ex.Message}");
                    }
                }
                catch (EdadInvalidaException ex)
                {
                    throw new Exception($"Edad inválida: {ex.Message}");
                }
            }
            catch (PasswordInvalidaException ex)
            {
                throw new Exception($"Password inválida: {ex.Message}");
            }
        }
        catch (EmailInvalidoException ex)
        {
            throw new Exception($"Email inválido: {ex.Message}");
        }
    }

    // Problema 2: No hay forma de saber qué métodos lanzan excepciones sin mirar su implementación
    private void ValidarEmail(string email) { /* ... */ }
    private void ValidarPassword(string password) { /* ... */ }
    private void ValidarEdad(int edad) { /* ... */ }
    private void GuardarEnBaseDeDatos(Usuario usuario) { /* ... */ }
}
```

#### 11.2. Introducción a Railway Oriented Programming

**Railway Oriented Programming (ROP)** es una técnica de programación funcional que ve el flujo de un programa como una vía de tren con dos raíles:

- **🟢 Raíl de éxito (Success Track)**: El flujo continúa cuando todas las operaciones tienen éxito. 
- **🔴 Raíl de error (Failure Track)**: El flujo salta a este raíl cuando ocurre un error y se mantiene ahí hasta el final.

La clave de este enfoque es que el **camino de error es un flujo de datos válido y esperado**, eliminando la necesidad de excepciones para errores comunes de negocio.

**Diagrama conceptual:**

```
          ┌──────────┐         ┌──────────┐         ┌──────────┐
Entrada ──┤ Validar  ├─Success─┤ Procesar ├─Success─┤ Guardar  ├── Success
          │  Email   │         │ Password │         │   BD     │
          └────┬─────┘         └────┬─────┘         └────┬─────┘
               │                    │                    │
             Failure              Failure              Failure
               │                    │                    │
               └────────────────────┴────────────────────┴───── Error
```

#### 11.3. La clase `Result` de CSharpFunctionalExtensions

Para implementar este modelo en . NET, utilizamos la librería **CSharpFunctionalExtensions** que proporciona la clase **`Result`** y **`Result<T>`**.

**Instalación:**

```bash
dotnet add package CSharpFunctionalExtensions
```

**`Result`** es un tipo de dato que representa uno de dos posibles estados:

- **`Result. Success()`**:  Representa una operación exitosa (sin valor de retorno).
- **`Result.Failure(error)`**: Representa un error con un mensaje descriptivo.

**`Result<T>`** es la versión genérica que encapsula un valor en caso de éxito: 

- **`Result.Success<T>(value)`**: Operación exitosa con un valor.
- **`Result.Failure<T>(error)`**: Error con un mensaje.

**Ejemplo básico:**

```csharp
using CSharpFunctionalExtensions;

namespace MiAplicacion. RailwayOriented;

public class EjemploBasico
{
    // Método que devuelve Result en lugar de lanzar excepciones
    public static Result<int> Dividir(int dividendo, int divisor)
    {
        if (divisor == 0)
        {
            return Result. Failure<int>("No se puede dividir entre cero");
        }

        return Result.Success(dividendo / divisor);
    }

    public static void Uso()
    {
        // Caso de éxito
        Result<int> resultado1 = Dividir(10, 2);
        
        if (resultado1.IsSuccess)
        {
            Console.WriteLine($"Resultado: {resultado1.Value}"); // 5
        }

        // Caso de error
        Result<int> resultado2 = Dividir(10, 0);
        
        if (resultado2.IsFailure)
        {
            Console.WriteLine($"Error: {resultado2.Error}"); // "No se puede dividir entre cero"
        }

        // Uso con pattern matching (C# 9+)
        string mensaje = Dividir(10, 2) switch
        {
            { IsSuccess: true } r => $"Éxito: {r.Value}",
            { IsFailure: true } r => $"Error: {r. Error}",
            _ => "Estado desconocido"
        };

        Console.WriteLine(mensaje);
    }
}
```

#### 11.4. Encadenamiento de operaciones y manejo de errores

El poder real de Railway Oriented Programming reside en la capacidad de **encadenar operaciones** usando métodos como **`Bind`**, **`Map`**, y **`Ensure`**.  Si una operación falla, el `Result` contendrá el error y las siguientes operaciones se ignorarán automáticamente.

**Ejemplo completo:  Creación de usuario con validaciones:**

```csharp
using CSharpFunctionalExtensions;

namespace MiAplicacion. RailwayOriented;

public record Usuario(string Email, string Password, int Edad);

public record UsuarioDto(string Email, string Password, int Edad);

public class ServicioUsuario
{
    // Paso 1: Validar email
    public static Result<string> ValidarEmail(string email)
    {
        if (string.IsNullOrWhiteSpace(email))
        {
            return Result.Failure<string>("El email no puede estar vacío");
        }

        if (!email.Contains("@"))
        {
            return Result.Failure<string>("El email debe contener @");
        }

        return Result.Success(email);
    }

    // Paso 2: Validar password
    public static Result<string> ValidarPassword(string password)
    {
        if (string.IsNullOrWhiteSpace(password))
        {
            return Result. Failure<string>("La password no puede estar vacía");
        }

        if (password.Length < 8)
        {
            return Result.Failure<string>("La password debe tener al menos 8 caracteres");
        }

        if (!password.Any(char.IsDigit))
        {
            return Result.Failure<string>("La password debe contener al menos un número");
        }

        return Result.Success(password);
    }

    // Paso 3: Validar edad
    public static Result<int> ValidarEdad(int edad)
    {
        if (edad < 18)
        {
            return Result.Failure<int>("Debes ser mayor de 18 años");
        }

        if (edad > 120)
        {
            return Result.Failure<int>("La edad no es realista");
        }

        return Result.Success(edad);
    }

    // Paso 4: Crear el usuario
    public static Result<Usuario> CrearUsuario(string email, string password, int edad)
    {
        var emailValidado = ValidarEmail(email);
        if (emailValidado.IsFailure)
        {
            return Result.Failure<Usuario>(emailValidado.Error);
        }

        var passwordValidada = ValidarPassword(password);
        if (passwordValidada.IsFailure)
        {
            return Result.Failure<Usuario>(passwordValidada.Error);
        }

        var edadValidada = ValidarEdad(edad);
        if (edadValidada.IsFailure)
        {
            return Result.Failure<Usuario>(edadValidada.Error);
        }

        var usuario = new Usuario(emailValidado.Value, passwordValidada.Value, edadValidada. Value);
        return Result.Success(usuario);
    }

    // ✅ VERSIÓN MEJORADA:  Encadenamiento con Bind
    public static Result<Usuario> CrearUsuarioConBind(string email, string password, int edad)
    {
        return ValidarEmail(email)
            .Bind(_ => ValidarPassword(password)
                .Bind(__ => ValidarEdad(edad)
                    .Map(___ => new Usuario(email, password, edad))));
    }

    // ✅ VERSIÓN AÚN MEJOR: Usando múltiples validaciones
    public static Result<Usuario> CrearUsuarioConEnsure(UsuarioDto dto)
    {
        return Result.Success(dto)
            .Ensure(d => ! string.IsNullOrWhiteSpace(d.Email), "El email no puede estar vacío")
            .Ensure(d => d.Email.Contains("@"), "El email debe contener @")
            .Ensure(d => ! string.IsNullOrWhiteSpace(d.Password), "La password no puede estar vacía")
            .Ensure(d => d.Password.Length >= 8, "La password debe tener al menos 8 caracteres")
            .Ensure(d => d. Password.Any(char.IsDigit), "La password debe contener al menos un número")
            .Ensure(d => d.Edad >= 18, "Debes ser mayor de 18 años")
            .Ensure(d => d.Edad <= 120, "La edad no es realista")
            .Map(d => new Usuario(d.Email, d. Password, d.Edad));
    }

    // Paso 5: Guardar en base de datos
    public static async Task<Result> GuardarEnBaseDeDatos(Usuario usuario)
    {
        try
        {
            // Simular guardado en BD
            await Task.Delay(100);
            
            // Simular posible error de BD (email duplicado)
            if (usuario.Email == "duplicado@example.com")
            {
                return Result.Failure("El email ya está registrado");
            }

            Console.WriteLine($"✅ Usuario guardado: {usuario.Email}");
            return Result.Success();
        }
        catch (Exception ex)
        {
            return Result.Failure($"Error de base de datos: {ex.Message}");
        }
    }

    // Flujo completo: Crear y guardar usuario
    public static async Task<Result<Usuario>> RegistrarUsuario(UsuarioDto dto)
    {
        return await CrearUsuarioConEnsure(dto)
            .Tap(usuario => Console.WriteLine($"Usuario creado: {usuario.Email}"))
            .Bind(async usuario =>
            {
                var resultadoGuardado = await GuardarEnBaseDeDatos(usuario);
                return resultadoGuardado. IsSuccess
                    ? Result.Success(usuario)
                    : Result. Failure<Usuario>(resultadoGuardado.Error);
            });
    }

    // Ejemplos de uso
    public static async Task EjemplosDeUso()
    {
        Console.WriteLine("=== RAILWAY ORIENTED PROGRAMMING ===\n");

        // ✅ Caso de éxito
        Console.WriteLine("--- Caso de Éxito ---");
        var resultado1 = await RegistrarUsuario(new UsuarioDto(
            "usuario@example.com",
            "Password123",
            25
        ));

        if (resultado1.IsSuccess)
        {
            Console.WriteLine($"✅ Usuario registrado: {resultado1.Value.Email}\n");
        }

        // ❌ Error:  Email inválido
        Console.WriteLine("--- Error: Email Inválido ---");
        var resultado2 = await RegistrarUsuario(new UsuarioDto(
            "email-sin-arroba",
            "Password123",
            25
        ));

        if (resultado2.IsFailure)
        {
            Console.WriteLine($"❌ Error:  {resultado2.Error}\n");
        }

        // ❌ Error: Password corta
        Console.WriteLine("--- Error: Password Corta ---");
        var resultado3 = await RegistrarUsuario(new UsuarioDto(
            "usuario@example.com",
            "Pass1",
            25
        ));

        if (resultado3.IsFailure)
        {
            Console.WriteLine($"❌ Error: {resultado3.Error}\n");
        }

        // ❌ Error: Edad inválida
        Console.WriteLine("--- Error: Edad Inválida ---");
        var resultado4 = await RegistrarUsuario(new UsuarioDto(
            "usuario@example.com",
            "Password123",
            15
        ));

        if (resultado4.IsFailure)
        {
            Console.WriteLine($"❌ Error: {resultado4.Error}\n");
        }

        // ❌ Error: Email duplicado (error de BD)
        Console.WriteLine("--- Error: Email Duplicado ---");
        var resultado5 = await RegistrarUsuario(new UsuarioDto(
            "duplicado@example.com",
            "Password123",
            25
        ));

        if (resultado5.IsFailure)
        {
            Console.WriteLine($"❌ Error: {resultado5.Error}\n");
        }
    }
}
```

#### 11.5. Más operadores clave de `Result`

Además de `Bind` y `Ensure`, CSharpFunctionalExtensions ofrece muchos operadores útiles para trabajar con el flujo de éxito y error:

```csharp
public class OperadoresResult
{
    // Map - Transforma el valor de éxito sin cambiar el tipo Result
    public static void EjemploMap()
    {
        Console.WriteLine("\n--- Operador Map ---");

        Result<int> resultado = Result.Success(10);

        // Map transforma el valor si es éxito
        Result<string> resultadoMapeado = resultado
            .Map(valor => $"El valor es: {valor}");

        Console.WriteLine(resultadoMapeado.Value); // "El valor es: 10"

        // Map en un error no tiene efecto
        Result<int> error = Result.Failure<int>("Error de validación");
        Result<string> errorMapeado = error. Map(valor => $"Valor:  {valor}");

        Console.WriteLine(errorMapeado.Error); // "Error de validación"
    }

    // Bind - Encadena operaciones que devuelven Result
    public static void EjemploBind()
    {
        Console.WriteLine("\n--- Operador Bind ---");

        Result<string> ProcesarA(int valor) => 
            valor > 0 
                ? Result.Success($"Procesado: {valor}") 
                : Result. Failure<string>("Valor debe ser positivo");

        Result<string> ProcesarB(string texto) => 
            texto.Length > 5 
                ? Result.Success(texto. ToUpper()) 
                : Result.Failure<string>("Texto muy corto");

        // Encadenar operaciones
        var resultado = Result.Success(10)
            .Bind(ProcesarA)
            .Bind(ProcesarB);

        if (resultado.IsSuccess)
        {
            Console.WriteLine($"✅ {resultado.Value}");
        }

        // Si alguna operación falla, el error se propaga
        var error = Result.Success(-5)
            .Bind(ProcesarA)
            .Bind(ProcesarB);

        if (error.IsFailure)
        {
            Console.WriteLine($"❌ {error.Error}");
        }
    }

    // Tap - Ejecuta una acción sin modificar el Result (útil para logging)
    public static void EjemploTap()
    {
        Console.WriteLine("\n--- Operador Tap ---");

        var resultado = Result.Success(42)
            .Tap(valor => Console.WriteLine($"Logging:  Valor procesado = {valor}"))
            .Map(valor => valor * 2)
            .Tap(valor => Console.WriteLine($"Logging: Valor después de Map = {valor}"));

        Console.WriteLine($"Resultado final: {resultado.Value}");
    }

    // TapError - Ejecuta una acción solo si hay error (útil para logging de errores)
    public static void EjemploTapError()
    {
        Console.WriteLine("\n--- Operador TapError ---");

        var resultado = Result. Failure<int>("Error crítico")
            .TapError(error => Console.WriteLine($"⚠️ Log de error: {error}"))
            .TapError(error => Console.WriteLine($"⚠️ Notificando al equipo: {error}"));

        Console.WriteLine($"Estado:  {(resultado.IsFailure ? "Fallo" : "Éxito")}");
    }

    // Ensure - Añade validaciones adicionales
    public static void EjemploEnsure()
    {
        Console.WriteLine("\n--- Operador Ensure ---");

        Result<int> ValidarNumero(int numero)
        {
            return Result.Success(numero)
                .Ensure(n => n > 0, "El número debe ser positivo")
                .Ensure(n => n < 100, "El número debe ser menor que 100")
                .Ensure(n => n % 2 == 0, "El número debe ser par");
        }

        var valido = ValidarNumero(50);
        Console.WriteLine($"50:  {(valido.IsSuccess ? "✅ Válido" : $"❌ {valido.Error}")}");

        var invalido = ValidarNumero(101);
        Console.WriteLine($"101: {(invalido.IsSuccess ? "✅ Válido" : $"❌ {invalido.Error}")}");
    }

    // Match - Maneja ambos casos (éxito y error) de forma funcional
    public static void EjemploMatch()
    {
        Console.WriteLine("\n--- Operador Match ---");

        Result<int> resultado = Result.Success(42);

        string mensaje = resultado. Match(
            onSuccess: valor => $"✅ Éxito: El valor es {valor}",
            onFailure: error => $"❌ Error: {error}"
        );

        Console.WriteLine(mensaje);

        // Con error
        Result<int> error = Result.Failure<int>("Algo falló");

        string mensajeError = error.Match(
            onSuccess: valor => $"✅ Éxito: {valor}",
            onFailure: error => $"❌ Error: {error}"
        );

        Console.WriteLine(mensajeError);
    }

    // Finally - Ejecuta una acción siempre, independientemente del resultado
    public static void EjemploFinally()
    {
        Console.WriteLine("\n--- Operador Finally ---");

        var resultado = Result. Success(100)
            .Tap(v => Console.WriteLine($"Procesando: {v}"))
            .Finally(r => Console.WriteLine($"Finally: Estado = {(r.IsSuccess ?  "Éxito" : "Error")}"));
    }

    // Check - Ejecuta una operación que devuelve Result sin cambiar el valor original
    public static async Task EjemploCheck()
    {
        Console.WriteLine("\n--- Operador Check ---");

        async Task<Result> EnviarNotificacion(Usuario usuario)
        {
            await Task.Delay(100);
            Console.WriteLine($"📧 Notificación enviada a {usuario.Email}");
            return Result.Success();
        }

        var resultado = await Result.Success(new Usuario("user@example.com", "Pass123", 25))
            .Check(async usuario => await EnviarNotificacion(usuario))
            .Map(usuario => $"Usuario {usuario.Email} registrado");

        if (resultado.IsSuccess)
        {
            Console.WriteLine($"✅ {resultado.Value}");
        }
    }

    // Combine - Combina múltiples Results en uno solo
    public static void EjemploCombine()
    {
        Console.WriteLine("\n--- Operador Combine ---");

        Result resultado1 = Result.Success();
        Result resultado2 = Result.Success();
        Result resultado3 = Result. Failure("Error en paso 3");

        // Si todos son éxito, devuelve Success
        var combinado1 = Result.Combine(resultado1, resultado2);
        Console.WriteLine($"Combinar 2 éxitos: {(combinado1.IsSuccess ? "✅ Éxito" : $"❌ {combinado1.Error}")}");

        // Si alguno falla, devuelve el primer error
        var combinado2 = Result.Combine(resultado1, resultado2, resultado3);
        Console.WriteLine($"Combinar con 1 error: {(combinado2.IsSuccess ? "✅ Éxito" : $"❌ {combinado2.Error}")}");
    }

    // OnSuccess / OnFailure - Ejecuta acciones condicionales
    public static void EjemploOnSuccessFailure()
    {
        Console.WriteLine("\n--- Operadores OnSuccess / OnFailure ---");

        var exito = Result.Success(42)
            .OnSuccess(valor => Console.WriteLine($"✅ OnSuccess: {valor}"))
            .OnFailure(error => Console.WriteLine($"❌ OnFailure:  {error}"));

        var fallo = Result.Failure<int>("Error de validación")
            .OnSuccess(valor => Console.WriteLine($"✅ OnSuccess: {valor}"))
            .OnFailure(error => Console.WriteLine($"❌ OnFailure: {error}"));
    }
}
```

**Ejemplo avanzado: Pipeline completo de procesamiento de pedido**

```csharp
public record Pedido(int Id, string ProductoId, int Cantidad, decimal PrecioUnitario);
public record PedidoValidado(Pedido Pedido, decimal Total);
public record PedidoProcesado(PedidoValidado Pedido, string NumeroTransaccion);

public class ServicioPedidos
{
    // Pipeline completo con Railway Oriented Programming
    public static async Task<Result<PedidoProcesado>> ProcesarPedido(Pedido pedido)
    {
        return await Result.Success(pedido)
            // Validaciones
            .Ensure(p => p. Cantidad > 0, "La cantidad debe ser mayor que cero")
            .Ensure(p => p. Cantidad <= 100, "La cantidad no puede superar 100 unidades")
            .Ensure(p => p.PrecioUnitario > 0, "El precio debe ser mayor que cero")
            .Tap(p => Console.WriteLine($"📋 Pedido validado: {p.Id}"))
            
            // Verificar stock
            .Bind(p => VerificarStock(p))
            . Tap(p => Console.WriteLine($"✅ Stock verificado para:  {p.ProductoId}"))
            
            // Calcular total
            .Map(p => new PedidoValidado(p, p. Cantidad * p.PrecioUnitario))
            .Tap(pv => Console.WriteLine($"💰 Total calculado: {pv.Total: C}"))
            
            // Procesar pago
            .Bind(async pv => await ProcesarPago(pv))
            .Tap(pp => Console.WriteLine($"💳 Pago procesado:  {pp.NumeroTransaccion}"))
            
            // Actualizar inventario
            .Check(async pp => await ActualizarInventario(pp. Pedido. Pedido))
            .Tap(pp => Console.WriteLine($"📦 Inventario actualizado"))
            
            // Enviar confirmación
            .Check(async pp => await EnviarConfirmacion(pp))
            .Tap(pp => Console.WriteLine($"📧 Confirmación enviada"));
    }

    private static Result<Pedido> VerificarStock(Pedido pedido)
    {
        // Simular verificación de stock
        if (pedido.ProductoId == "AGOTADO")
        {
            return Result.Failure<Pedido>($"Producto {pedido.ProductoId} sin stock");
        }

        return Result.Success(pedido);
    }

    private static async Task<Result<PedidoProcesado>> ProcesarPago(PedidoValidado pedido)
    {
        await Task.Delay(200); // Simular llamada a pasarela de pago

        if (pedido.Total > 10000)
        {
            return Result.Failure<PedidoProcesado>("Importe supera el límite permitido");
        }

        var numeroTransaccion = $"TRX-{Guid.NewGuid().ToString()[..8]. ToUpper()}";
        return Result.Success(new PedidoProcesado(pedido, numeroTransaccion));
    }

    private static async Task<Result> ActualizarInventario(Pedido pedido)
    {
        await Task.Delay(100); // Simular actualización de BD
        return Result.Success();
    }

    private static async Task<Result> EnviarConfirmacion(PedidoProcesado pedido)
    {
        await Task.Delay(100); // Simular envío de email
        return Result.Success();
    }

    public static async Task EjemploCompletoPipeline()
    {
        Console.WriteLine("\n=== PIPELINE COMPLETO DE PROCESAMIENTO ===\n");

        // ✅ Caso de éxito
        Console. WriteLine("--- Pedido Válido ---");
        var pedido1 = new Pedido(1, "LAPTOP-001", 2, 1200m);
        var resultado1 = await ProcesarPedido(pedido1);

        resultado1.Match(
            onSuccess: pp => Console.WriteLine($"✅ Pedido procesado exitosamente: {pp.NumeroTransaccion}\n"),
            onFailure:  error => Console.WriteLine($"❌ Error: {error}\n")
        );

        // ❌ Error: Cantidad inválida
        Console.WriteLine("--- Error: Cantidad Inválida ---");
        var pedido2 = new Pedido(2, "MOUSE-002", 0, 25m);
        var resultado2 = await ProcesarPedido(pedido2);

        resultado2.Match(
            onSuccess: pp => Console.WriteLine($"✅ Pedido procesado:  {pp.NumeroTransaccion}\n"),
            onFailure: error => Console.WriteLine($"❌ Error: {error}\n")
        );

        // ❌ Error: Sin stock
        Console.WriteLine("--- Error: Sin Stock ---");
        var pedido3 = new Pedido(3, "AGOTADO", 1, 50m);
        var resultado3 = await ProcesarPedido(pedido3);

        resultado3.Match(
            onSuccess: pp => Console.WriteLine($"✅ Pedido procesado: {pp.NumeroTransaccion}\n"),
            onFailure: error => Console.WriteLine($"❌ Error: {error}\n")
        );

        // ❌ Error:  Importe excesivo
        Console.WriteLine("--- Error: Importe Excesivo ---");
        var pedido4 = new Pedido(4, "SERVER-001", 20, 5000m);
        var resultado4 = await ProcesarPedido(pedido4);

        resultado4.Match(
            onSuccess: pp => Console.WriteLine($"✅ Pedido procesado: {pp.NumeroTransaccion}\n"),
            onFailure: error => Console. WriteLine($"❌ Error:  {error}\n")
        );
    }
}
```

#### 11.6. Beneficios y casos de uso en aplicaciones . NET

**Ventajas de Railway Oriented Programming:**

✅ **Mayor legibilidad**:  El código se lee como una secuencia de pasos clara.  El camino de error está integrado en el flujo. 

✅ **Manejo de errores explícito**: `Result` obliga al programador a considerar los dos posibles resultados (Success o Failure).

✅ **Sin excepciones para errores de negocio**: Las excepciones se reservan para situaciones verdaderamente excepcionales.

✅ **Composición funcional**: Fácil encadenamiento de operaciones con `Bind`, `Map`, etc.

✅ **Testeable**: Es mucho más fácil probar funciones que devuelven un resultado predecible. 

✅ **Rendimiento**: Evitar el coste de lanzar y capturar excepciones mejora el rendimiento.

**Casos de uso ideales:**

- 🔹 **Validación de datos**: Formularios, DTOs, entrada de usuario. 
- 🔹 **Flujos de negocio**: Procesos con múltiples pasos que pueden fallar.
- 🔹 **APIs REST**: Devolver respuestas estructuradas de éxito/error.
- 🔹 **Procesamiento de archivos**: Lectura, validación y transformación de datos.
- 🔹 **Integración con servicios externos**: Llamadas a APIs de terceros que pueden fallar. 

**Integración con ASP.NET Core:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    private readonly ServicioUsuario _servicio;

    public UsuariosController(ServicioUsuario servicio)
    {
        _servicio = servicio;
    }

    [HttpPost]
    public async Task<IActionResult> Registrar([FromBody] UsuarioDto dto)
    {
        var resultado = await ServicioUsuario.RegistrarUsuario(dto);

        // Convertir Result a IActionResult
        return resultado.Match<IActionResult>(
            onSuccess: usuario => Ok(new { mensaje = "Usuario registrado", usuario }),
            onFailure: error => BadRequest(new { error })
        );
    }
}
```

**Cuándo NO usar Railway Oriented Programming:**

❌ **Errores verdaderamente excepcionales**: Out of memory, disco lleno, etc.  (usa excepciones).

❌ **Código simple sin validaciones**: Si no hay múltiples pasos de validación, puede ser overkill.

❌ **Equipos sin experiencia funcional**: Requiere un cambio de mentalidad.

---

### 12. Clientes REST con HttpClient y Refit

En . NET, **HttpClient** es la clase base para realizar peticiones HTTP, mientras que **Refit** es una librería que simplifica enormemente la creación de clientes REST mediante interfaces declarativas y atributos.  Refit es el equivalente directo a Retrofit de Java.

**Instalación de paquetes necesarios:**

```bash
dotnet add package Refit
dotnet add package Refit.HttpClientFactory
dotnet add package System.Text.Json
```

#### 12.1. Configuración con Refit:    Interfaces y modelos de datos

Refit te permite definir la API como una **interfaz de C#** y usar **atributos** para describir las peticiones HTTP. Refit se encarga de todo el trabajo pesado:  serialización, deserialización, manejo de la red y construcción de URLs.

**Modelo de datos:**

```csharp
namespace MiAplicacion.Models;

public record ProductoDto
{
    public int Id { get; init; }
    public required string Nombre { get; init; }
    public decimal Precio { get; init; }
    public required string Categoria { get; init; }
    public DateTime FechaCreacion { get; init; }
}

public record CrearProductoRequest
{
    public required string Nombre { get; init; }
    public decimal Precio { get; init; }
    public required string Categoria { get; init; }
}

public record ActualizarProductoRequest
{
    public required string Nombre { get; init; }
    public decimal Precio { get; init; }
    public required string Categoria { get; init; }
}

public record ApiResponse<T>
{
    public bool Success { get; init; }
    public T?  Data { get; init; }
    public string?  Error { get; init; }
}
```

**Interfaz de la API con Refit:**

```csharp
using Refit;

namespace MiAplicacion.ApiClients;

public interface IProductosApi
{
    // GET:  Obtener todos los productos
    [Get("/api/productos")]
    Task<List<ProductoDto>> ObtenerTodosAsync();

    // GET: Obtener producto por ID
    [Get("/api/productos/{id}")]
    Task<ProductoDto> ObtenerPorIdAsync(int id);

    // GET: Búsqueda con query parameters
    [Get("/api/productos")]
    Task<List<ProductoDto>> BuscarAsync([Query] string?  categoria = null, [Query] decimal? precioMin = null);

    // POST: Crear un producto
    [Post("/api/productos")]
    Task<ProductoDto> CrearAsync([Body] CrearProductoRequest producto);

    // PUT: Actualizar un producto
    [Put("/api/productos/{id}")]
    Task<ProductoDto> ActualizarAsync(int id, [Body] ActualizarProductoRequest producto);

    // PATCH: Actualización parcial
    [Patch("/api/productos/{id}")]
    Task<ProductoDto> ActualizarParcialAsync(int id, [Body] Dictionary<string, object> cambios);

    // DELETE: Eliminar un producto
    [Delete("/api/productos/{id}")]
    Task EliminarAsync(int id);

    // GET: Con headers personalizados
    [Get("/api/productos/{id}")]
    [Headers("Accept: application/json", "User-Agent: MiApp")]
    Task<ProductoDto> ObtenerConHeadersAsync(int id);

    // POST: Con autorización
    [Post("/api/productos")]
    [Headers("Authorization: Bearer")]
    Task<ProductoDto> CrearConAuthAsync([Body] CrearProductoRequest producto, [Authorize("Bearer")] string token);

    // GET:  Devuelve la respuesta HTTP completa
    [Get("/api/productos/{id}")]
    Task<IApiResponse<ProductoDto>> ObtenerConRespuestaCompletaAsync(int id);

    // GET: Con paginación
    [Get("/api/productos")]
    Task<PaginatedResponse<ProductoDto>> ObtenerPaginadosAsync([Query] int page = 1, [Query] int pageSize = 10);
}

public record PaginatedResponse<T>
{
    public List<T> Items { get; init; } = new();
    public int TotalCount { get; init; }
    public int PageNumber { get; init; }
    public int PageSize { get; init; }
    public int TotalPages => (int)Math.Ceiling(TotalCount / (double)PageSize);
}
```

**Configuración en Program.cs (ASP.NET Core):**

```csharp
using Refit;
using MiAplicacion.ApiClients;
using System.Text.Json;
using System.Text.Json.Serialization;

var builder = WebApplication. CreateBuilder(args);

// Configurar opciones de serialización JSON
var jsonOptions = new JsonSerializerOptions
{
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
    DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingNull,
    Converters = { new JsonStringEnumConverter() }
};

// Configurar Refit con HttpClientFactory
builder.Services.AddRefitClient<IProductosApi>(new RefitSettings
{
    ContentSerializer = new SystemTextJsonContentSerializer(jsonOptions)
})
.ConfigureHttpClient(c =>
{
    c.BaseAddress = new Uri("https://api.example.com");
    c.Timeout = TimeSpan.FromSeconds(30);
})
// Políticas de resiliencia (opcional, requiere Polly)
.AddTransientHttpErrorPolicy(policy => 
    policy.WaitAndRetryAsync(3, retryAttempt => 
        TimeSpan.FromSeconds(Math.Pow(2, retryAttempt))))
.AddTransientHttpErrorPolicy(policy =>
    policy.CircuitBreakerAsync(5, TimeSpan.FromSeconds(30)));

var app = builder.Build();
app.Run();
```

**Configuración en aplicación de consola:**

```csharp
using Refit;
using MiAplicacion.ApiClients;
using System.Text.Json;

public class Program
{
    public static async Task Main(string[] args)
    {
        // Configurar opciones JSON
        var jsonOptions = new JsonSerializerOptions
        {
            PropertyNamingPolicy = JsonNamingPolicy.CamelCase
        };

        // Crear el cliente Refit
        var refitSettings = new RefitSettings
        {
            ContentSerializer = new SystemTextJsonContentSerializer(jsonOptions)
        };

        var api = RestService.For<IProductosApi>(
            "https://api.example.com",
            refitSettings
        );

        // Usar el cliente
        await EjemplosDeUso(api);
    }

    private static async Task EjemplosDeUso(IProductosApi api)
    {
        // Implementación de ejemplos
    }
}
```

#### 12.2. Operaciones CRUD con Refit

##### 12.2.1. CREATE:  Creación de un producto (`[Post]`)

```csharp
public class EjemploCreate
{
    public static async Task CrearProducto(IProductosApi api)
    {
        Console.WriteLine("\n=== CREATE: Crear Producto ===\n");

        var nuevoProducto = new CrearProductoRequest
        {
            Nombre = "Laptop Dell XPS 15",
            Precio = 1500.00m,
            Categoria = "Electrónica"
        };

        try
        {
            // Refit maneja automáticamente la serialización
            ProductoDto productoCreado = await api.CrearAsync(nuevoProducto);

            Console.WriteLine("✅ Producto creado con éxito:");
            Console. WriteLine($"   ID: {productoCreado.Id}");
            Console.WriteLine($"   Nombre: {productoCreado.Nombre}");
            Console.WriteLine($"   Precio: {productoCreado. Precio:C}");
            Console.WriteLine($"   Categoría: {productoCreado. Categoria}");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error HTTP {ex.StatusCode}: {ex. Content}");
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"❌ Error de red: {ex. Message}");
        }
    }

    // Crear con autorización
    public static async Task CrearConAutorizacion(IProductosApi api, string token)
    {
        var producto = new CrearProductoRequest
        {
            Nombre = "Teclado Mecánico",
            Precio = 89.99m,
            Categoria = "Accesorios"
        };

        try
        {
            var resultado = await api.CrearConAuthAsync(producto, token);
            Console.WriteLine($"✅ Producto creado:  {resultado.Nombre}");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.Unauthorized)
        {
            Console.WriteLine("❌ Error:  Token de autorización inválido");
        }
    }
}
```

##### 12.2.2. READ: Lectura de productos (`[Get]`)

```csharp
public class EjemploRead
{
    // Obtener todos los productos
    public static async Task ObtenerTodos(IProductosApi api)
    {
        Console.WriteLine("\n=== READ: Obtener Todos los Productos ===\n");

        try
        {
            List<ProductoDto> productos = await api.ObtenerTodosAsync();

            Console.WriteLine($"✅ Se encontraron {productos. Count} productos:\n");

            foreach (var producto in productos)
            {
                Console.WriteLine($"  • {producto.Nombre} - {producto.Precio:C} ({producto.Categoria})");
            }
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error:  {ex.Message}");
        }
    }

    // Obtener por ID
    public static async Task ObtenerPorId(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== READ: Obtener Producto ID {id} ===\n");

        try
        {
            ProductoDto producto = await api. ObtenerPorIdAsync(id);

            Console.WriteLine("✅ Producto encontrado:");
            Console.WriteLine($"   Nombre: {producto.Nombre}");
            Console.WriteLine($"   Precio: {producto. Precio:C}");
            Console.WriteLine($"   Categoría: {producto.Categoria}");
            Console.WriteLine($"   Fecha Creación: {producto.FechaCreacion:dd/MM/yyyy}");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            Console.WriteLine($"❌ Producto con ID {id} no encontrado");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error HTTP {ex.StatusCode}: {ex.Content}");
        }
    }

    // Búsqueda con filtros
    public static async Task BuscarConFiltros(IProductosApi api)
    {
        Console.WriteLine("\n=== READ: Buscar con Filtros ===\n");

        try
        {
            // Query parameters se pasan como argumentos del método
            var productos = await api.BuscarAsync(
                categoria: "Electrónica",
                precioMin: 100m
            );

            Console.WriteLine($"✅ Productos de 'Electrónica' con precio mínimo 100€:  {productos.Count}\n");

            foreach (var p in productos)
            {
                Console.WriteLine($"  • {p.Nombre}:  {p.Precio:C}");
            }
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error: {ex. Message}");
        }
    }

    // Obtener con respuesta completa (headers, status code, etc.)
    public static async Task ObtenerConRespuestaCompleta(IProductosApi api, int id)
    {
        Console.WriteLine("\n=== READ: Con Respuesta Completa ===\n");

        try
        {
            IApiResponse<ProductoDto> respuesta = await api.ObtenerConRespuestaCompletaAsync(id);

            Console. WriteLine($"Status Code: {respuesta.StatusCode}");
            Console.WriteLine($"Headers: {string.Join(", ", respuesta. Headers.Select(h => h. Key))}");

            if (respuesta.IsSuccessStatusCode && respuesta.Content != null)
            {
                Console.WriteLine($"✅ Producto:  {respuesta.Content.Nombre}");
            }
        }
        catch (ApiException ex)
        {
            Console. WriteLine($"❌ Error:  {ex.Message}");
        }
    }

    // Paginación
    public static async Task ObtenerPaginados(IProductosApi api)
    {
        Console.WriteLine("\n=== READ: Con Paginación ===\n");

        int paginaActual = 1;
        int pageSize = 10;

        try
        {
            var respuesta = await api.ObtenerPaginadosAsync(paginaActual, pageSize);

            Console.WriteLine($"📄 Página {respuesta.PageNumber} de {respuesta.TotalPages}");
            Console.WriteLine($"📊 Total de productos: {respuesta.TotalCount}\n");

            foreach (var producto in respuesta.Items)
            {
                Console.WriteLine($"  • {producto.Nombre} - {producto.Precio:C}");
            }

            // Navegar a la siguiente página
            if (paginaActual < respuesta. TotalPages)
            {
                Console.WriteLine("\n➡️  Cargando siguiente página...\n");
                var siguientePagina = await api.ObtenerPaginadosAsync(paginaActual + 1, pageSize);
                
                foreach (var producto in siguientePagina.Items)
                {
                    Console.WriteLine($"  • {producto.Nombre} - {producto.Precio:C}");
                }
            }
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error: {ex.Message}");
        }
    }
}
```

##### 12.2.3. UPDATE:    Actualización de un producto (`[Put]`)

```csharp
public class EjemploUpdate
{
    // Actualización completa (PUT)
    public static async Task ActualizarCompleto(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== UPDATE: Actualizar Producto ID {id} ===\n");

        var productoActualizado = new ActualizarProductoRequest
        {
            Nombre = "Laptop Dell XPS 15 (Actualizado)",
            Precio = 1399.99m,
            Categoria = "Electrónica"
        };

        try
        {
            ProductoDto resultado = await api.ActualizarAsync(id, productoActualizado);

            Console.WriteLine("✅ Producto actualizado con éxito:");
            Console. WriteLine($"   Nombre: {resultado.Nombre}");
            Console.WriteLine($"   Precio: {resultado.Precio:C}");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            Console.WriteLine($"❌ Producto con ID {id} no encontrado");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error HTTP {ex.StatusCode}: {ex.Content}");
        }
    }

    // Actualización parcial (PATCH)
    public static async Task ActualizarParcial(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== UPDATE:  Actualización Parcial ID {id} ===\n");

        // Solo actualizar el precio
        var cambios = new Dictionary<string, object>
        {
            { "precio", 1299.99m }
        };

        try
        {
            ProductoDto resultado = await api.ActualizarParcialAsync(id, cambios);

            Console.WriteLine("✅ Precio actualizado:");
            Console.WriteLine($"   Nuevo precio: {resultado. Precio:C}");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error: {ex.Message}");
        }
    }

    // Actualización con validación previa
    public static async Task ActualizarConValidacion(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== UPDATE: Con Validación Previa ===\n");

        try
        {
            // Primero obtener el producto actual
            var productoActual = await api.ObtenerPorIdAsync(id);
            Console.WriteLine($"📋 Producto actual: {productoActual.Nombre} - {productoActual.Precio:C}");

            // Validar que existe y actualizar
            var productoActualizado = new ActualizarProductoRequest
            {
                Nombre = productoActual.Nombre,
                Precio = productoActual.Precio * 0.9m, // 10% de descuento
                Categoria = productoActual. Categoria
            };

            var resultado = await api.ActualizarAsync(id, productoActualizado);

            Console.WriteLine($"✅ Descuento aplicado.  Nuevo precio: {resultado. Precio:C}");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            Console.WriteLine($"❌ Producto no encontrado");
        }
    }
}
```

##### 12.2.4. DELETE:  Eliminación de un producto (`[Delete]`)

```csharp
public class EjemploDelete
{
    public static async Task EliminarProducto(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== DELETE: Eliminar Producto ID {id} ===\n");

        try
        {
            await api.EliminarAsync(id);
            Console.WriteLine($"✅ Producto con ID {id} eliminado con éxito");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            Console.WriteLine($"❌ Producto con ID {id} no encontrado");
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.Conflict)
        {
            Console. WriteLine($"❌ No se puede eliminar:  El producto tiene dependencias");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error HTTP {ex.StatusCode}: {ex.Content}");
        }
    }

    // Eliminación con confirmación
    public static async Task EliminarConConfirmacion(IProductosApi api, int id)
    {
        Console.WriteLine($"\n=== DELETE: Con Confirmación ===\n");

        try
        {
            // Obtener el producto primero
            var producto = await api.ObtenerPorIdAsync(id);
            
            Console.WriteLine($"⚠️  Estás a punto de eliminar: {producto.Nombre}");
            Console.Write("¿Confirmar?  (s/n): ");
            
            var confirmacion = Console.ReadLine();
            
            if (confirmacion?.ToLower() == "s")
            {
                await api.EliminarAsync(id);
                Console.WriteLine("✅ Producto eliminado");
            }
            else
            {
                Console. WriteLine("❌ Operación cancelada");
            }
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            Console.WriteLine($"❌ Producto no encontrado");
        }
    }

    // Eliminación en lote
    public static async Task EliminarEnLote(IProductosApi api, List<int> ids)
    {
        Console. WriteLine($"\n=== DELETE:  Eliminación en Lote ===\n");
        Console.WriteLine($"Eliminando {ids.Count} productos.. .\n");

        var resultados = new List<(int Id, bool Exito, string?  Error)>();

        foreach (var id in ids)
        {
            try
            {
                await api.EliminarAsync(id);
                resultados.Add((id, true, null));
                Console.WriteLine($"  ✅ Producto {id} eliminado");
            }
            catch (ApiException ex)
            {
                resultados.Add((id, false, ex.Message));
                Console.WriteLine($"  ❌ Producto {id}:  {ex.StatusCode}");
            }
        }

        // Resumen
        var exitosos = resultados.Count(r => r.Exito);
        var fallidos = resultados.Count(r => !r.Exito);

        Console.WriteLine($"\n📊 Resumen:");
        Console.WriteLine($"   Exitosos: {exitosos}");
        Console.WriteLine($"   Fallidos: {fallidos}");
    }
}
```

#### 12.3. Manejo de respuestas y errores

Refit proporciona varias formas de manejar errores y respuestas de forma elegante:

```csharp
public class ManejoDeErrores
{
    // Manejo básico con try-catch
    public static async Task ManejoBasico(IProductosApi api)
    {
        Console.WriteLine("\n=== MANEJO DE ERRORES: Básico ===\n");

        try
        {
            var producto = await api.ObtenerPorIdAsync(999);
            Console.WriteLine($"✅ Producto:  {producto.Nombre}");
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error API:");
            Console.WriteLine($"   Status Code: {ex.StatusCode}");
            Console.WriteLine($"   Mensaje: {ex.Message}");
            Console.WriteLine($"   Contenido: {ex.Content}");
            Console.WriteLine($"   Uri: {ex.Uri}");
        }
        catch (HttpRequestException ex)
        {
            Console.WriteLine($"❌ Error de red:  {ex.Message}");
        }
        catch (TaskCanceledException ex)
        {
            Console.WriteLine($"❌ Timeout: La operación tardó demasiado");
        }
    }

    // Manejo con pattern matching
    public static async Task ManejoConPatternMatching(IProductosApi api, int id)
    {
        Console.WriteLine("\n=== MANEJO DE ERRORES: Pattern Matching ===\n");

        try
        {
            var producto = await api.ObtenerPorIdAsync(id);
            Console.WriteLine($"✅ Producto: {producto.Nombre}");
        }
        catch (ApiException ex)
        {
            var mensaje = ex.StatusCode switch
            {
                System.Net.HttpStatusCode.NotFound => "Producto no encontrado",
                System.Net.HttpStatusCode.BadRequest => "Solicitud inválida",
                System.Net.HttpStatusCode.Unauthorized => "No autorizado",
                System.Net.HttpStatusCode.Forbidden => "Acceso prohibido",
                System.Net.HttpStatusCode.InternalServerError => "Error del servidor",
                System.Net.HttpStatusCode.ServiceUnavailable => "Servicio no disponible",
                _ => $"Error desconocido: {ex.StatusCode}"
            };

            Console.WriteLine($"❌ {mensaje}");
        }
    }

    // Manejo con Result (Railway Oriented Programming)
    public static async Task<Result<ProductoDto>> ManejoConResult(IProductosApi api, int id)
    {
        Console.WriteLine("\n=== MANEJO DE ERRORES: Con Result ===\n");

        try
        {
            var producto = await api.ObtenerPorIdAsync(id);
            return Result.Success(producto);
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            return Result.Failure<ProductoDto>("Producto no encontrado");
        }
        catch (ApiException ex)
        {
            return Result.Failure<ProductoDto>($"Error de API: {ex.StatusCode}");
        }
        catch (HttpRequestException ex)
        {
            return Result.Failure<ProductoDto>($"Error de conexión: {ex.Message}");
        }
    }

    // Manejo con reintentos automáticos
    public static async Task<ProductoDto? > ManejoConReintentos(IProductosApi api, int id, int maxReintentos = 3)
    {
        Console.WriteLine("\n=== MANEJO DE ERRORES: Con Reintentos ===\n");

        for (int intento = 1; intento <= maxReintentos; intento++)
        {
            try
            {
                Console.WriteLine($"Intento {intento} de {maxReintentos}.. .");
                var producto = await api.ObtenerPorIdAsync(id);
                Console.WriteLine($"✅ Éxito en intento {intento}");
                return producto;
            }
            catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
            {
                // No reintentar para 404
                Console.WriteLine("❌ Producto no encontrado (no se reintentará)");
                return null;
            }
            catch (ApiException ex) when (
                ex.StatusCode == System. Net.HttpStatusCode.InternalServerError ||
                ex. StatusCode == System.Net.HttpStatusCode.ServiceUnavailable)
            {
                Console.WriteLine($"⚠️  Error {ex.StatusCode} en intento {intento}");

                if (intento < maxReintentos)
                {
                    var espera = TimeSpan.FromSeconds(Math.Pow(2, intento)); // Backoff exponencial
                    Console.WriteLine($"Esperando {espera.TotalSeconds}s antes de reintentar...");
                    await Task.Delay(espera);
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine($"❌ Error inesperado: {ex. Message}");
                return null;
            }
        }

        Console.WriteLine($"❌ Se agotaron los {maxReintentos} intentos");
        return null;
    }

    // Manejo con deserialización de errores personalizados
    public static async Task ManejoConErroresPersonalizados(IProductosApi api, int id)
    {
        Console.WriteLine("\n=== MANEJO DE ERRORES: Errores Personalizados ===\n");

        try
        {
            var producto = await api. ObtenerPorIdAsync(id);
            Console.WriteLine($"✅ Producto: {producto.Nombre}");
        }
        catch (ApiException ex)
        {
            // Intentar deserializar el error de la API
            try
            {
                var errorResponse = JsonSerializer.Deserialize<ApiErrorResponse>(ex.Content ??  "{}");

                if (errorResponse != null)
                {
                    Console.WriteLine($"❌ Error de API:");
                    Console.WriteLine($"   Código: {errorResponse. Code}");
                    Console. WriteLine($"   Mensaje: {errorResponse.Message}");
                    Console.WriteLine($"   Detalles: {string.Join(", ", errorResponse. Details ??  new List<string>())}");
                }
            }
            catch
            {
                Console.WriteLine($"❌ Error:  {ex.Message}");
            }
        }
    }

    // Manejo con timeout
    public static async Task<ProductoDto?> ManejoConTimeout(IProductosApi api, int id, int timeoutSegundos = 5)
    {
        Console. WriteLine($"\n=== MANEJO DE ERRORES: Con Timeout ({timeoutSegundos}s) ===\n");

        using var cts = new CancellationTokenSource(TimeSpan.FromSeconds(timeoutSegundos));

        try
        {
            // Nota: Necesitarías modificar la interfaz para aceptar CancellationToken
            var producto = await api.ObtenerPorIdAsync(id);
            Console.WriteLine($"✅ Producto obtenido: {producto.Nombre}");
            return producto;
        }
        catch (TaskCanceledException)
        {
            Console.WriteLine($"❌ Timeout: La operación tardó más de {timeoutSegundos} segundos");
            return null;
        }
        catch (ApiException ex)
        {
            Console.WriteLine($"❌ Error API: {ex.StatusCode}");
            return null;
        }
    }
}

public record ApiErrorResponse
{
    public string Code { get; init; } = string.Empty;
    public string Message { get; init; } = string.Empty;
    public List<string>? Details { get; init; }
}
```

**Ejemplo completo de uso integrado:**

```csharp
public class ServicioProductos
{
    private readonly IProductosApi _api;

    public ServicioProductos(IProductosApi api)
    {
        _api = api;
    }

    public async Task<Result<List<ProductoDto>>> ObtenerProductosBaratos()
    {
        try
        {
            var productos = await _api.BuscarAsync(precioMin: 0, precioMax: 100);
            return Result.Success(productos);
        }
        catch (ApiException ex)
        {
            return Result.Failure<List<ProductoDto>>($"Error al obtener productos:  {ex.StatusCode}");
        }
    }

    public async Task<Result<ProductoDto>> CrearYValidar(CrearProductoRequest request)
    {
        return await Result.Success(request)
            . Ensure(r => ! string.IsNullOrWhiteSpace(r.Nombre), "El nombre es requerido")
            .Ensure(r => r.Precio > 0, "El precio debe ser mayor que cero")
            .Bind(async r =>
            {
                try
                {
                    var productoCreado = await _api.CrearAsync(r);
                    return Result.Success(productoCreado);
                }
                catch (ApiException ex)
                {
                    return Result.Failure<ProductoDto>($"Error al crear:  {ex.Message}");
                }
            });
    }

    public async Task EjemploCompleto()
    {
        Console.WriteLine("\n=== EJEMPLO COMPLETO DE USO ===\n");

        // 1. Crear producto
        var resultado = await CrearYValidar(new CrearProductoRequest
        {
            Nombre = "Ratón Logitech",
            Precio = 29.99m,
            Categoria = "Accesorios"
        });

        resultado.Match(
            onSuccess: p => Console.WriteLine($"✅ Creado: {p.Nombre} (ID: {p.Id})"),
            onFailure: error => Console.WriteLine($"❌ {error}")
        );

        if (resultado.IsSuccess)
        {
            int idCreado = resultado.Value.Id;

            // 2. Obtener el producto
            try
            {
                var producto = await _api.ObtenerPorIdAsync(idCreado);
                Console.WriteLine($"✅ Obtenido: {producto.Nombre}");

                // 3. Actualizar
                await _api.ActualizarAsync(idCreado, new ActualizarProductoRequest
                {
                    Nombre = producto.Nombre,
                    Precio = 24.99m, // Rebaja
                    Categoria = producto.Categoria
                });
                Console.WriteLine("✅ Precio actualizado");

                // 4. Eliminar
                await _api.EliminarAsync(idCreado);
                Console.WriteLine("✅ Producto eliminado");
            }
            catch (ApiException ex)
            {
                Console.WriteLine($"❌ Error: {ex.StatusCode}");
            }
        }
    }
}
```

---

### 13. Pruebas y herramientas

Las pruebas son esenciales en el desarrollo de software para asegurar que el código se comporta como se espera. Las **pruebas unitarias** verifican el comportamiento de las unidades de código más pequeñas, como las clases o los métodos.

#### 13.1. Pruebas unitarias

Una buena práctica en el desarrollo de pruebas es seguir el patrón **AAA (Arrange, Act, Assert)**.  Este patrón divide cada prueba en tres partes claras y distintas:

- **Arrange** (Preparar): Configura los objetos y prepara el entorno para la prueba. 
- **Act** (Actuar): Ejecuta el método o la acción que se va a probar.
- **Assert** (Afirmar): Verifica que el resultado de la acción es el esperado.

**Instalación de paquetes NuGet necesarios:**

```bash
dotnet add package NUnit
dotnet add package NUnit3TestAdapter
dotnet add package Microsoft.NET.Test.Sdk
dotnet add package Moq
dotnet add package FluentAssertions
```

##### 13.1.1. NUnit para pruebas unitarias

**NUnit** es uno de los frameworks de testing más populares en . NET.   Proporciona atributos para definir tests y una rica API de assertions.

**Atributos principales de NUnit:**

- `[TestFixture]`: Marca una clase que contiene tests. 
- `[Test]`: Marca un método como un test.
- `[SetUp]`: Se ejecuta antes de cada test. 
- `[TearDown]`: Se ejecuta después de cada test. 
- `[OneTimeSetUp]`: Se ejecuta una vez antes de todos los tests de la clase.
- `[OneTimeTearDown]`: Se ejecuta una vez después de todos los tests de la clase. 
- `[TestCase]`: Permite ejecutar el mismo test con diferentes parámetros. 
- `[Category]`: Agrupa tests por categoría. 
- `[Ignore]`: Ignora temporalmente un test. 

**Ejemplo básico con NUnit:**

```csharp
using NUnit.Framework;

namespace MiAplicacion.Tests;

public class Calculadora
{
    public int Sumar(int a, int b) => a + b;
    public int Restar(int a, int b) => a - b;
    public int Multiplicar(int a, int b) => a * b;
    public double Dividir(int a, int b)
    {
        if (b == 0)
            throw new DivideByZeroException("No se puede dividir entre cero");
        
        return (double)a / b;
    }
}

[TestFixture]
public class CalculadoraTests
{
    private Calculadora _calculadora = null!;

    [SetUp]
    public void SetUp()
    {
        // Arrange: Se ejecuta antes de cada test
        _calculadora = new Calculadora();
        Console.WriteLine("SetUp: Calculadora inicializada");
    }

    [TearDown]
    public void TearDown()
    {
        // Se ejecuta después de cada test
        Console.WriteLine("TearDown: Test completado\n");
    }

    [Test]
    public void Sumar_DosNumeros_DebeRetornarLaSuma()
    {
        // Arrange
        int numero1 = 5;
        int numero2 = 3;
        int resultadoEsperado = 8;

        // Act
        int resultadoActual = _calculadora.Sumar(numero1, numero2);

        // Assert
        Assert. That(resultadoActual, Is.EqualTo(resultadoEsperado));
    }

    [Test]
    public void Restar_DosNumeros_DebeRetornarLaDiferencia()
    {
        // Arrange
        int numero1 = 10;
        int numero2 = 3;

        // Act
        int resultado = _calculadora.Restar(numero1, numero2);

        // Assert
        Assert.That(resultado, Is.EqualTo(7));
    }

    // TestCase permite ejecutar el mismo test con diferentes valores
    [TestCase(2, 3, 6)]
    [TestCase(5, 4, 20)]
    [TestCase(-2, 3, -6)]
    [TestCase(0, 100, 0)]
    public void Multiplicar_DosNumeros_DebeRetornarElProducto(int a, int b, int esperado)
    {
        // Act
        int resultado = _calculadora.Multiplicar(a, b);

        // Assert
        Assert.That(resultado, Is.EqualTo(esperado));
    }

    [Test]
    public void Dividir_DosNumeros_DebeRetornarElCociente()
    {
        // Arrange
        int dividendo = 10;
        int divisor = 2;

        // Act
        double resultado = _calculadora.Dividir(dividendo, divisor);

        // Assert
        Assert. That(resultado, Is.EqualTo(5.0));
    }

    [Test]
    public void Dividir_EntreZero_DebeLanzarExcepcion()
    {
        // Arrange
        int dividendo = 10;
        int divisor = 0;

        // Act & Assert
        Assert.Throws<DivideByZeroException>(() => _calculadora.Dividir(dividendo, divisor));
    }

    [Test]
    public void Dividir_EntreZero_DebeLanzarExcepcionConMensajeEspecifico()
    {
        // Act & Assert
        var ex = Assert.Throws<DivideByZeroException>(() => _calculadora.Dividir(10, 0));
        Assert.That(ex.Message, Is.EqualTo("No se puede dividir entre cero"));
    }

    [Test]
    [Category("Operaciones Básicas")]
    public void Sumar_NumerosNegativos_DebeRetornarResultadoCorrecto()
    {
        // Arrange
        int a = -5;
        int b = -3;

        // Act
        int resultado = _calculadora. Sumar(a, b);

        // Assert
        Assert.That(resultado, Is.EqualTo(-8));
    }

    [Test]
    [Ignore("Test en desarrollo")]
    public void TestEnDesarrollo()
    {
        Assert. Fail("Este test aún no está implementado");
    }
}
```

**Assertions comunes de NUnit:**

```csharp
[TestFixture]
public class EjemplosAssertions
{
    [Test]
    public void Assertions_Igualdad()
    {
        int valor = 42;

        // Igualdad
        Assert.That(valor, Is.EqualTo(42));
        Assert.That(valor, Is.Not.EqualTo(100));
    }

    [Test]
    public void Assertions_Comparacion()
    {
        int valor = 10;

        // Comparaciones
        Assert.That(valor, Is.GreaterThan(5));
        Assert.That(valor, Is.LessThan(20));
        Assert.That(valor, Is.GreaterThanOrEqualTo(10));
        Assert.That(valor, Is.InRange(5, 15));
    }

    [Test]
    public void Assertions_Strings()
    {
        string texto = "Hola Mundo";

        // Strings
        Assert.That(texto, Is.EqualTo("Hola Mundo"));
        Assert.That(texto, Does.Contain("Mundo"));
        Assert.That(texto, Does.StartWith("Hola"));
        Assert.That(texto, Does. EndWith("Mundo"));
        Assert.That(texto, Is.Not.Empty);
    }

    [Test]
    public void Assertions_Colecciones()
    {
        var lista = new List<int> { 1, 2, 3, 4, 5 };

        // Colecciones
        Assert.That(lista, Has.Count.EqualTo(5));
        Assert.That(lista, Does.Contain(3));
        Assert.That(lista, Is.Ordered);
        Assert.That(lista, Is.All.GreaterThan(0));
        Assert.That(lista, Is. Unique);
    }

    [Test]
    public void Assertions_Null()
    {
        string?  textoNulo = null;
        string textoNoNulo = "Valor";

        // Null
        Assert.That(textoNulo, Is.Null);
        Assert.That(textoNoNulo, Is.Not. Null);
    }

    [Test]
    public void Assertions_Tipos()
    {
        object objeto = "texto";

        // Tipos
        Assert. That(objeto, Is.TypeOf<string>());
        Assert.That(objeto, Is. InstanceOf<string>());
    }

    [Test]
    public void Assertions_Booleanos()
    {
        bool verdadero = true;
        bool falso = false;

        // Booleanos
        Assert.That(verdadero, Is.True);
        Assert.That(falso, Is.False);
    }
}
```

##### 13.1.2. Moq para la creación de objetos simulados (mocks)

Cuando una clase a probar depende de otras, es difícil aislar su comportamiento. **Moq** es un framework de mocking que permite crear **objetos simulados (mocks)** para simular el comportamiento de las dependencias.

**Ejemplo sin mocks (dependencia real):**

```csharp
public interface IRepositorioUsuario
{
    Usuario?  ObtenerPorEmail(string email);
    bool ExisteUsuario(string email);
    void Guardar(Usuario usuario);
}

public record Usuario(string Email, string Nombre, int Edad);

public class ServicioRegistro
{
    private readonly IRepositorioUsuario _repositorio;

    public ServicioRegistro(IRepositorioUsuario repositorio)
    {
        _repositorio = repositorio;
    }

    public Result<Usuario> RegistrarUsuario(string email, string nombre, int edad)
    {
        // Validaciones
        if (string.IsNullOrWhiteSpace(email))
            return Result.Failure<Usuario>("El email es requerido");

        if (! email.Contains("@"))
            return Result. Failure<Usuario>("Email inválido");

        if (edad < 18)
            return Result.Failure<Usuario>("Debe ser mayor de 18 años");

        // Verificar si ya existe
        if (_repositorio.ExisteUsuario(email))
            return Result.Failure<Usuario>("El email ya está registrado");

        // Crear usuario
        var usuario = new Usuario(email, nombre, edad);

        // Guardar
        _repositorio.Guardar(usuario);

        return Result. Success(usuario);
    }
}
```

**Tests con Moq:**

```csharp
using Moq;
using NUnit.Framework;

namespace MiAplicacion.Tests;

[TestFixture]
public class ServicioRegistroTests
{
    private Mock<IRepositorioUsuario> _mockRepositorio = null!;
    private ServicioRegistro _servicio = null!;

    [SetUp]
    public void SetUp()
    {
        // Arrange:  Crear el mock del repositorio
        _mockRepositorio = new Mock<IRepositorioUsuario>();
        
        // Crear la instancia del servicio con el mock
        _servicio = new ServicioRegistro(_mockRepositorio.Object);
    }

    [Test]
    public void RegistrarUsuario_DatosValidos_DebeRetornarExito()
    {
        // Arrange
        string email = "usuario@example.com";
        string nombre = "Juan Pérez";
        int edad = 25;

        // Configurar el comportamiento del mock
        _mockRepositorio
            .Setup(r => r. ExisteUsuario(email))
            .Returns(false); // Simular que NO existe

        // Act
        var resultado = _servicio.RegistrarUsuario(email, nombre, edad);

        // Assert
        Assert.That(resultado.IsSuccess, Is.True);
        Assert.That(resultado.Value.Email, Is.EqualTo(email));
        Assert.That(resultado. Value.Nombre, Is.EqualTo(nombre));

        // Verificar que el método Guardar fue llamado exactamente 1 vez
        _mockRepositorio.Verify(r => r.Guardar(It.IsAny<Usuario>()), Times.Once);
    }

    [Test]
    public void RegistrarUsuario_EmailVacio_DebeRetornarError()
    {
        // Arrange
        string email = "";
        string nombre = "Juan";
        int edad = 25;

        // Act
        var resultado = _servicio. RegistrarUsuario(email, nombre, edad);

        // Assert
        Assert.That(resultado. IsFailure, Is.True);
        Assert.That(resultado. Error, Is.EqualTo("El email es requerido"));

        // Verificar que NO se llamó a Guardar
        _mockRepositorio. Verify(r => r.Guardar(It.IsAny<Usuario>()), Times.Never);
    }

    [Test]
    public void RegistrarUsuario_EmailInvalido_DebeRetornarError()
    {
        // Arrange
        string email = "email-sin-arroba";

        // Act
        var resultado = _servicio.RegistrarUsuario(email, "Juan", 25);

        // Assert
        Assert.That(resultado. IsFailure, Is.True);
        Assert.That(resultado. Error, Is.EqualTo("Email inválido"));
    }

    [Test]
    public void RegistrarUsuario_MenorDeEdad_DebeRetornarError()
    {
        // Arrange
        string email = "menor@example.com";
        int edad = 15;

        _mockRepositorio. Setup(r => r.ExisteUsuario(email)).Returns(false);

        // Act
        var resultado = _servicio.RegistrarUsuario(email, "Menor", edad);

        // Assert
        Assert.That(resultado.IsFailure, Is.True);
        Assert.That(resultado.Error, Is.EqualTo("Debe ser mayor de 18 años"));
    }

    [Test]
    public void RegistrarUsuario_EmailYaExiste_DebeRetornarError()
    {
        // Arrange
        string email = "existente@example.com";

        // Simular que el usuario YA existe
        _mockRepositorio
            .Setup(r => r.ExisteUsuario(email))
            .Returns(true);

        // Act
        var resultado = _servicio.RegistrarUsuario(email, "Usuario", 25);

        // Assert
        Assert.That(resultado. IsFailure, Is.True);
        Assert.That(resultado. Error, Is.EqualTo("El email ya está registrado"));

        // Verificar que Guardar NO fue llamado
        _mockRepositorio.Verify(r => r.Guardar(It.IsAny<Usuario>()), Times.Never);
    }

    [Test]
    public void RegistrarUsuario_DebeGuardarUsuarioConDatosCorrectos()
    {
        // Arrange
        string email = "nuevo@example.com";
        string nombre = "Ana García";
        int edad = 30;

        _mockRepositorio. Setup(r => r.ExisteUsuario(email)).Returns(false);

        Usuario?  usuarioGuardado = null;

        // Capturar el usuario que se guarda
        _mockRepositorio
            .Setup(r => r. Guardar(It.IsAny<Usuario>()))
            .Callback<Usuario>(u => usuarioGuardado = u);

        // Act
        var resultado = _servicio.RegistrarUsuario(email, nombre, edad);

        // Assert
        Assert.That(resultado.IsSuccess, Is.True);
        Assert.That(usuarioGuardado, Is.Not.Null);
        Assert.That(usuarioGuardado! .Email, Is.EqualTo(email));
        Assert.That(usuarioGuardado. Nombre, Is.EqualTo(nombre));
        Assert.That(usuarioGuardado.Edad, Is.EqualTo(edad));
    }
}
```

**Técnicas avanzadas de Moq:**

```csharp
[TestFixture]
public class TecnicasAvanzadasMoq
{
    [Test]
    public void Mock_ConArgumentosEspecificos()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();

        // Setup con argumentos específicos
        mock. Setup(r => r.ObtenerPorEmail("admin@example.com"))
            .Returns(new Usuario("admin@example.com", "Admin", 30));

        mock.Setup(r => r.ObtenerPorEmail("user@example.com"))
            .Returns(new Usuario("user@example. com", "User", 25));

        // Act
        var admin = mock.Object.ObtenerPorEmail("admin@example. com");
        var user = mock.Object.ObtenerPorEmail("user@example.com");
        var noExiste = mock.Object.ObtenerPorEmail("otro@example.com");

        // Assert
        Assert.That(admin! .Nombre, Is.EqualTo("Admin"));
        Assert.That(user!.Nombre, Is.EqualTo("User"));
        Assert.That(noExiste, Is.Null);
    }

    [Test]
    public void Mock_ConMatchers()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();

        // It. IsAny<T>:  Cualquier valor del tipo T
        mock.Setup(r => r.ExisteUsuario(It.IsAny<string>()))
            .Returns(true);

        // It.Is<T>: Valor que cumple una condición
        mock.Setup(r => r.ObtenerPorEmail(It. Is<string>(email => email.EndsWith("@admin.com"))))
            .Returns(new Usuario("admin@admin. com", "Admin", 30));

        // Act
        bool existe = mock.Object.ExisteUsuario("cualquier@email.com");
        var admin = mock.Object.ObtenerPorEmail("super@admin.com");

        // Assert
        Assert.That(existe, Is.True);
        Assert.That(admin, Is.Not.Null);
    }

    [Test]
    public void Mock_ConExcepciones()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();

        // Simular que se lanza una excepción
        mock.Setup(r => r. Guardar(It.IsAny<Usuario>()))
            .Throws<InvalidOperationException>();

        // Act & Assert
        Assert. Throws<InvalidOperationException>(() => 
            mock.Object.Guardar(new Usuario("test@example.com", "Test", 25)));
    }

    [Test]
    public void Mock_ConCallbacks()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();
        int contador = 0;

        // Ejecutar una acción cuando se llama al método
        mock.Setup(r => r. Guardar(It.IsAny<Usuario>()))
            .Callback<Usuario>(u =>
            {
                contador++;
                Console.WriteLine($"Guardando usuario:  {u.Email}");
            });

        // Act
        mock.Object.Guardar(new Usuario("user1@example.com", "User 1", 25));
        mock.Object.Guardar(new Usuario("user2@example.com", "User 2", 30));

        // Assert
        Assert.That(contador, Is.EqualTo(2));
    }

    [Test]
    public void Mock_ConReturnsAsync()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuarioAsync>();

        mock.Setup(r => r.ObtenerPorEmailAsync("test@example.com"))
            .ReturnsAsync(new Usuario("test@example.com", "Test", 25));

        // Act
        var resultado = mock.Object.ObtenerPorEmailAsync("test@example.com").Result;

        // Assert
        Assert.That(resultado, Is. Not.Null);
        Assert.That(resultado! .Email, Is.EqualTo("test@example.com"));
    }

    [Test]
    public void Mock_VerificarLlamadasEspecificas()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();
        mock.Setup(r => r.ExisteUsuario(It.IsAny<string>())).Returns(false);

        var servicio = new ServicioRegistro(mock.Object);

        // Act
        servicio.RegistrarUsuario("test@example.com", "Test", 25);

        // Assert - Verificaciones específicas
        mock.Verify(r => r.ExisteUsuario("test@example.com"), Times.Once);
        mock.Verify(r => r.Guardar(It. Is<Usuario>(u => u.Email == "test@example.com")), Times.Once);
        mock.Verify(r => r.ObtenerPorEmail(It.IsAny<string>()), Times.Never);
    }

    [Test]
    public void Mock_VerificarOrdenDeLlamadas()
    {
        // Arrange
        var mock = new Mock<IRepositorioUsuario>();
        var secuencia = new MockSequence();

        // Definir el orden esperado de llamadas
        mock.InSequence(secuencia).Setup(r => r.ExisteUsuario(It.IsAny<string>())).Returns(false);
        mock.InSequence(secuencia).Setup(r => r.Guardar(It.IsAny<Usuario>()));

        var servicio = new ServicioRegistro(mock.Object);

        // Act
        servicio.RegistrarUsuario("test@example.com", "Test", 25);

        // Assert - La secuencia se verifica automáticamente
        mock.Verify();
    }
}

public interface IRepositorioUsuarioAsync
{
    Task<Usuario? > ObtenerPorEmailAsync(string email);
}
```

##### 13.1.3. FluentAssertions para aserciones expresivas

**FluentAssertions** proporciona una API fluida y muy legible para escribir assertions.  Hace que las pruebas sean más expresivas y fáciles de entender.

```csharp
using FluentAssertions;
using NUnit.Framework;

namespace MiAplicacion.Tests;

[TestFixture]
public class EjemplosFluentAssertions
{
    [Test]
    public void FluentAssertions_Igualdad()
    {
        // Arrange
        int valor = 42;
        string texto = "Hola Mundo";

        // Assert con FluentAssertions
        valor.Should().Be(42);
        valor.Should().NotBe(100);
        texto.Should().Be("Hola Mundo");
    }

    [Test]
    public void FluentAssertions_Comparaciones()
    {
        // Arrange
        int valor = 10;

        // Assert
        valor.Should().BeGreaterThan(5);
        valor.Should().BeLessThan(20);
        valor.Should().BeGreaterOrEqualTo(10);
        valor.Should().BeLessOrEqualTo(10);
        valor.Should().BeInRange(5, 15);
        valor.Should().BePositive();
    }

    [Test]
    public void FluentAssertions_Strings()
    {
        // Arrange
        string texto = "Hola Mundo";
        string?  textoNulo = null;

        // Assert
        texto.Should().Be("Hola Mundo");
        texto.Should().Contain("Mundo");
        texto.Should().StartWith("Hola");
        texto.Should().EndWith("Mundo");
        texto.Should().NotBeNullOrEmpty();
        texto.Should().HaveLength(10);
        texto.Should().MatchRegex(@"^Hola.*");

        textoNulo.Should().BeNull();
    }

    [Test]
    public void FluentAssertions_Colecciones()
    {
        // Arrange
        var lista = new List<int> { 1, 2, 3, 4, 5 };
        var listaVacia = new List<int>();

        // Assert
        lista.Should().HaveCount(5);
        lista.Should().Contain(3);
        lista.Should().NotContain(10);
        lista.Should().ContainInOrder(1, 2, 3);
        lista.Should().BeInAscendingOrder();
        lista.Should().OnlyHaveUniqueItems();
        lista.Should().AllSatisfy(x => x. Should().BePositive());

        listaVacia.Should().BeEmpty();
        lista.Should().NotBeEmpty();
    }

    [Test]
    public void FluentAssertions_Objetos()
    {
        // Arrange
        var usuario1 = new Usuario("test@example.com", "Juan", 25);
        var usuario2 = new Usuario("test@example.com", "Juan", 25);
        var usuario3 = new Usuario("otro@example.com", "Pedro", 30);

        // Assert
        usuario1.Should().BeEquivalentTo(usuario2); // Comparación por valor (records)
        usuario1.Should().NotBeSameAs(usuario2); // No es la misma instancia
        usuario1.Should().NotBeEquivalentTo(usuario3);

        usuario1.Should().Match<Usuario>(u => 
            u.Email == "test@example.com" && 
            u.Edad == 25);
    }

    [Test]
    public void FluentAssertions_Excepciones()
    {
        // Arrange
        var calculadora = new Calculadora();

        // Assert
        Action accion = () => calculadora.Dividir(10, 0);

        accion.Should().Throw<DivideByZeroException>()
            .WithMessage("No se puede dividir entre cero");
    }

    [Test]
    public async Task FluentAssertions_Async()
    {
        // Arrange
        Func<Task<int>> operacionAsincrona = async () =>
        {
            await Task. Delay(100);
            return 42;
        };

        // Assert
        await operacionAsincrona.Should().CompleteWithinAsync(TimeSpan.FromSeconds(1));
        
        var resultado = await operacionAsincrona();
        resultado.Should().Be(42);
    }

    [Test]
    public void FluentAssertions_Result()
    {
        // Arrange
        Result<int> exitoso = Result.Success(42);
        Result<int> fallido = Result.Failure<int>("Error de validación");

        // Assert
        exitoso.IsSuccess.Should().BeTrue();
        exitoso.Value.Should().Be(42);

        fallido.IsFailure.Should().BeTrue();
        fallido.Error.Should().Be("Error de validación");
    }

    [Test]
    public void FluentAssertions_ConMensajesPersonalizados()
    {
        // Arrange
        int edad = 15;

        // Assert con mensaje personalizado
        edad.Should().BeGreaterOrEqualTo(18, 
            because: "el usuario debe ser mayor de edad para registrarse");
    }
}
```

**Ejemplo completo integrando NUnit, Moq y FluentAssertions:**

```csharp
using NUnit.Framework;
using Moq;
using FluentAssertions;

namespace MiAplicacion.Tests;

public interface IServicioEmail
{
    Task<bool> EnviarEmailAsync(string destinatario, string asunto, string cuerpo);
}

public class ServicioRegistroCompleto
{
    private readonly IRepositorioUsuario _repositorio;
    private readonly IServicioEmail _servicioEmail;

    public ServicioRegistroCompleto(IRepositorioUsuario repositorio, IServicioEmail servicioEmail)
    {
        _repositorio = repositorio;
        _servicioEmail = servicioEmail;
    }

    public async Task<Result<Usuario>> RegistrarYNotificarAsync(string email, string nombre, int edad)
    {
        // Validaciones
        if (string.IsNullOrWhiteSpace(email))
            return Result.Failure<Usuario>("El email es requerido");

        if (! email.Contains("@"))
            return Result.Failure<Usuario>("Email inválido");

        if (edad < 18)
            return Result.Failure<Usuario>("Debe ser mayor de 18 años");

        // Verificar existencia
        if (_repositorio.ExisteUsuario(email))
            return Result. Failure<Usuario>("El email ya está registrado");

        // Crear y guardar
        var usuario = new Usuario(email, nombre, edad);
        _repositorio.Guardar(usuario);

        // Enviar email de bienvenida
        await _servicioEmail.EnviarEmailAsync(
            email,
            "Bienvenido",
            $"Hola {nombre}, bienvenido a nuestra plataforma"
        );

        return Result. Success(usuario);
    }
}

[TestFixture]
public class ServicioRegistroCompletoTests
{
    private Mock<IRepositorioUsuario> _mockRepositorio = null!;
    private Mock<IServicioEmail> _mockEmail = null!;
    private ServicioRegistroCompleto _servicio = null!;

    [SetUp]
    public void SetUp()
    {
        _mockRepositorio = new Mock<IRepositorioUsuario>();
        _mockEmail = new Mock<IServicioEmail>();
        _servicio = new ServicioRegistroCompleto(_mockRepositorio. Object, _mockEmail.Object);
    }

    [Test]
    public async Task RegistrarYNotificar_DatosValidos_DebeRegistrarYEnviarEmail()
    {
        // Arrange
        string email = "nuevo@example.com";
        string nombre = "Ana García";
        int edad = 28;

        _mockRepositorio.Setup(r => r.ExisteUsuario(email)).Returns(false);
        _mockEmail.Setup(e => e.EnviarEmailAsync(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()))
            .ReturnsAsync(true);

        // Act
        var resultado = await _servicio. RegistrarYNotificarAsync(email, nombre, edad);

        // Assert con FluentAssertions
        resultado. IsSuccess.Should().BeTrue();
        resultado.Value.Should().NotBeNull();
        resultado.Value. Email.Should().Be(email);
        resultado.Value.Nombre.Should().Be(nombre);
        resultado.Value.Edad. Should().Be(edad);

        // Verificar llamadas con Moq
        _mockRepositorio.Verify(r => r. ExisteUsuario(email), Times.Once);
        _mockRepositorio.Verify(r => r.Guardar(It.Is<Usuario>(u => 
            u.Email == email && 
            u.Nombre == nombre && 
            u. Edad == edad)), Times.Once);

        _mockEmail.Verify(e => e.EnviarEmailAsync(
            email,
            "Bienvenido",
            It.Is<string>(cuerpo => cuerpo. Contains(nombre))), Times.Once);
    }

    [Test]
    public async Task RegistrarYNotificar_EmailYaExiste_NoDebeEnviarEmail()
    {
        // Arrange
        string email = "existente@example.com";
        _mockRepositorio.Setup(r => r.ExisteUsuario(email)).Returns(true);

        // Act
        var resultado = await _servicio.RegistrarYNotificarAsync(email, "Usuario", 25);

        // Assert
        resultado.IsFailure.Should().BeTrue();
        resultado. Error.Should().Be("El email ya está registrado");

        // NO se debe enviar email
        _mockEmail.Verify(e => e. EnviarEmailAsync(
            It.IsAny<string>(), 
            It.IsAny<string>(), 
            It.IsAny<string>()), Times.Never);

        // NO se debe guardar
        _mockRepositorio.Verify(r => r.Guardar(It. IsAny<Usuario>()), Times.Never);
    }

    [TestCase("", "Juan", 25, "El email es requerido")]
    [TestCase("sin-arroba", "Juan", 25, "Email inválido")]
    [TestCase("valido@example.com", "Juan", 15, "Debe ser mayor de 18 años")]
    public async Task RegistrarYNotificar_DatosInvalidos_DebeRetornarErrorEsperado(
        string email, string nombre, int edad, string errorEsperado)
    {
        // Act
        var resultado = await _servicio.RegistrarYNotificarAsync(email, nombre, edad);

        // Assert
        resultado.IsFailure.Should().BeTrue();
        resultado.Error. Should().Be(errorEsperado);

        // NO debe interactuar con dependencias
        _mockRepositorio. Verify(r => r. Guardar(It.IsAny<Usuario>()), Times.Never);
        _mockEmail.Verify(e => e.EnviarEmailAsync(
            It.IsAny<string>(), 
            It.IsAny<string>(), 
            It.IsAny<string>()), Times.Never);
    }
}
```

### 13.2. Gestión de proyectos y documentación

##### 13.2.1. dotnet CLI y archivos . csproj

El **CLI de . NET** (`dotnet`) es la herramienta de línea de comandos para crear, compilar, ejecutar y publicar aplicaciones .NET.

**Comandos principales:**

```bash
# Crear nuevo proyecto
dotnet new console -n MiAplicacion
dotnet new webapi -n MiApi
dotnet new classlib -n MiLibreria
dotnet new nunit -n MiAplicacion.Tests

# Agregar paquetes NuGet
dotnet add package Newtonsoft.Json
dotnet add package Microsoft.EntityFrameworkCore

# Compilar
dotnet build

# Ejecutar
dotnet run

# Ejecutar tests
dotnet test

# Publicar para producción
dotnet publish -c Release -o ./publish

# Agregar referencia a otro proyecto
dotnet add reference ../MiLibreria/MiLibreria.csproj

# Listar paquetes instalados
dotnet list package

# Restaurar dependencias
dotnet restore
```

**Estructura del archivo .csproj:**

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <!-- Versión de .NET -->
    <TargetFramework>net10.0</TargetFramework>
    
    <!-- Habilitar nullable reference types -->
    <Nullable>enable</Nullable>
    
    <!-- Tratar warnings como errores -->
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    
    <!-- Versión de C# -->
    <LangVersion>14</LangVersion>
    
    <!-- Información del paquete -->
    <Version>1.0.0</Version>
    <Authors>Tu Nombre</Authors>
    <Company>Tu Empresa</Company>
    <Description>Descripción de tu aplicación</Description>
  </PropertyGroup>

  <ItemGroup>
    <!-- Referencias a paquetes NuGet -->
    <PackageReference Include="Microsoft. EntityFrameworkCore" Version="10.0.0" />
    <PackageReference Include="Refit" Version="7.0.0" />
    <PackageReference Include="FluentValidation" Version="11.0.0" />
  </ItemGroup>

  <ItemGroup>
    <!-- Referencias a otros proyectos -->
    <ProjectReference Include="..\MiLibreria\MiLibreria.csproj" />
  </ItemGroup>

</Project>
```

##### 13.2.2. Documentación XML para el código

La documentación XML en C# permite generar documentación automática del código mediante comentarios especiales.

```csharp
namespace MiAplicacion. Services;

/// <summary>
/// Servicio para gestionar operaciones con productos. 
/// </summary>
/// <remarks>
/// Este servicio proporciona funcionalidades CRUD para productos
/// y maneja la validación de negocio.
/// </remarks>
public class ProductoService
{
    /// <summary>
    /// Crea un nuevo producto en el sistema.
    /// </summary>
    /// <param name="nombre">El nombre del producto.  No puede estar vacío.</param>
    /// <param name="precio">El precio del producto. Debe ser mayor que cero.</param>
    /// <param name="categoria">La categoría del producto. </param>
    /// <returns>
    /// Un <see cref="Result{T}"/> que contiene el producto creado si tuvo éxito,
    /// o un mensaje de error si falló.
    /// </returns>
    /// <exception cref="ArgumentNullException">
    /// Se lanza cuando <paramref name="nombre"/> o <paramref name="categoria"/> son nulos.
    /// </exception>
    /// <example>
    /// <code>
    /// var servicio = new ProductoService();
    /// var resultado = await servicio.CrearProductoAsync("Laptop", 1200m, "Electrónica");
    /// 
    /// if (resultado.IsSuccess)
    /// {
    ///     Console.WriteLine($"Producto creado:  {resultado.Value.Nombre}");
    /// }
    /// </code>
    /// </example>
    public async Task<Result<Producto>> CrearProductoAsync(
        string nombre, 
        decimal precio, 
        string categoria)
    {
        // Implementación
        throw new NotImplementedException();
    }

    /// <summary>
    /// Obtiene un producto por su identificador.
    /// </summary>
    /// <param name="id">El ID del producto a buscar.</param>
    /// <returns>
    /// El producto si se encuentra, o <c>null</c> si no existe.
    /// </returns>
    /// <seealso cref="ObtenerTodosAsync"/>
    public async Task<Producto?> ObtenerPorIdAsync(int id)
    {
        throw new NotImplementedException();
    }

    /// <summary>
    /// Obtiene todos los productos del sistema.
    /// </summary>
    /// <returns>Una lista de todos los productos disponibles.</returns>
    public async Task<List<Producto>> ObtenerTodosAsync()
    {
        throw new NotImplementedException();
    }
}
```

**Habilitar generación de documentación XML en . csproj:**

```xml
<PropertyGroup>
  <GenerateDocumentationFile>true</GenerateDocumentationFile>
  <NoWarn>$(NoWarn);1591</NoWarn> <!-- Suprimir warnings de XML docs faltantes -->
</PropertyGroup>
```

---

### 14. Dockerización de aplicaciones .NET

El **despliegue de aplicaciones** es el proceso de poner una aplicación en funcionamiento para que los usuarios puedan interactuar con ella. En la actualidad, este proceso se ha simplificado y estandarizado gracias a tecnologías de contenedores como **Docker**. 

#### 14.1. ¿Qué es Docker? 

**Docker** es una plataforma de código abierto que permite automatizar el despliegue de aplicaciones dentro de contenedores.  Un **contenedor** es una unidad de software ligera y portátil que empaqueta todo lo necesario para ejecutar una aplicación:  código, dependencias, librerías y configuraciones.

La clave para entender Docker es la distinción entre una **imagen** y un **contenedor**. 

* Una **imagen** es una plantilla de solo lectura que contiene las instrucciones para crear un contenedor, incluyendo el sistema operativo base, las librerías y las dependencias de la aplicación.
* Un **contenedor** es una instancia en ejecución de una imagen, un entorno aislado donde la aplicación se ejecuta.

**Comandos Docker básicos:**

```bash
# Construir una imagen
docker build -t mi-app: 1.0 .

# Listar imágenes
docker images

# Ejecutar un contenedor
docker run -p 8080:8080 mi-app:1.0

# Listar contenedores en ejecución
docker ps

# Listar todos los contenedores (incluyendo detenidos)
docker ps -a

# Detener un contenedor
docker stop <container-id>

# Eliminar un contenedor
docker rm <container-id>

# Eliminar una imagen
docker rmi mi-app:1.0

# Ver logs de un contenedor
docker logs <container-id>

# Acceder a un contenedor en ejecución
docker exec -it <container-id> /bin/bash
```

#### 14.2. Docker Compose

**Docker Compose** es una herramienta que simplifica la administración de aplicaciones que consisten en múltiples contenedores.  En lugar de ejecutar cada contenedor individualmente, puedes definir la configuración de todos los servicios, redes y volúmenes en un solo archivo YAML y administrarlos con un solo comando.

Es especialmente útil para aplicaciones que dependen de varios servicios, como una aplicación . NET que necesita una base de datos PostgreSQL, MongoDB y Redis.

**Ejemplo de `docker-compose.yml` completo:**

```yaml
version: '3.8'

services:
  # Aplicación .NET
  api:
    build: 
      context: . 
      dockerfile: Dockerfile
    container_name: mi-api
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=miapp_db;Username=postgres;Password=mysecretpassword
      - ConnectionStrings__MongoDB=mongodb://mongo:27017
      - ConnectionStrings__Redis=redis:6379
    depends_on:
      - postgres
      - mongo
      - redis
    networks:
      - app-network

  # PostgreSQL
  postgres:
    image: postgres:16-alpine
    container_name: mi-postgres
    environment:
      POSTGRES_DB: miapp_db
      POSTGRES_USER:  postgres
      POSTGRES_PASSWORD:  mysecretpassword
    ports:
      - "5432:5432"
    volumes: 
      - postgres-data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval:  10s
      timeout: 5s
      retries: 5

  # MongoDB
  mongo:
    image: mongo:7
    container_name: mi-mongo
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: adminpass
    ports:
      - "27017:27017"
    volumes:
      - mongo-data:/data/db
    networks:
      - app-network
    healthcheck:
      test:  echo 'db.runCommand("ping").ok' | mongosh localhost:27017/test --quiet
      interval: 10s
      timeout: 5s
      retries: 5

  # Redis
  redis: 
    image: redis:7-alpine
    container_name: mi-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - app-network
    healthcheck: 
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5

networks:
  app-network: 
    driver: bridge

volumes: 
  postgres-data:
  mongo-data:
  redis-data:
```

**Comandos de Docker Compose:**

```bash
# Iniciar todos los servicios
docker-compose up

# Iniciar en segundo plano (detached)
docker-compose up -d

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes
docker-compose down -v

# Ver logs
docker-compose logs

# Ver logs de un servicio específico
docker-compose logs api

# Reconstruir imágenes
docker-compose build

# Escalar un servicio
docker-compose up -d --scale api=3
```

#### 14.3. Despliegue de aplicaciones .  NET

Para desplegar una aplicación .NET en un contenedor, necesitas un **Dockerfile** que especifique cómo se construirá la imagen y cómo se ejecutará tu aplicación.

##### 14.3.1. Despliegue simple

El enfoque más directo es crear una imagen a partir de una base del SDK de .NET y copiar tu aplicación pre-compilada.

```dockerfile
# Usa la imagen base del runtime de .NET 10
FROM mcr.microsoft.com/dotnet/aspnet:10.0

# Establece el directorio de trabajo
WORKDIR /app

# Copia los archivos publicados de tu aplicación
COPY ./publish . 

# Expone el puerto que la aplicación usa
EXPOSE 8080

# Define el comando para ejecutar la aplicación
ENTRYPOINT ["dotnet", "MiAplicacion.dll"]
```

**Compilar y publicar la aplicación localmente primero:**

```bash
# Publicar la aplicación
dotnet publish -c Release -o ./publish

# Construir la imagen Docker
docker build -t mi-aplicacion:1.0 . 

# Ejecutar el contenedor
docker run -p 8080:8080 mi-aplicacion:1.0
```

##### 14.3.2. Despliegue con un Dockerfile multi-etapa

Un enfoque más eficiente y profesional es usar un **Dockerfile multi-etapa**.  Esto te permite compilar y construir la aplicación dentro de un primer contenedor (la etapa de compilación) y luego copiar solo los archivos necesarios a un segundo contenedor más ligero (la etapa de ejecución). Esto reduce significativamente el tamaño de la imagen final.

**Dockerfile multi-etapa para aplicación ASP.NET Core:**

```dockerfile
# ============================================
# Etapa 1: BUILD - Compilación de la aplicación
# ============================================
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build

# Establecer directorio de trabajo
WORKDIR /src

# Copiar archivos de proyecto y restaurar dependencias
# (Se hace primero para aprovechar la cache de Docker)
COPY ["MiAplicacion/MiAplicacion.csproj", "MiAplicacion/"]
COPY ["MiAplicacion. Core/MiAplicacion.Core.csproj", "MiAplicacion.Core/"]
COPY ["MiAplicacion.Infrastructure/MiAplicacion.Infrastructure.csproj", "MiAplicacion.Infrastructure/"]

RUN dotnet restore "MiAplicacion/MiAplicacion.csproj"

# Copiar el resto del código fuente
COPY .  .

# Compilar la aplicación
WORKDIR "/src/MiAplicacion"
RUN dotnet build "MiAplicacion.csproj" -c Release -o /app/build

# ============================================
# Etapa 2: PUBLISH - Publicación de la aplicación
# ============================================
FROM build AS publish
RUN dotnet publish "MiAplicacion.csproj" -c Release -o /app/publish /p:UseAppHost=false

# ============================================
# Etapa 3: RUNTIME - Imagen final de ejecución
# ============================================
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final

# Crear un usuario no-root para mayor seguridad
RUN addgroup --system --gid 1000 appgroup \
    && adduser --system --uid 1000 --ingroup appgroup --shell /bin/sh appuser

WORKDIR /app

# Copiar los archivos publicados desde la etapa de publish
COPY --from=publish /app/publish .

# Cambiar al usuario no-root
USER appuser

# Exponer el puerto
EXPOSE 8080

# Configurar variables de entorno
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1

# Comando de inicio
ENTRYPOINT ["dotnet", "MiAplicacion.dll"]
```

**Dockerfile multi-etapa con ejecución de tests:**

```dockerfile
# ============================================
# Etapa 1: BUILD
# ============================================
FROM mcr.microsoft.com/dotnet/sdk:10.0 AS build
WORKDIR /src

# Copiar y restaurar
COPY ["MiAplicacion/MiAplicacion.csproj", "MiAplicacion/"]
COPY ["MiAplicacion.Tests/MiAplicacion.Tests.csproj", "MiAplicacion.Tests/"]
RUN dotnet restore "MiAplicacion/MiAplicacion.csproj"
RUN dotnet restore "MiAplicacion.Tests/MiAplicacion.Tests.csproj"

# Copiar código
COPY . .

# ============================================
# Etapa 2: TEST - Ejecutar pruebas
# ============================================
FROM build AS test
WORKDIR /src/MiAplicacion.Tests
RUN dotnet test --no-restore --verbosity normal

# ============================================
# Etapa 3: PUBLISH
# ============================================
FROM build AS publish
WORKDIR /src/MiAplicacion
RUN dotnet publish "MiAplicacion.csproj" -c Release -o /app/publish

# ============================================
# Etapa 4: RUNTIME
# ============================================
FROM mcr.microsoft.com/dotnet/aspnet:10.0 AS final
WORKDIR /app
COPY --from=publish /app/publish . 

EXPOSE 8080
ENTRYPOINT ["dotnet", "MiAplicacion.dll"]
```

**Archivo `.dockerignore` (importante para optimizar el build):**

```dockerignore
# Directorios de compilación
**/bin/
**/obj/
**/out/

# Dependencias
**/node_modules/
**/packages/

# Archivos de usuario
**/.vs/
**/.vscode/
**/.idea/

# Archivos temporales
**/*. swp
**/*.bak
**/*~

# Datos locales
**/data/
**/logs/

# Git
.git/
.gitignore
.gitattributes

# Docker
**/Dockerfile*
**/docker-compose*
**/. dockerignore

# Documentación
**/*.md
LICENSE

# Tests y coverage
**/TestResults/
**/coverage/
```

**Construir y ejecutar:**

```bash
# Construir la imagen con todas las etapas (incluyendo tests)
docker build -t mi-aplicacion:1.0 .

# Ejecutar el contenedor
docker run -d \
  -p 8080:8080 \
  --name mi-app \
  -e ASPNETCORE_ENVIRONMENT=Production \
  -e ConnectionStrings__DefaultConnection="Host=postgres;Database=mydb" \
  mi-aplicacion:1.0

# Ver logs
docker logs -f mi-app
```

#### 14.4. Testcontainers y Docker

**Testcontainers** es una librería que permite ejecutar contenedores Docker durante las pruebas de integración. Esto es especialmente útil para probar código que interactúa con bases de datos, colas de mensajes, o cualquier servicio externo.

**Instalación:**

```bash
dotnet add package Testcontainers
dotnet add package Testcontainers.PostgreSql
dotnet add package Testcontainers.MongoDb
```

**Ejemplo:  Tests de integración con PostgreSQL:**

```csharp
using NUnit.Framework;
using Testcontainers.PostgreSql;
using Microsoft.EntityFrameworkCore;
using FluentAssertions;

namespace MiAplicacion.Tests. Integration;

[TestFixture]
public class ProductoRepositoryIntegrationTests
{
    private PostgreSqlContainer _postgresContainer = null!;
    private ApplicationDbContext _context = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        // Configurar y arrancar el contenedor de PostgreSQL
        _postgresContainer = new PostgreSqlBuilder()
            .WithImage("postgres:16-alpine")
            .WithDatabase("testdb")
            .WithUsername("testuser")
            .WithPassword("testpass")
            .WithCleanUp(true) // Limpiar el contenedor al terminar
            . Build();

        await _postgresContainer.StartAsync();

        Console.WriteLine($"PostgreSQL iniciado en: {_postgresContainer.GetConnectionString()}");
    }

    [SetUp]
    public async Task SetUp()
    {
        // Crear el DbContext con la conexión al contenedor
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgresContainer.GetConnectionString())
            .Options;

        _context = new ApplicationDbContext(options);

        // Aplicar migraciones
        await _context. Database.EnsureCreatedAsync();
    }

    [TearDown]
    public async Task TearDown()
    {
        // Limpiar la base de datos después de cada test
        await _context.Database. EnsureDeletedAsync();
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
    public async Task Guardar_NuevoProducto_DebeGuardarEnBaseDeDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Laptop Test",
            Precio = 1200.00m,
            Categoria = "Electrónica"
        };

        // Act
        _context.Productos.Add(producto);
        await _context.SaveChangesAsync();

        // Assert
        var productoGuardado = await _context. Productos
            .FirstOrDefaultAsync(p => p.Nombre == "Laptop Test");

        productoGuardado.Should().NotBeNull();
        productoGuardado! .Id.Should().BeGreaterThan(0);
        productoGuardado. Nombre.Should().Be("Laptop Test");
        productoGuardado.Precio.Should().Be(1200.00m);
    }

    [Test]
    public async Task ObtenerPorCategoria_DebeRetornarProductosFiltrados()
    {
        // Arrange
        var productos = new[]
        {
            new Producto { Nombre = "Laptop", Precio = 1200m, Categoria = "Electrónica" },
            new Producto { Nombre = "Mouse", Precio = 25m, Categoria = "Electrónica" },
            new Producto { Nombre = "Libro", Precio = 15m, Categoria = "Hogar" }
        };

        _context.Productos.AddRange(productos);
        await _context. SaveChangesAsync();

        // Act
        var electronicos = await _context.Productos
            .Where(p => p.Categoria == "Electrónica")
            .ToListAsync();

        // Assert
        electronicos.Should().HaveCount(2);
        electronicos. Should().OnlyContain(p => p.Categoria == "Electrónica");
    }

    [Test]
    public async Task Actualizar_ProductoExistente_DebeActualizarDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Laptop Original",
            Precio = 1200m,
            Categoria = "Electrónica"
        };

        _context.Productos.Add(producto);
        await _context.SaveChangesAsync();

        // Act
        producto.Nombre = "Laptop Actualizada";
        producto.Precio = 1100m;
        await _context.SaveChangesAsync();

        // Assert
        var productoActualizado = await _context. Productos. FindAsync(producto.Id);
        productoActualizado! . Nombre.Should().Be("Laptop Actualizada");
        productoActualizado.Precio.Should().Be(1100m);
    }

    [Test]
    public async Task Eliminar_ProductoExistente_DebeEliminarDeLaBaseDeDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Producto a Eliminar",
            Precio = 50m,
            Categoria = "Test"
        };

        _context.Productos.Add(producto);
        await _context.SaveChangesAsync();
        int idProducto = producto.Id;

        // Act
        _context.Productos.Remove(producto);
        await _context.SaveChangesAsync();

        // Assert
        var productoEliminado = await _context.Productos.FindAsync(idProducto);
        productoEliminado.Should().BeNull();
    }
}
```

**Ejemplo: Tests de integración con MongoDB:**

```csharp
using NUnit.Framework;
using Testcontainers.MongoDb;
using MongoDB.Driver;
using FluentAssertions;

namespace MiAplicacion.Tests.Integration;

[TestFixture]
public class ProductoMongoRepositoryIntegrationTests
{
    private MongoDbContainer _mongoContainer = null!;
    private IMongoCollection<Producto> _collection = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        // Configurar y arrancar el contenedor de MongoDB
        _mongoContainer = new MongoDbBuilder()
            .WithImage("mongo:7")
            .WithCleanUp(true)
            .Build();

        await _mongoContainer. StartAsync();

        Console.WriteLine($"MongoDB iniciado en: {_mongoContainer.GetConnectionString()}");
    }

    [SetUp]
    public void SetUp()
    {
        // Crear la conexión a MongoDB
        var client = new MongoClient(_mongoContainer.GetConnectionString());
        var database = client.GetDatabase("testdb");
        _collection = database.GetCollection<Producto>("productos");
    }

    [TearDown]
    public async Task TearDown()
    {
        // Limpiar la colección después de cada test
        await _collection.DeleteManyAsync(FilterDefinition<Producto>.Empty);
    }

    [OneTimeTearDown]
    public async Task OneTimeTearDown()
    {
        await _mongoContainer.StopAsync();
        await _mongoContainer. DisposeAsync();
    }

    [Test]
    public async Task InsertOne_NuevoProducto_DebeGuardarEnMongoDB()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Teclado Mecánico",
            Precio = 89.99m,
            Categoria = "Accesorios"
        };

        // Act
        await _collection.InsertOneAsync(producto);

        // Assert
        var productoGuardado = await _collection
            .Find(p => p. Nombre == "Teclado Mecánico")
            .FirstOrDefaultAsync();

        productoGuardado.Should().NotBeNull();
        productoGuardado!.Id.Should().NotBeNullOrEmpty();
        productoGuardado.Precio.Should().Be(89.99m);
    }

    [Test]
    public async Task Find_ConFiltro_DebeRetornarProductosFiltrados()
    {
        // Arrange
        var productos = new[]
        {
            new Producto { Nombre = "Producto A", Precio = 100m, Categoria = "Cat1" },
            new Producto { Nombre = "Producto B", Precio = 200m, Categoria = "Cat1" },
            new Producto { Nombre = "Producto C", Precio = 150m, Categoria = "Cat2" }
        };

        await _collection.InsertManyAsync(productos);

        // Act
        var filter = Builders<Producto>.Filter.Eq(p => p. Categoria, "Cat1");
        var resultados = await _collection.Find(filter).ToListAsync();

        // Assert
        resultados.Should().HaveCount(2);
        resultados.Should().OnlyContain(p => p. Categoria == "Cat1");
    }

    [Test]
    public async Task UpdateOne_ProductoExistente_DebeActualizarDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Producto Original",
            Precio = 100m,
            Categoria = "Test"
        };

        await _collection.InsertOneAsync(producto);

        // Act
        var filter = Builders<Producto>. Filter.Eq(p => p.Id, producto.Id);
        var update = Builders<Producto>.Update.Set(p => p. Precio, 80m);
        await _collection.UpdateOneAsync(filter, update);

        // Assert
        var productoActualizado = await _collection
            .Find(p => p. Id == producto.Id)
            .FirstOrDefaultAsync();

        productoActualizado! .Precio.Should().Be(80m);
    }

    [Test]
    public async Task DeleteOne_ProductoExistente_DebeEliminarDeMongoDB()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Producto a Eliminar",
            Precio = 50m,
            Categoria = "Test"
        };

        await _collection.InsertOneAsync(producto);
        string idProducto = producto.Id! ;

        // Act
        await _collection.DeleteOneAsync(p => p.Id == idProducto);

        // Assert
        var productoEliminado = await _collection
            .Find(p => p. Id == idProducto)
            .FirstOrDefaultAsync();

        productoEliminado.Should().BeNull();
    }

    [Test]
    public async Task Aggregate_DebeCalcularEstadisticas()
    {
        // Arrange
        var productos = new[]
        {
            new Producto { Nombre = "P1", Precio = 100m, Categoria = "A" },
            new Producto { Nombre = "P2", Precio = 200m, Categoria = "A" },
            new Producto { Nombre = "P3", Precio = 150m, Categoria = "B" }
        };

        await _collection.InsertManyAsync(productos);

        // Act
        var pipeline = _collection.Aggregate()
            .Group(p => p.Categoria, g => new
            {
                Categoria = g.Key,
                Cantidad = g.Count(),
                PrecioTotal = g.Sum(p => p.Precio)
            });

        var resultados = await pipeline.ToListAsync();

        // Assert
        resultados. Should().HaveCount(2);
        
        var categoriaA = resultados.First(r => r.Categoria == "A");
        categoriaA. Cantidad.Should().Be(2);
        categoriaA.PrecioTotal.Should().Be(300m);
    }
}
```

**Ejemplo: Tests con múltiples contenedores:**

```csharp
using NUnit.Framework;
using Testcontainers.PostgreSql;
using Testcontainers.MongoDb;
using Testcontainers.Redis;
using FluentAssertions;

namespace MiAplicacion.Tests.Integration;

[TestFixture]
public class SistemaCompletoIntegrationTests
{
    private PostgreSqlContainer _postgresContainer = null!;
    private MongoDbContainer _mongoContainer = null!;
    private RedisContainer _redisContainer = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        // Iniciar todos los contenedores en paralelo
        _postgresContainer = new PostgreSqlBuilder()
            .WithImage("postgres:16-alpine")
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
        Console.WriteLine($"  Redis: {_redisContainer.GetConnectionString()}");
    }

    [Test]
    public async Task SistemaCompleto_DebeIntegrarTodosLosServicios()
    {
        // Arrange - Configurar servicios
        var postgresOptions = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgresContainer.GetConnectionString())
            .Options;

        await using var dbContext = new ApplicationDbContext(postgresOptions);
        await dbContext.Database.EnsureCreatedAsync();

        var mongoClient = new MongoClient(_mongoContainer.GetConnectionString());
        var mongoDb = mongoClient.GetDatabase("testdb");
        var mongoCollection = mongoDb.GetCollection<Producto>("productos");

        // Simular uso de Redis (requiere StackExchange.Redis)
        // var redis = ConnectionMultiplexer.Connect(_redisContainer.GetConnectionString());

        // Act - Operaciones en PostgreSQL
        var productoSql = new Producto
        {
            Nombre = "Producto SQL",
            Precio = 100m,
            Categoria = "Test"
        };

        dbContext.Productos.Add(productoSql);
        await dbContext.SaveChangesAsync();

        // Act - Operaciones en MongoDB
        var productoMongo = new Producto
        {
            Nombre = "Producto Mongo",
            Precio = 200m,
            Categoria = "Test"
        };

        await mongoCollection.InsertOneAsync(productoMongo);

        // Assert
        var productosSql = await dbContext.Productos. ToListAsync();
        var productosMongo = await mongoCollection. Find(_ => true).ToListAsync();

        productosSql.Should().HaveCount(1);
        productosMongo.Should().HaveCount(1);
    }

    [OneTimeTearDown]
    public async Task OneTimeTearDown()
    {
        // Detener todos los contenedores en paralelo
        await Task.WhenAll(
            _postgresContainer.StopAsync(),
            _mongoContainer.StopAsync(),
            _redisContainer.StopAsync()
        );

        await Task.WhenAll(
            _postgresContainer.DisposeAsync().AsTask(),
            _mongoContainer.DisposeAsync().AsTask(),
            _redisContainer. DisposeAsync().AsTask()
        );

        Console.WriteLine("✅ Todos los contenedores detenidos y eliminados");
    }
}
```

**Ventajas de Testcontainers:**

✅ **Tests realistas**: Pruebas contra servicios reales, no mocks. 

✅ **Aislamiento**: Cada test tiene su propia instancia limpia. 

✅ **CI/CD friendly**: Los tests funcionan en cualquier entorno con Docker.

✅ **Reproducibilidad**: Los tests son consistentes en todos los entornos.

✅ **Cleanup automático**: Los contenedores se eliminan automáticamente. 

---

