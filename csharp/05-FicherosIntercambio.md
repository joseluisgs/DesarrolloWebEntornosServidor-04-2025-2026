# Ficheros y Formatos de Intercambio en . NET

---

- [Ficheros y Formatos de Intercambio en . NET](#ficheros-y-formatos-de-intercambio-en--net)
  - [Uso de recursos y gestión de archivos](#uso-de-recursos-y-gestión-de-archivos)
  - [Formatos de intercambio](#formatos-de-intercambio)
    - [CSV](#csv)
    - [JSON](#json)
    - [XML](#xml)

---

## Uso de recursos y gestión de archivos

En .NET, la gestión de recursos se realiza mediante la declaración `using`, que garantiza que los recursos se liberen automáticamente, similar al `try-with-resources` de Java.

**. NET utiliza dos APIs principales para trabajar con archivos:**

1. **`System.IO`**:  API clásica con `StreamReader`, `StreamWriter`, etc.
2. **`System.IO.File`**: API de alto nivel para operaciones comunes. 

**Lectura de archivos con `using`:**

```csharp
// Lectura línea por línea (API clásica)
using var reader = new StreamReader("archivo. txt");
string? linea;
while ((linea = reader.ReadLine()) != null)
{
    Console.WriteLine(linea);
}

// Lectura completa del archivo
string contenido = File.ReadAllText("archivo.txt");
Console.WriteLine(contenido);

// Lectura de todas las líneas
string[] lineas = File.ReadAllLines("archivo.txt");
foreach (var linea in lineas)
{
    Console.WriteLine(linea);
}

// Lectura asíncrona con IAsyncEnumerable
await foreach (var linea in File.ReadLinesAsync("archivo.txt"))
{
    Console.WriteLine(linea);
}
```

**Escritura de archivos:**

```csharp
// Escritura simple
File.WriteAllText("archivo.txt", "Contenido del archivo");

// Escritura de múltiples líneas
var lineas = new[] { "Primera línea", "Segunda línea", "Tercera línea" };
File.WriteAllLines("archivo.txt", lineas);

// Escritura con StreamWriter
using var writer = new StreamWriter("archivo.txt");
writer.WriteLine("Primera línea");
writer.WriteLine("Segunda línea");

// Escritura asíncrona
await File.WriteAllTextAsync("archivo.txt", "Contenido asíncrono");

// Append (agregar al final)
File.AppendAllText("archivo.txt", "\nLínea adicional");
```

**Uso de `Path` para rutas seguras:**

```csharp
// Construcción de rutas multiplataforma
string ruta = Path.Combine("datos", "archivos", "ejemplo.txt");

// Obtener información de la ruta
string directorio = Path.GetDirectoryName(ruta);
string nombreArchivo = Path.GetFileName(ruta);
string extension = Path.GetExtension(ruta);
string nombreSinExtension = Path.GetFileNameWithoutExtension(ruta);

// Verificar existencia
bool existe = File.Exists(ruta);
bool directorioExiste = Directory.Exists(directorio);
```

---

## Formatos de intercambio

Los formatos de intercambio de datos son estándares que definen cómo se estructuran y organizan los datos para su intercambio entre diferentes sistemas. 

### CSV

CSV (Comma-Separated Values) es un formato simple para representar datos tabulares. En . NET, podemos procesarlo manualmente o usar librerías como **CsvHelper**. 

**Lectura de CSV sin librerías:**

```csharp
public record Pokemon(string Nombre, string Tipo, int Nivel);

// Lectura simple con LINQ
var pokemons = File.ReadLines("pokemons.csv")
    .Skip(1) // Saltar la cabecera
    .Select(linea => linea.Split(','))
    .Select(valores => new Pokemon(
        valores[0]. Trim(),
        valores[1].Trim(),
        int.Parse(valores[2].Trim())
    ))
    .ToList();

// Imprimir
pokemons.ForEach(p => Console.WriteLine($"{p.Nombre} - {p.Tipo} - Nivel {p.Nivel}"));
```

**Escritura de CSV sin librerías:**

```csharp
var pokemons = new List<Pokemon>
{
    new("Pikachu", "Eléctrico", 10),
    new("Charmander", "Fuego", 10),
    new("Bulbasaur", "Planta", 10)
};

// Escribir con cabecera
var lineas = new[] { "Nombre,Tipo,Nivel" }
    . Concat(pokemons.Select(p => $"{p.Nombre},{p.Tipo},{p. Nivel}"));

File.WriteAllLines("pokemons.csv", lineas);
```

**Uso de CsvHelper (recomendado para CSV complejos):**

```bash
dotnet add package CsvHelper
```

```csharp
using CsvHelper;
using CsvHelper.Configuration;
using System.Globalization;

// Clase con mapeo personalizado
public class Pokemon
{
    public string Nombre { get; set; } = string.Empty;
    public string Tipo { get; set; } = string.Empty;
    public int Nivel { get; set; }
}

// Configuración del mapeo
public class PokemonMap : ClassMap<Pokemon>
{
    public PokemonMap()
    {
        Map(m => m.Nombre).Name("nombre");
        Map(m => m.Tipo).Name("tipo");
        Map(m => m. Nivel).Name("nivel");
    }
}

// Lectura con CsvHelper
using var reader = new StreamReader("pokemons.csv");
using var csv = new CsvReader(reader, CultureInfo.InvariantCulture);

csv.Context.RegisterClassMap<PokemonMap>();
var pokemons = csv.GetRecords<Pokemon>().ToList();

pokemons.ForEach(Console.WriteLine);

// Escritura con CsvHelper
using var writer = new StreamWriter("pokemons_output.csv");
using var csvWriter = new CsvWriter(writer, CultureInfo.InvariantCulture);

csvWriter.WriteRecords(pokemons);
```

**Manejo de CSV con caracteres especiales:**

```csharp
var config = new CsvConfiguration(CultureInfo.InvariantCulture)
{
    Delimiter = ";",  // Cambiar delimitador
    HasHeaderRecord = true,
    Quote = '"',
    MissingFieldFound = null, // Ignorar campos faltantes
    BadDataFound = null // Ignorar datos incorrectos
};

using var reader = new StreamReader("datos.csv");
using var csv = new CsvReader(reader, config);

var registros = csv.GetRecords<Pokemon>().ToList();
```

---

### JSON

JSON es el formato de intercambio de datos más utilizado actualmente. . NET proporciona `System.Text.Json` (recomendado) y también soporta `Newtonsoft.Json`.

**Usando `System.Text.Json` (nativo y de alto rendimiento):**

```csharp
using System.Text.Json;
using System.Text.Json.Serialization;

public record Pokemon(
    [property: JsonPropertyName("nombre")] string Nombre,
    [property: JsonPropertyName("tipo")] string Tipo,
    [property: JsonPropertyName("nivel")] int Nivel
);

// Opciones de serialización
var opciones = new JsonSerializerOptions
{
    PropertyNamingPolicy = JsonNamingPolicy.CamelCase,
    WriteIndented = true, // Pretty print
    DefaultIgnoreCondition = JsonIgnoreCondition.WhenWritingNull,
    Encoder = System.Text.Encodings.Web.JavaScriptEncoder.UnsafeRelaxedJsonEscaping
};

// Lectura de JSON
string jsonString = File. ReadAllText("pokemons. json");
var pokemons = JsonSerializer.Deserialize<List<Pokemon>>(jsonString, opciones);

if (pokemons != null)
{
    pokemons.ForEach(Console.WriteLine);
}

// Lectura asíncrona
await using var stream = File.OpenRead("pokemons.json");
var pokemonsAsync = await JsonSerializer.DeserializeAsync<List<Pokemon>>(stream, opciones);

// Escritura de JSON
var nuevosPokemons = new List<Pokemon>
{
    new("Pikachu", "Eléctrico", 10),
    new("Charmander", "Fuego", 10),
    new("Bulbasaur", "Planta", 10)
};

string json = JsonSerializer.Serialize(nuevosPokemons, opciones);
File.WriteAllText("pokemons_output.json", json);

// Escritura asíncrona
await using var writeStream = File.Create("pokemons_async.json");
await JsonSerializer. SerializeAsync(writeStream, nuevosPokemons, opciones);
```

**Manejo de fechas en JSON:**

```csharp
public record Entrenador(
    string Nombre,
    [property: JsonConverter(typeof(JsonDateOnlyConverter))] 
    DateOnly FechaNacimiento,
    [property: JsonConverter(typeof(JsonDateTimeConverter))] 
    DateTime UltimaConexion
);

// Convertidor personalizado para DateOnly
public class JsonDateOnlyConverter : JsonConverter<DateOnly>
{
    private const string Format = "yyyy-MM-dd";

    public override DateOnly Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
    {
        return DateOnly.ParseExact(reader.GetString()!, Format, CultureInfo.InvariantCulture);
    }

    public override void Write(Utf8JsonWriter writer, DateOnly value, JsonSerializerOptions options)
    {
        writer.WriteStringValue(value.ToString(Format, CultureInfo.InvariantCulture));
    }
}

// Convertidor para DateTime
public class JsonDateTimeConverter : JsonConverter<DateTime>
{
    public override DateTime Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
    {
        return DateTime.Parse(reader.GetString()!, CultureInfo.InvariantCulture);
    }

    public override void Write(Utf8JsonWriter writer, DateTime value, JsonSerializerOptions options)
    {
        writer.WriteStringValue(value.ToString("O", CultureInfo.InvariantCulture)); // ISO 8601
    }
}
```

**Lectura de JSON con estructura desconocida:**

```csharp
// Deserializar a JsonDocument
using var doc = JsonDocument.Parse(jsonString);
var root = doc.RootElement;

// Navegar por el JSON dinámicamente
if (root.TryGetProperty("nombre", out var nombreElement))
{
    string nombre = nombreElement.GetString() ?? string.Empty;
}

// Deserializar a Dictionary
var datos = JsonSerializer.Deserialize<Dictionary<string, object>>(jsonString);

// Usar JsonNode (más flexible)
var jsonNode = JsonNode.Parse(jsonString);
string?  tipo = jsonNode? ["tipo"]?.GetValue<string>();
```

**Validación de JSON con esquema:**

```csharp
// Ejemplo de validación manual
public static bool ValidarPokemon(JsonElement element)
{
    return element.TryGetProperty("nombre", out _) &&
           element.TryGetProperty("tipo", out _) &&
           element.TryGetProperty("nivel", out var nivelElement) &&
           nivelElement.TryGetInt32(out var nivel) &&
           nivel > 0;
}

// Uso
using var doc = JsonDocument.Parse(jsonString);
bool esValido = ValidarPokemon(doc.RootElement);
```

**Serialización con Railway Oriented Programming:**

```csharp
using CSharpFunctionalExtensions;

public static Result<List<Pokemon>> LeerPokemons(string rutaArchivo)
{
    try
    {
        if (!File.Exists(rutaArchivo))
            return Result.Failure<List<Pokemon>>("El archivo no existe");

        var json = File.ReadAllText(rutaArchivo);
        var pokemons = JsonSerializer.Deserialize<List<Pokemon>>(json);

        return pokemons != null
            ? Result.Success(pokemons)
            : Result.Failure<List<Pokemon>>("JSON inválido o vacío");
    }
    catch (JsonException ex)
    {
        return Result.Failure<List<Pokemon>>($"Error al deserializar JSON: {ex.Message}");
    }
    catch (IOException ex)
    {
        return Result.Failure<List<Pokemon>>($"Error de lectura: {ex.Message}");
    }
}

// Uso
var resultado = LeerPokemons("pokemons.json");

resultado.Match(
    onSuccess: pokemons => pokemons.ForEach(Console.WriteLine),
    onFailure: error => Console.WriteLine($"Error: {error}")
);
```

---

### XML

XML (eXtensible Markup Language) es otro formato de intercambio común, especialmente en sistemas legacy.

**Lectura de XML con LINQ to XML:**

```csharp
using System.Xml.Linq;

public record Pokemon(string Nombre, string Tipo, int Nivel);

// Archivo XML de ejemplo: 
/*
<pokemons>
  <pokemon>
    <nombre>Pikachu</nombre>
    <tipo>Eléctrico</tipo>
    <nivel>10</nivel>
  </pokemon>
  <pokemon>
    <nombre>Charmander</nombre>
    <tipo>Fuego</tipo>
    <nivel>10</nivel>
  </pokemon>
</pokemons>
*/

// Lectura con LINQ to XML
var doc = XDocument.Load("pokemons.xml");

var pokemons = doc.Descendants("pokemon")
    .Select(p => new Pokemon(
        p.Element("nombre")?.Value ?? string.Empty,
        p.Element("tipo")?.Value ?? string.Empty,
        int.Parse(p.Element("nivel")?.Value ?? "0")
    ))
    .ToList();

pokemons.ForEach(Console.WriteLine);
```

**Escritura de XML:**

```csharp
var pokemons = new List<Pokemon>
{
    new("Pikachu", "Eléctrico", 10),
    new("Charmander", "Fuego", 10)
};

var doc = new XDocument(
    new XElement("pokemons",
        pokemons.Select(p =>
            new XElement("pokemon",
                new XElement("nombre", p.Nombre),
                new XElement("tipo", p. Tipo),
                new XElement("nivel", p.Nivel)
            )
        )
    )
);

doc.Save("pokemons_output.xml");
```

**Serialización XML con atributos:**

```csharp
using System.Xml.Serialization;

[XmlRoot("pokemon")]
public class PokemonXml
{
    [XmlElement("nombre")]
    public string Nombre { get; set; } = string.Empty;

    [XmlElement("tipo")]
    public string Tipo { get; set; } = string.Empty;

    [XmlElement("nivel")]
    public int Nivel { get; set; }
}

// Serializar
var serializer = new XmlSerializer(typeof(List<PokemonXml>), new XmlRootAttribute("pokemons"));
using var writer = new StreamWriter("pokemons.xml");
serializer.Serialize(writer, pokemons);

// Deserializar
using var reader = new StreamReader("pokemons.xml");
var pokemonsDeserializados = (List<PokemonXml>)serializer.Deserialize(reader)!;
```

---

**Resumen de comparación:**

| **Formato** | **Ventajas**                              | **Desventajas**                          | **Uso recomendado**                      |
|: ------------|:------------------------------------------|:-----------------------------------------|:-----------------------------------------|
| **CSV**     | Simple, ligero, fácil de leer/editar     | Sin tipos de datos, sin jerarquía        | Datos tabulares simples                  |
| **JSON**    | Ligero, legible, amplio soporte          | Verboso para datos grandes               | APIs REST, configuración, intercambio web|
| **XML**     | Soporte de esquemas, validación          | Verboso, más complejo                    | Sistemas legacy, documentos complejos    |

---
