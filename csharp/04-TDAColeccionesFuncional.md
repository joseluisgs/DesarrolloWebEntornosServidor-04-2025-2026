# Tipos de Datos Abstractos, Colecciones y Programación Funcional en . NET

---

- [Tipos de Datos Abstractos, Colecciones y Programación Funcional en . NET](#tipos-de-datos-abstractos-colecciones-y-programación-funcional-en--net)
  - [Tipos de datos abstractos](#tipos-de-datos-abstractos)
  - [Programación con genéricos](#programación-con-genéricos)
    - [Varianza en .NET](#varianza-en-net)
  - [Colecciones en .  NET](#colecciones-en---net)
  - [Programación funcional en . NET](#programación-funcional-en--net)
  - [Tipos anulables y operadores null](#tipos-anulables-y-operadores-null)
  - [Operaciones funcionales con colecciones:   LINQ](#operaciones-funcionales-con-colecciones---linq)
    - [Ejemplos prácticos](#ejemplos-prácticos)
    - [Similitudes con SQL](#similitudes-con-sql)

---

## Tipos de datos abstractos

Un Tipo de Dato Abstracto (TDA) es un concepto en programación que se refiere a una estructura de datos que define un conjunto de operaciones y propiedades, pero oculta los detalles internos de implementación.   En .  NET, los TDAs se implementan mediante **interfaces** y **clases abstractas**. 

Un TDA se compone de dos partes principales:

1. **Interfaz**:  Define las operaciones que se pueden realizar con los datos y las propiedades que estos deben cumplir.  

2. **Implementación**: Es la forma en que se estructuran y almacenan los datos internamente para que las operaciones definidas en la interfaz puedan llevarse a cabo.

**Ejemplo de TDA en C#:**

```csharp
// Interfaz que define el TDA de una Pila
public interface IPila<T>
{
    void Push(T elemento);
    T Pop();
    T Peek();
    bool IsEmpty { get; }
    int Count { get; }
}

// Implementación del TDA usando una lista interna
public class Pila<T> : IPila<T>
{
    private readonly List<T> _elementos = new();

    public void Push(T elemento) => _elementos.Add(elemento);

    public T Pop()
    {
        if (IsEmpty)
            throw new InvalidOperationException("La pila está vacía");
            
        var elemento = _elementos[^1];
        _elementos.RemoveAt(_elementos.Count - 1);
        return elemento;
    }

    public T Peek()
    {
        if (IsEmpty)
            throw new InvalidOperationException("La pila está vacía");
            
        return _elementos[^1];
    }

    public bool IsEmpty => _elementos.Count == 0;
    public int Count => _elementos. Count;
}

// Uso
var pila = new Pila<int>();
pila.Push(10);
pila.Push(20);
Console.WriteLine(pila.Pop()); // 20
```

---

## Programación con genéricos

La programación con genéricos en . NET permite crear clases, interfaces y métodos que pueden trabajar con diferentes tipos de datos de manera segura y flexible.

**Sintaxis básica:**

```csharp
// Clase genérica
public class Contenedor<T>
{
    private T _valor;

    public Contenedor(T valor)
    {
        _valor = valor;
    }

    public T ObtenerValor() => _valor;
}

// Método genérico
public T ObtenerPrimero<T>(List<T> lista)
{
    if (lista. Count == 0)
        throw new InvalidOperationException("La lista está vacía");
        
    return lista[0];
}

// Uso
var contenedorEntero = new Contenedor<int>(42);
var contenedorTexto = new Contenedor<string>("Hola");

var numeros = new List<int> { 1, 2, 3 };
int primero = ObtenerPrimero(numeros);
```

**Restricciones de tipos (Generic Constraints):**

```csharp
// T debe ser un tipo por referencia
public class Repositorio<T> where T : class
{
    private List<T> _items = new();
}

// T debe tener un constructor sin parámetros
public class Fabrica<T> where T : new()
{
    public T Crear() => new T();
}

// T debe implementar IComparable
public class Ordenador<T> where T : IComparable<T>
{
    public List<T> Ordenar(List<T> lista)
    {
        return lista.OrderBy(x => x).ToList();
    }
}

// Múltiples restricciones
public class Gestor<T> where T : class, IDisposable, new()
{
    public T CrearYUsar()
    {
        using var recurso = new T();
        // Usar recurso
        return recurso;
    }
}
```

### Varianza en .NET

La varianza permite que los tipos genéricos sean covariantes o contravariantes. 

**Covarianza (`out`)**: Permite usar un tipo más derivado. 

```csharp
// Interfaz covariante
public interface IProductor<out T>
{
    T Obtener();
}

public class ProductorAnimales : IProductor<Animal>
{
    public Animal Obtener() => new Animal();
}

public class ProductorPerros : IProductor<Perro>
{
    public Perro Obtener() => new Perro();
}

// Uso - covarianza permite asignar un productor más específico
IProductor<Animal> productor = new ProductorPerros(); // ✅ Válido
```

**Contravarianza (`in`)**: Permite usar un tipo menos derivado.  

```csharp
// Interfaz contravariante
public interface IConsumidor<in T>
{
    void Consumir(T elemento);
}

public class ConsumidorAnimales :   IConsumidor<Animal>
{
    public void Consumir(Animal animal) { /* ...  */ }
}

// Uso - contravarianza permite asignar un consumidor más general
IConsumidor<Perro> consumidor = new ConsumidorAnimales(); // ✅ Válido
```

---

## Colecciones en .  NET

Las colecciones en . NET son TDAs que permiten almacenar y manipular grupos de elementos.  

![Colecciones](../images/colecciones.jpg)

**Principales colecciones:**

```csharp
// List<T> - Lista dinámica
var lista = new List<int> { 1, 2, 3 };
lista.Add(4);
lista.  Remove(2);

// Dictionary<TKey, TValue> - Pares clave-valor
var diccionario = new Dictionary<string, int>
{
    ["uno"] = 1,
    ["dos"] = 2
};

// HashSet<T> - Conjunto sin duplicados
var conjunto = new HashSet<int> { 1, 2, 3 };
conjunto.Add(2); // No se añade porque ya existe

// Queue<T> - Cola FIFO
var cola = new Queue<int>();
cola.Enqueue(1);
cola.Enqueue(2);
int primero = cola.Dequeue(); // 1

// Stack<T> - Pila LIFO
var pila = new Stack<int>();
pila.Push(1);
pila.Push(2);
int ultimo = pila.Pop(); // 2

// ImmutableList<T> - Lista inmutable
var listaInmutable = ImmutableList. Create(1, 2, 3);
var nuevaLista = listaInmutable.  Add(4); // Crea una nueva lista
```

---

## Programación funcional en . NET

La programación funcional en . NET se basa en funciones como ciudadanos de primera clase, expresiones lambda y operaciones de alto nivel con LINQ.

**1.   Funciones como argumentos:**

```csharp
// Func<T, TResult> - función que devuelve un valor
Func<int, int> sumarDos = x => x + 2;
int resultado = AplicarFuncion(sumarDos, 5); // 7

int AplicarFuncion(Func<int, int> funcion, int valor)
{
    return funcion(valor);
}
```

**2. Composición de funciones:**

```csharp
Func<int, int> duplicar = x => x * 2;
Func<int, int> sumarUno = x => x + 1;

// Composición manual
Func<int, int> duplicarYSumarUno = x => sumarUno(duplicar(x));

int resultado = duplicarYSumarUno(5); // 11
```

**3. Recursión:**

```csharp
int Factorial(int n)
{
    return n <= 1 ? 1 : n * Factorial(n - 1);
}

int resultado = Factorial(5); // 120
```

**4. Delegados y expresiones lambda:**

```csharp
// Action<T> - función que no devuelve valor
Action<string> imprimir = mensaje => Console.WriteLine(mensaje);
imprimir("Hola, mundo!");

// Predicate<T> - función que devuelve bool
Predicate<int> esPar = n => n % 2 == 0;
bool resultado = esPar(4); // true

// Func con múltiples parámetros
Func<int, int, int> sumar = (a, b) => a + b;
int suma = sumar(3, 5); // 8
```

---

## Tipos anulables y operadores null

En lugar de `Optional` de Java, C# tiene tipos anulables y operadores null nativos:  

```csharp
// Tipo anulable
int? edad = null;

// Operador de coalescencia nula (??)
string nombre = ObtenerNombre() ?? "Invitado";

// Operador de propagación nula (?.)
int? longitud = texto? . Length;

// Operador de asignación de coalescencia nula (??=)
string?   apellido = null;
apellido ??= "Desconocido";

// Pattern matching con null
string mensaje = valor switch
{
    null => "Valor nulo",
    int n when n > 0 => "Positivo",
    int n when n < 0 => "Negativo",
    _ => "Cero"
};
```

---

## Operaciones funcionales con colecciones:   LINQ

**LINQ (Language Integrated Query)** es el equivalente a la API Stream de Java, pero integrado directamente en el lenguaje. 

![LINQ](../images/JavaStream.png)

**Operaciones principales:**

```csharp
var numeros = new List<int> { 1, 2, 3, 4, 5, 6, 7, 8, 9, 10 };

// 1. FILTER (Where)
var pares = numeros.Where(n => n % 2 == 0).ToList();
// [2, 4, 6, 8, 10]

// 2. MAP (Select)
var cuadrados = numeros.Select(n => n * n).ToList();
// [1, 4, 9, 16, 25, 36, 49, 64, 81, 100]

// 3. REDUCE (Aggregate)
var suma = numeros.Aggregate(0, (acc, n) => acc + n);
// 55

// 4. FOREACH
numeros.ForEach(n => Console.WriteLine($"Número: {n}"));

// 5. DISTINCT
var sinDuplicados = numeros. Distinct().ToList();

// 6. ORDER BY
var ordenados = numeros.OrderByDescending(n => n).ToList();

// 7. TAKE / SKIP (paginación)
var primerosCinco = numeros.Take(5).ToList();
var saltarTres = numeros.Skip(3).ToList();

// 8. ANY / ALL / CONTAINS
bool hayPares = numeros.Any(n => n % 2 == 0); // true
bool todosPares = numeros.All(n => n % 2 == 0); // false
bool contieneCinco = numeros.Contains(5); // true

// 9. FIRST / LAST / SINGLE
int primero = numeros.First(); // 1
int ultimo = numeros. Last(); // 10
int? primeroMayorQueDiez = numeros.FirstOrDefault(n => n > 10); // null

// 10. GROUP BY
var grupos = numeros
    .GroupBy(n => n % 2 == 0 ? "Pares" : "Impares")
    .ToList();
```

### Ejemplos prácticos

**Ejemplo completo con objetos:**

```csharp
public record Persona(string Nombre, int Edad, string Ciudad);

var personas = new List<Persona>
{
    new("Juan", 25, "Madrid"),
    new("María", 30, "Barcelona"),
    new("Pedro", 20, "Madrid"),
    new("Ana", 35, "Valencia")
};

// Filtrar, ordenar y transformar
var resultado = personas
    .Where(p => p. Edad > 25)
    .OrderBy(p => p. Nombre)
    .Select(p => new { p.Nombre, p.Ciudad })
    .ToList();

// Agrupar por ciudad
var porCiudad = personas
    .GroupBy(p => p. Ciudad)
    .Select(g => new
    {
        Ciudad = g. Key,
        Cantidad = g.Count(),
        EdadPromedio = g.Average(p => p.  Edad)
    })
    .ToList();

// Calcular estadísticas
double edadPromedio = personas. Average(p => p.Edad);
int edadMaxima = personas.Max(p => p.  Edad);
int edadMinima = personas.Min(p => p.Edad);

// Usar con comparadores
var ordenadoPorEdad = personas
    .OrderBy(p => p. Edad)
    .ThenBy(p => p. Nombre)
    .ToList();
```

**Operaciones en paralelo (AsParallel):**

```csharp
var numeros = Enumerable.Range(1, 1000000).ToList();

// Procesamiento paralelo
var suma = numeros
    .AsParallel()
    .Where(n => n % 2 == 0)
    .Sum();

Console.WriteLine($"Suma de pares: {suma}");
```

### Similitudes con SQL

LINQ tiene una sintaxis muy similar a SQL:

![SQL](../images/stream-sql.png)

| **LINQ (C#)**                          | **SQL**                                    | **Descripción**                                  |
|: ---------------------------------------|:-------------------------------------------|:-------------------------------------------------|
| `from p in productos`                  | `FROM productos`                           | Fuente de datos                                  |
| `where p.Precio > 100`                 | `WHERE Precio > 100`                       | Filtrar registros                                |
| `select new { p.Nombre, p.Precio }`    | `SELECT Nombre, Precio`                    | Proyectar columnas                               |
| `orderby p.Precio descending`          | `ORDER BY Precio DESC`                     | Ordenar resultados                               |
| `group p by p.Categoria`               | `GROUP BY Categoria`                       | Agrupar resultados                               |
| `.Count()`                             | `COUNT(*)`                                 | Contar registros                                 |
| `.Sum(p => p.Precio)`                  | `SUM(Precio)`                              | Sumar valores                                    |
| `.Average(p => p.Precio)`              | `AVG(Precio)`                              | Calcular promedio                                |
| `.Take(10)`                            | `LIMIT 10` / `TOP 10`                      | Limitar resultados                               |

**Ejemplo de sintaxis de consulta (Query Syntax):**

```csharp
// Sintaxis de consulta (similar a SQL)
var consulta = from p in productos
               where p.Categoria == "Electrónica"
               orderby p.Precio descending
               select new { p.Nombre, p.  Precio };

var resultado = consulta.ToList();

// Equivalente con sintaxis de métodos
var resultado2 = productos
    .Where(p => p.Categoria == "Electrónica")
    .OrderByDescending(p => p.Precio)
    .Select(p => new { p. Nombre, p.Precio })
    .ToList();
```

---

