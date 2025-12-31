# Gestión de Proyectos y Construcción en .NET

---

- [Gestión de Proyectos y Construcción en .NET](#gestión-de-proyectos-y-construcción-en-net)
  - [Gestores de dependencias y construcción de proyectos](#gestores-de-dependencias-y-construcción-de-proyectos)
    - [dotnet CLI: La herramienta unificada](#dotnet-cli-la-herramienta-unificada)
    - [Ejemplo de archivo .csproj](#ejemplo-de-archivo-csproj)
    - [Ejemplo de solución (.sln)](#ejemplo-de-solución-sln)
    - [NuGet: Gestor de paquetes](#nuget-gestor-de-paquetes)
  - [Generación de código y reducción de boilerplate](#generación-de-código-y-reducción-de-boilerplate)
    - [Source Generators](#source-generators)
    - [Records para POCOs inmutables](#records-para-pocos-inmutables)
    - [Primary Constructors (C# 12+)](#primary-constructors-c-12)

---

## Gestores de dependencias y construcción de proyectos

Las herramientas de construcción (build tools) son programas que ayudan en la automatización del proceso de construcción y gestión de proyectos de software. En el ecosistema .  NET, **dotnet CLI** es la herramienta unificada que combina la compilación, gestión de dependencias, ejecución de pruebas, empaquetado y despliegue de aplicaciones. 

A diferencia de Java que tiene Maven y Gradle como herramientas separadas, .  NET consolida todo en una única herramienta de línea de comandos integrada con el SDK. 

### dotnet CLI: La herramienta unificada

**dotnet CLI** es la interfaz de línea de comandos multiplataforma para .  NET. Proporciona comandos para crear, compilar, ejecutar, probar y publicar aplicaciones. 

**Comandos principales:**

```bash
# CREACIÓN DE PROYECTOS
dotnet new console -n MiAplicacion              # Aplicación de consola
dotnet new webapi -n MiApi                      # API REST con ASP.NET Core
dotnet new classlib -n MiLibreria               # Biblioteca de clases
dotnet new nunit -n MiAplicacion.Tests          # Proyecto de tests con NUnit
dotnet new sln -n MiSolucion                    # Archivo de solución

# GESTIÓN DE DEPENDENCIAS (NuGet)
dotnet add package Newtonsoft.Json              # Agregar paquete
dotnet add package Dapper --version 2.1.28      # Agregar versión específica
dotnet remove package Newtonsoft.Json           # Eliminar paquete
dotnet list package                             # Listar paquetes instalados
dotnet list package --outdated                  # Ver paquetes desactualizados
dotnet restore                                  # Restaurar dependencias

# GESTIÓN DE REFERENCIAS
dotnet add reference ../MiLibreria/MiLibreria.csproj   # Referenciar otro proyecto
dotnet list reference                                   # Listar referencias

# COMPILACIÓN Y EJECUCIÓN
dotnet build                                    # Compilar el proyecto
dotnet build -c Release                         # Compilar en modo Release
dotnet run                                      # Compilar y ejecutar
dotnet run --project ./MiApi/MiApi.csproj      # Ejecutar proyecto específico

# TESTING
dotnet test                                     # Ejecutar todos los tests
dotnet test --filter "Category=Integration"    # Ejecutar tests filtrados
dotnet test --collect:"XPlat Code Coverage"    # Con cobertura de código

# PUBLICACIÓN Y DESPLIEGUE
dotnet publish -c Release -o ./publish         # Publicar aplicación
dotnet publish -r win-x64 --self-contained     # Publicación con runtime incluido
dotnet publish -r linux-x64 -p:PublishSingleFile=true  # Ejecutable único

# HERRAMIENTAS
dotnet tool install -g dotnet-ef               # Instalar herramienta global (EF Core)
dotnet ef migrations add InitialCreate         # Crear migración de BD
dotnet ef database update                      # Aplicar migraciones

# LIMPIEZA
dotnet clean                                   # Limpiar artefactos de compilación
```

### Ejemplo de archivo .csproj

El archivo `.csproj` es el equivalente al `pom.xml` de Maven o `build.gradle` de Gradle.  Define la configuración del proyecto, dependencias y opciones de compilación. 

**Ejemplo completo de un proyecto de API REST:**

```xml
<Project Sdk="Microsoft.NET. Sdk. Web">

  <!-- CONFIGURACIÓN BÁSICA -->
  <PropertyGroup>
    <!-- Framework objetivo -->
    <TargetFramework>net10.0</TargetFramework>
    
    <!-- Versión de C# -->
    <LangVersion>14</LangVersion>
    
    <!-- Habilitar nullable reference types -->
    <Nullable>enable</Nullable>
    
    <!-- Habilitar implicit usings (C# 10+) -->
    <ImplicitUsings>enable</ImplicitUsings>
    
    <!-- Tratar warnings como errores -->
    <TreatWarningsAsErrors>true</TreatWarningsAsErrors>
    
    <!-- Información del ensamblado -->
    <AssemblyName>MiAplicacion. Api</AssemblyName>
    <RootNamespace>MiAplicacion.Api</RootNamespace>
    <Version>1.0.0</Version>
    <Authors>Tu Nombre</Authors>
    <Company>Tu Empresa</Company>
    <Product>Mi Aplicación API</Product>
    <Description>API REST para gestión de productos</Description>
    
    <!-- Generar documentación XML -->
    <GenerateDocumentationFile>true</GenerateDocumentationFile>
    <NoWarn>$(NoWarn);1591</NoWarn>
  </PropertyGroup>

  <!-- DEPENDENCIAS DE NUGET -->
  <ItemGroup>
    <!-- ASP.NET Core -->
    <PackageReference Include="Microsoft.AspNetCore.OpenApi" Version="10.0.0" />
    <PackageReference Include="Swashbuckle.AspNetCore" Version="6.5.0" />
    
    <!-- Entity Framework Core -->
    <PackageReference Include="Microsoft.EntityFrameworkCore" Version="10.0.0" />
    <PackageReference Include="Microsoft. EntityFrameworkCore.Design" Version="10.0.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
    <PackageReference Include="Npgsql.EntityFrameworkCore. PostgreSQL" Version="10.0.0" />
    
    <!-- Validación y mapeo -->
    <PackageReference Include="FluentValidation. AspNetCore" Version="11.3.0" />
    <PackageReference Include="AutoMapper.Extensions.Microsoft.DependencyInjection" Version="12.0.0" />
    
    <!-- Funcional y Railway Oriented Programming -->
    <PackageReference Include="CSharpFunctionalExtensions" Version="2.40. 0" />
    
    <!-- Autenticación JWT -->
    <PackageReference Include="Microsoft.AspNetCore.Authentication.JwtBearer" Version="10.0.0" />
    
    <!-- Cliente HTTP -->
    <PackageReference Include="Refit" Version="7.0.0" />
    <PackageReference Include="Refit.HttpClientFactory" Version="7.0.0" />
    
    <!-- Observabilidad -->
    <PackageReference Include="Serilog.AspNetCore" Version="8.0.0" />
    <PackageReference Include="Serilog.Sinks.Console" Version="5.0.0" />
  </ItemGroup>

  <!-- REFERENCIAS A OTROS PROYECTOS -->
  <ItemGroup>
    <ProjectReference Include="..\MiAplicacion.Core\MiAplicacion.Core.csproj" />
    <ProjectReference Include="..\MiAplicacion.Infrastructure\MiAplicacion.Infrastructure.csproj" />
  </ItemGroup>

  <!-- CONFIGURACIÓN DE PUBLICACIÓN -->
  <PropertyGroup Condition="'$(Configuration)' == 'Release'">
    <DebugType>none</DebugType>
    <DebugSymbols>false</DebugSymbols>
    <Optimize>true</Optimize>
  </PropertyGroup>

</Project>
```

**Ejemplo de proyecto de biblioteca de clases:**

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <LangVersion>14</LangVersion>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    
    <!-- Información del paquete NuGet -->
    <PackageId>MiEmpresa.MiLibreria</PackageId>
    <Version>1.0.0</Version>
    <Authors>Tu Nombre</Authors>
    <Company>Tu Empresa</Company>
    <PackageLicenseExpression>MIT</PackageLicenseExpression>
    <PackageProjectUrl>https://github.com/tuusuario/milibreria</PackageProjectUrl>
    <RepositoryUrl>https://github.com/tuusuario/milibreria</RepositoryUrl>
    <RepositoryType>git</RepositoryType>
    <PackageTags>utilidades; helpers; extensions</PackageTags>
    <Description>Librería de utilidades comunes</Description>
    
    <!-- Generar paquete NuGet en cada build -->
    <GeneratePackageOnBuild>true</GeneratePackageOnBuild>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="System.Text.Json" Version="10.0.0" />
  </ItemGroup>

</Project>
```

**Ejemplo de proyecto de tests:**

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <LangVersion>14</LangVersion>
    <Nullable>enable</Nullable>
    <ImplicitUsings>enable</ImplicitUsings>
    
    <!-- Marcar como proyecto de test -->
    <IsPackable>false</IsPackable>
    <IsTestProject>true</IsTestProject>
  </PropertyGroup>

  <ItemGroup>
    <!-- Framework de testing -->
    <PackageReference Include="NUnit" Version="4.1.0" />
    <PackageReference Include="NUnit3TestAdapter" Version="4.5.0" />
    <PackageReference Include="Microsoft.NET. Test.Sdk" Version="17.9.0" />
    
    <!-- Mocking y assertions -->
    <PackageReference Include="Moq" Version="4.20.0" />
    <PackageReference Include="FluentAssertions" Version="6.12.0" />
    
    <!-- Testcontainers -->
    <PackageReference Include="Testcontainers" Version="3.7.0" />
    <PackageReference Include="Testcontainers. PostgreSql" Version="3.7.0" />
    
    <!-- Cobertura de código -->
    <PackageReference Include="coverlet.collector" Version="6.0.0">
      <PrivateAssets>all</PrivateAssets>
      <IncludeAssets>runtime; build; native; contentfiles; analyzers; buildtransitive</IncludeAssets>
    </PackageReference>
  </ItemGroup>

  <ItemGroup>
    <!-- Proyecto a testear -->
    <ProjectReference Include="..\MiAplicacion.Api\MiAplicacion.Api.csproj" />
  </ItemGroup>

</Project>
```

### Ejemplo de solución (.sln)

Una **solución** (`.sln`) es un contenedor que agrupa múltiples proyectos relacionados.   Es el equivalente a un multi-módulo de Maven o un multi-project de Gradle.

**Estructura típica de una solución:**

```
MiAplicacion/
├── MiAplicacion.sln                    # Archivo de solución
├── src/
│   ├── MiAplicacion. Api/              # Proyecto API
│   │   ├── MiAplicacion.Api.csproj
│   │   ├── Program.cs
│   │   ├── Controllers/
│   │   └── appsettings.json
│   ├── MiAplicacion.Core/             # Lógica de negocio
│   │   ├── MiAplicacion.Core.csproj
│   │   ├── Entities/
│   │   ├── Interfaces/
│   │   └── Services/
│   └── MiAplicacion.Infrastructure/   # Acceso a datos
│       ├── MiAplicacion.Infrastructure.csproj
│       ├── Data/
│       └── Repositories/
└── tests/
    ├── MiAplicacion. UnitTests/
    │   └── MiAplicacion.UnitTests.csproj
    └── MiAplicacion. IntegrationTests/
        └── MiAplicacion.IntegrationTests.csproj
```

**Comandos para gestionar soluciones:**

```bash
# Crear solución
dotnet new sln -n MiAplicacion

# Agregar proyectos a la solución
dotnet sln add src/MiAplicacion.Api/MiAplicacion.Api.csproj
dotnet sln add src/MiAplicacion.Core/MiAplicacion.Core.csproj
dotnet sln add src/MiAplicacion.Infrastructure/MiAplicacion.Infrastructure.csproj
dotnet sln add tests/MiAplicacion.UnitTests/MiAplicacion.UnitTests.csproj

# Listar proyectos en la solución
dotnet sln list

# Eliminar proyecto de la solución
dotnet sln remove tests/MiAplicacion.UnitTests/MiAplicacion.UnitTests.csproj

# Compilar toda la solución
dotnet build

# Ejecutar tests de toda la solución
dotnet test
```

### NuGet: Gestor de paquetes

**NuGet** es el gestor de paquetes oficial de .  NET, equivalente a Maven Central para Java o npm para Node.js.

**Fuentes de paquetes:**

```bash
# Listar fuentes configuradas
dotnet nuget list source

# Agregar fuente personalizada
dotnet nuget add source https://api.nuget.org/v3/index.json -n "NuGet Official"

# Agregar fuente privada con autenticación
dotnet nuget add source https://pkgs.dev.azure.com/myorg/_packaging/myfeed/nuget/v3/index. json \
  -n "Azure DevOps" \
  -u myusername \
  -p mypassword \
  --store-password-in-clear-text
```

**Archivo `nuget.config` (configuración centralizada):**

```xml
<?xml version="1.0" encoding="utf-8"?>
<configuration>
  <packageSources>
    <clear />
    <add key="nuget.org" value="https://api.nuget.org/v3/index.json" protocolVersion="3" />
    <add key="MyPrivateFeed" value="https://my-company.com/nuget/v3/index.json" />
  </packageSources>
  
  <packageSourceCredentials>
    <MyPrivateFeed>
      <add key="Username" value="myuser" />
      <add key="ClearTextPassword" value="mypassword" />
    </MyPrivateFeed>
  </packageSourceCredentials>
</configuration>
```

**Crear y publicar un paquete NuGet:**

```bash
# Empaquetar el proyecto
dotnet pack -c Release

# Publicar a NuGet. org (requiere API key)
dotnet nuget push bin/Release/MiLibreria.1.0.0.nupkg \
  --api-key tu-api-key \
  --source https://api.nuget.org/v3/index.json
```

---

## Generación de código y reducción de boilerplate

A diferencia de Java que usa Lombok para generar código repetitivo, C# tiene características nativas del lenguaje que eliminan la necesidad de librerías externas.

### Source Generators

**Source Generators** (C# 9+) son una característica del compilador que permite generar código adicional durante la compilación.  Es similar a los procesadores de anotaciones de Java, pero más potente.

**Ejemplo de uso con JSON serialization:**

```csharp
using System.Text.Json. Serialization;

namespace MiAplicacion.Models;

// El source generator genera automáticamente el código de serialización
[JsonSerializable(typeof(Producto))]
[JsonSerializable(typeof(List<Producto>))]
internal partial class ProductoJsonContext : JsonSerializerContext
{
}

public record Producto(int Id, string Nombre, decimal Precio);

// Uso
var producto = new Producto(1, "Laptop", 1200m);
var json = JsonSerializer. Serialize(producto, ProductoJsonContext.Default. Producto);
```

### Records para POCOs inmutables

Los **records** (C# 9+) son el equivalente directo a las clases con `@Data` de Lombok, pero son nativos del lenguaje. 

**Comparación Lombok vs C# Records:**

```java
// Java con Lombok
@Data
@AllArgsConstructor
@NoArgsConstructor
public class Producto {
    private int id;
    private String nombre;
    private double precio;
}
```

```csharp
// C# con Records (mucho más conciso)
public record Producto(int Id, string Nombre, decimal Precio);

// Automáticamente incluye: 
// - Constructor con todos los parámetros
// - Propiedades de solo lectura
// - Equals y GetHashCode por valor
// - ToString descriptivo
// - Deconstruction
// - With expressions para copias modificadas
```

**Características avanzadas de records:**

```csharp
// Record con propiedades adicionales
public record Producto(int Id, string Nombre, decimal Precio)
{
    // Propiedad calculada
    public decimal PrecioConIva => Precio * 1.21m;
    
    // Método personalizado
    public bool EsCaro() => Precio > 1000;
}

// Uso
var producto = new Producto(1, "Laptop", 1200m);

// With expression (copia inmutable con cambios)
var productoRebajado = producto with { Precio = 1000m };

// Deconstruction
var (id, nombre, precio) = producto;

// Comparación por valor
var producto2 = new Producto(1, "Laptop", 1200m);
Console.WriteLine(producto == producto2); // True

// ToString automático
Console.WriteLine(producto);
// Salida: Producto { Id = 1, Nombre = Laptop, Precio = 1200 }
```

**Records mutables (cuando se necesitan):**

```csharp
// Record con propiedades mutables
public record ProductoMutable
{
    public int Id { get; set; }
    public string Nombre { get; set; } = string.Empty;
    public decimal Precio { get; set; }
}
```

### Primary Constructors (C# 12+)

Los **Primary Constructors** eliminan aún más código repetitivo, similar a `@AllArgsConstructor` de Lombok.

**Comparación Lombok vs Primary Constructors:**

```java
// Java con Lombok
@AllArgsConstructor
public class ServicioProducto {
    private final IRepositorio repositorio;
    private final ILogger logger;
    
    public void guardar(Producto p) {
        logger.info("Guardando producto");
        repositorio.save(p);
    }
}
```

```csharp
// C# con Primary Constructor
public class ServicioProducto(IRepositorio repositorio, ILogger<ServicioProducto> logger)
{
    // Los parámetros están disponibles directamente
    public void Guardar(Producto p)
    {
        logger.LogInformation("Guardando producto");
        repositorio.Save(p);
    }
}
```

**Combinando records y primary constructors:**

```csharp
// POJO completo en una línea
public record Usuario(string Email, string Nombre, int Edad);

// Con validación
public record Usuario(string Email, string Nombre, int Edad)
{
    // Validación en el constructor
    public Usuario(string Email, string Nombre, int Edad) :  this(Email, Nombre, Edad)
    {
        ArgumentException.ThrowIfNullOrWhiteSpace(Email);
        ArgumentException.ThrowIfNullOrWhiteSpace(Nombre);
        if (Edad < 18) throw new ArgumentException("Debe ser mayor de edad");
    }
}

// Con propiedades calculadas e init-only
public record Producto(string Nombre, decimal Precio, string Categoria)
{
    // Propiedad init-only adicional
    public DateTime FechaCreacion { get; init; } = DateTime.UtcNow;
    
    // Propiedad calculada
    public decimal PrecioConDescuento => Precio * 0.9m;
}
```

**Comparación final:  Lombok vs Características nativas de C#**

| **Característica**              | **Java (Lombok)**                    | **C# (Nativo)**                          |
|: --------------------------------|:-------------------------------------|:-----------------------------------------|
| Getters/Setters                 | `@Getter` / `@Setter`                | Propiedades automáticas                  |
| Constructor sin args            | `@NoArgsConstructor`                 | Constructor por defecto                  |
| Constructor con todos los args  | `@AllArgsConstructor`                | Primary Constructor                      |
| ToString                        | `@ToString`                          | `record` (automático)                    |
| Equals/HashCode                 | `@EqualsAndHashCode`                 | `record` (automático)                    |
| POJO completo                   | `@Data`                              | `record`                                 |
| Builder                         | `@Builder`                           | `with` expressions + init                |
| Inmutabilidad                   | `@Value`                             | `record` (por defecto)                   |
| Logger                          | `@Slf4j`                             | Inyección de dependencias                |

---