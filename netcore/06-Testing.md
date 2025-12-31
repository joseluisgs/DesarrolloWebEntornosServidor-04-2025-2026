# Testing en ASP.NET Core

- [Testing en ASP.NET Core](#testing-en-aspnet-core)
  - [Testing en .  NET](#testing-en---net)
  - [Test Unitarios con NUnit y Moq](#test-unitarios-con-nunit-y-moq)
    - [Configuración del Proyecto de Pruebas](#configuración-del-proyecto-de-pruebas)
    - [Estructura de Tests con NUnit](#estructura-de-tests-con-nunit)
    - [Mocking con Moq](#mocking-con-moq)
    - [Testeando Servicios con Result (ROP)](#testeando-servicios-con-result-rop)
  - [Test de Integración](#test-de-integración)
    - [WebApplicationFactory](#webapplicationfactory)
    - [Test de Integración de Controladores](#test-de-integración-de-controladores)
  - [Testeando Controladores con MockMvc](#testeando-controladores-con-mockmvc)
    - [Test Unitario del Controlador](#test-unitario-del-controlador)
    - [Test de Integración HTTP](#test-de-integración-http)
  - [Cobertura de Código](#cobertura-de-código)
  - [Práctica de clase:   Testing Completo](#práctica-de-clase---testing-completo)
  - [Proyecto del curso](#proyecto-del-curso)

![Testing Banner](../images/banner06.webp)

---

## Testing en .  NET

El **testing** es fundamental en el desarrollo de software moderno.   Nos permite: 

✅ **Asegurar que el código funciona correctamente**

✅ **Detectar errores rápidamente**

✅ **Facilitar el mantenimiento y refactorización**

✅ **Documentar el comportamiento esperado**

✅ **Aumentar la confianza al hacer cambios**

**Niveles de testing:**

1. **Test Unitarios**: Prueban una unidad de código (clase, método) de forma aislada usando **mocks**.

2. **Test de Integración**:  Prueban la integración entre componentes (controlador + servicio + repositorio).

3. **Test End-to-End (E2E)**: Prueban el sistema completo, simulando un usuario real (ej:  con Postman, Playwright).

---

## Test Unitarios con NUnit y Moq

En .  NET usamos: 

- **NUnit**: Framework de testing (alternativas: xUnit, MSTest)
- **Moq**:  Biblioteca para crear mocks de dependencias

### Configuración del Proyecto de Pruebas

**Crear proyecto de pruebas en Rider:**

```bash
# Opción 1: Desde la terminal de Rider
dotnet new nunit -n FunkosApi. Tests -f net10.0
cd FunkosApi.Tests

# Agregar referencia al proyecto principal
dotnet add reference ../FunkosApi/FunkosApi. csproj

# Agregar paquetes necesarios
dotnet add package Moq
dotnet add package FluentAssertions
dotnet add package Microsoft.AspNetCore.Mvc.Testing
```

**Opción 2: Desde la interfaz de Rider**

1. Click derecho en la solución → Add → New Project
2. Seleccionar **NUnit Test Project**
3. Nombre: `FunkosApi.Tests`
4. Agregar referencia al proyecto `FunkosApi`

**Estructura del archivo `.csproj`:**

```xml
<Project Sdk="Microsoft.NET.Sdk">

  <PropertyGroup>
    <TargetFramework>net10.0</TargetFramework>
    <ImplicitUsings>enable</ImplicitUsings>
    <Nullable>enable</Nullable>
    <IsPackable>false</IsPackable>
    <IsTestProject>true</IsTestProject>
  </PropertyGroup>

  <ItemGroup>
    <PackageReference Include="Microsoft.NET.Test.Sdk" Version="17.11.0" />
    <PackageReference Include="NUnit" Version="4.2.2" />
    <PackageReference Include="NUnit.Analyzers" Version="4.3.0" />
    <PackageReference Include="NUnit3TestAdapter" Version="4.6.0" />
    <PackageReference Include="coverlet.collector" Version="6.0.2" />
    
    <!-- Mocking -->
    <PackageReference Include="Moq" Version="4.20.70" />
    
    <!-- Aserciones fluidas -->
    <PackageReference Include="FluentAssertions" Version="6.12.0" />
    
    <!-- Testing de ASP.NET Core -->
    <PackageReference Include="Microsoft. AspNetCore.Mvc.Testing" Version="10.0.0" />
  </ItemGroup>

  <ItemGroup>
    <ProjectReference Include="..\FunkosApi\FunkosApi.csproj" />
  </ItemGroup>

</Project>
```

---

### Estructura de Tests con NUnit

**Anatomía de un test con NUnit:**

```csharp
using NUnit.Framework;
using FluentAssertions;

namespace FunkosApi.Tests. Repositories;

[TestFixture] // Marca la clase como contenedor de tests
public class FunkoRepositoryTests
{
    private FunkoRepository _repository = null!;

    [SetUp] // Se ejecuta ANTES de cada test
    public void Setup()
    {
        _repository = new FunkoRepository();
    }

    [TearDown] // Se ejecuta DESPUÉS de cada test
    public void TearDown()
    {
        // Limpieza si es necesario
    }

    [OneTimeSetUp] // Se ejecuta UNA VEZ antes de todos los tests
    public void OneTimeSetup()
    {
        // Inicialización global
    }

    [OneTimeTearDown] // Se ejecuta UNA VEZ después de todos los tests
    public void OneTimeTeardown()
    {
        // Limpieza global
    }

    [Test] // Marca el método como test
    public async Task GetAllAsync_ReturnsAllFunkos()
    {
        // Arrange (Preparar)
        var expectedCount = 3;

        // Act (Actuar)
        var result = await _repository.GetAllAsync();

        // Assert (Afirmar)
        Assert.That(result, Is.Not.Null);
        Assert.That(result.Count(), Is.EqualTo(expectedCount));
        
        // FluentAssertions (más legible)
        result.Should().NotBeNull();
        result.Should().HaveCount(expectedCount);
    }

    [Test]
    public async Task GetByIdAsync_WithValidId_ReturnsFunko()
    {
        // Arrange
        var id = 1;

        // Act
        var result = await _repository.GetByIdAsync(id);

        // Assert
        result.Should().NotBeNull();
        result! .Id.Should().Be(id);
        result. Nombre.Should().NotBeNullOrEmpty();
    }

    [Test]
    public async Task GetByIdAsync_WithInvalidId_ReturnsNull()
    {
        // Arrange
        var invalidId = 9999;

        // Act
        var result = await _repository.GetByIdAsync(invalidId);

        // Assert
        result.Should().BeNull();
    }

    [TestCase(1)]
    [TestCase(2)]
    [TestCase(3)]
    public async Task GetByIdAsync_WithMultipleIds_ReturnsFunko(int id)
    {
        // Act
        var result = await _repository.GetByIdAsync(id);

        // Assert
        result. Should().NotBeNull();
        result!.Id.Should().Be(id);
    }
}
```

**Anotaciones importantes de NUnit:**

- `[TestFixture]`: Marca una clase como contenedor de tests
- `[Test]`: Marca un método como test
- `[TestCase(... )]`: Permite ejecutar el mismo test con diferentes parámetros
- `[SetUp]` / `[TearDown]`: Se ejecutan antes/después de cada test
- `[OneTimeSetUp]` / `[OneTimeTearDown]`: Se ejecutan una vez antes/después de todos los tests
- `[Ignore("Razón")]`: Ignora un test temporalmente
- `[Category("Nombre")]`: Agrupa tests por categorías

---

### Mocking con Moq

**Moq** permite crear **objetos simulados (mocks)** de interfaces/clases para aislar el código bajo prueba.

**Ejemplo:   Testeando un Servicio mockeando el Repositorio**

```csharp
using Moq;
using NUnit.Framework;
using FluentAssertions;
using CSharpFunctionalExtensions;

namespace FunkosApi.Tests.Services;

[TestFixture]
public class FunkoServiceTests
{
    private Mock<IFunkoRepository> _repositoryMock = null!;
    private Mock<IMemoryCache> _cacheMock = null!;
    private Mock<ILogger<FunkoService>> _loggerMock = null!;
    private FunkoService _service = null!;

    [SetUp]
    public void Setup()
    {
        // Crear mocks
        _repositoryMock = new Mock<IFunkoRepository>();
        _cacheMock = new Mock<IMemoryCache>();
        _loggerMock = new Mock<ILogger<FunkoService>>();

        // Inyectar mocks en el servicio
        _service = new FunkoService(
            _repositoryMock. Object,
            _loggerMock.Object,
            _cacheMock.Object
        );
    }

    [Test]
    public async Task GetAllAsync_ReturnsAllFunkos()
    {
        // Arrange
        var expectedFunkos = new List<Funko>
        {
            new() { Id = 1, Nombre = "Funko 1", Precio = 10, Cantidad = 5, Categoria = "Marvel" },
            new() { Id = 2, Nombre = "Funko 2", Precio = 20, Cantidad = 3, Categoria = "DC" }
        };

        // Configurar el mock para que devuelva datos específicos
        _repositoryMock
            .Setup(repo => repo. GetAllAsync())
            .ReturnsAsync(expectedFunkos);

        // Act
        var result = await _service.GetAllAsync();

        // Assert
        result.IsSuccess.Should().BeTrue();
        result.Value.Should().HaveCount(2);
        result.Value.Should().BeEquivalentTo(expectedFunkos);

        // Verificar que se llamó al repositorio exactamente 1 vez
        _repositoryMock.Verify(repo => repo.GetAllAsync(), Times.Once);
    }

    [Test]
    public async Task GetByIdAsync_WithExistingId_ReturnsSuccess()
    {
        // Arrange
        var funkoId = 1;
        var expectedFunko = new Funko 
        { 
            Id = funkoId, 
            Nombre = "Iron Man", 
            Precio = 25, 
            Cantidad = 10, 
            Categoria = "Marvel" 
        };

        _repositoryMock
            . Setup(repo => repo.GetByIdAsync(funkoId))
            .ReturnsAsync(expectedFunko);

        // Configurar mock de caché (no está en caché)
        object?  cachedValue = null;
        _cacheMock
            .Setup(cache => cache.TryGetValue(It.IsAny<object>(), out cachedValue))
            .Returns(false);

        // Act
        var result = await _service.GetByIdAsync(funkoId);

        // Assert
        result.IsSuccess.Should().BeTrue();
        result.Value.Should().BeEquivalentTo(expectedFunko);

        // Verificaciones
        _repositoryMock. Verify(repo => repo.GetByIdAsync(funkoId), Times.Once);
        _cacheMock.Verify(cache => cache.Set(
            It.IsAny<object>(),
            It.IsAny<object>(),
            It.IsAny<TimeSpan>()), Times.Once);
    }

    [Test]
    public async Task GetByIdAsync_WithNonExistingId_ReturnsFailure()
    {
        // Arrange
        var invalidId = 9999;

        _repositoryMock
            . Setup(repo => repo.GetByIdAsync(invalidId))
            .ReturnsAsync((Funko?)null);

        object? cachedValue = null;
        _cacheMock
            . Setup(cache => cache.TryGetValue(It.IsAny<object>(), out cachedValue))
            .Returns(false);

        // Act
        var result = await _service.GetByIdAsync(invalidId);

        // Assert
        result.IsFailure.Should().BeTrue();
        result.Error.Code.Should().Be("FUNKO_NOT_FOUND");
        result.Error.Message.Should().Contain(invalidId.ToString());

        _repositoryMock.Verify(repo => repo.GetByIdAsync(invalidId), Times.Once);
    }

    [Test]
    public async Task CreateAsync_WithValidData_ReturnsSuccess()
    {
        // Arrange
        var dto = new CreateFunkoDto("Spider-Man", 24.99m, 10, "Marvel", null);
        var expectedFunko = new Funko
        {
            Id = 1,
            Nombre = dto.Nombre,
            Precio = dto.Precio,
            Cantidad = dto.Cantidad,
            Categoria = dto. Categoria
        };

        _repositoryMock
            .Setup(repo => repo.GetByNombreAsync(dto.Nombre))
            .ReturnsAsync((Funko?)null); // No existe duplicado

        _repositoryMock
            .Setup(repo => repo.CreateAsync(It.IsAny<Funko>()))
            .ReturnsAsync(expectedFunko);

        // Act
        var result = await _service.CreateAsync(dto);

        // Assert
        result.IsSuccess.Should().BeTrue();
        result.Value. Nombre.Should().Be(dto.Nombre);
        result.Value.Precio.Should().Be(dto.Precio);

        _repositoryMock.Verify(repo => repo.CreateAsync(It.IsAny<Funko>()), Times.Once);
    }

    [Test]
    public async Task CreateAsync_WithInvalidPrice_ReturnsFailure()
    {
        // Arrange
        var dto = new CreateFunkoDto("Batman", -10m, 5, "DC", null); // Precio negativo

        // Act
        var result = await _service.CreateAsync(dto);

        // Assert
        result.IsFailure.Should().BeTrue();
        result.Error.Code. Should().Be("FUNKO_INVALID");
        result.Error.Message. Should().Contain("Precio");

        // No debe llamar al repositorio
        _repositoryMock.Verify(repo => repo.CreateAsync(It.IsAny<Funko>()), Times.Never);
    }

    [Test]
    public async Task CreateAsync_WithDuplicateName_ReturnsConflict()
    {
        // Arrange
        var dto = new CreateFunkoDto("Iron Man", 25m, 10, "Marvel", null);
        var existingFunko = new Funko { Id = 1, Nombre = "Iron Man", Precio = 20, Cantidad = 5, Categoria = "Marvel" };

        _repositoryMock
            .Setup(repo => repo.GetByNombreAsync(dto. Nombre))
            .ReturnsAsync(existingFunko); // Ya existe

        // Act
        var result = await _service.CreateAsync(dto);

        // Assert
        result.IsFailure.Should().BeTrue();
        result.Error.Code.Should().Be("FUNKO_CONFLICT");
        result.Error.Message.Should().Contain(dto.Nombre);

        _repositoryMock.Verify(repo => repo.CreateAsync(It.IsAny<Funko>()), Times.Never);
    }

    [Test]
    public async Task DeleteAsync_WithExistingId_ReturnsSuccess()
    {
        // Arrange
        var funkoId = 1;

        _repositoryMock
            .Setup(repo => repo.DeleteAsync(funkoId))
            .ReturnsAsync(true);

        // Act
        var result = await _service.DeleteAsync(funkoId);

        // Assert
        result.IsSuccess.Should().BeTrue();

        _repositoryMock.Verify(repo => repo.DeleteAsync(funkoId), Times.Once);
        _cacheMock.Verify(cache => cache.Remove(It. IsAny<object>()), Times.Once);
    }
}
```

**Métodos principales de Moq:**

- `Setup(...)`: Configura el comportamiento del mock
- `Returns(...)` / `ReturnsAsync(...)`: Especifica el valor de retorno
- `Throws(...)` / `ThrowsAsync(...)`: Lanza una excepción
- `Verify(...)`: Verifica que se llamó al método
- `Times.Once`, `Times.Never`, `Times.AtLeastOnce`, etc.: Cantidad de llamadas esperadas
- `It.IsAny<T>()`: Cualquier valor del tipo `T`
- `It.Is<T>(condition)`: Valor que cumple una condición

---

### Testeando Servicios con Result (ROP)

Cuando usamos **Railway Oriented Programming (ROP)** con `Result<T, Error>`, los tests son más claros: 

```csharp
[Test]
public async Task GetByIdAsync_WithValidId_ReturnsSuccessResult()
{
    // Arrange
    var funkoId = 1;
    var expectedFunko = new Funko { Id = funkoId, Nombre = "Test Funko", Precio = 15m, Cantidad = 5, Categoria = "Test" };

    _repositoryMock
        .Setup(repo => repo.GetByIdAsync(funkoId))
        .ReturnsAsync(expectedFunko);

    // Act
    var result = await _service.GetByIdAsync(funkoId);

    // Assert
    result.IsSuccess.Should().BeTrue();
    result.IsFailure.Should().BeFalse();
    result.Value.Should().BeEquivalentTo(expectedFunko);
    
    // El error no debe estar accesible
    Action act = () => _ = result.Error;
    act.Should().Throw<InvalidOperationException>();
}

[Test]
public async Task GetByIdAsync_WithInvalidId_ReturnsFailureResult()
{
    // Arrange
    var invalidId = 9999;

    _repositoryMock
        .Setup(repo => repo.GetByIdAsync(invalidId))
        .ReturnsAsync((Funko?)null);

    // Act
    var result = await _service.GetByIdAsync(invalidId);

    // Assert
    result.IsFailure.Should().BeTrue();
    result.IsSuccess.Should().BeFalse();
    result.Error.Code.Should().Be("FUNKO_NOT_FOUND");
    result.Error.Message.Should().Contain(invalidId.ToString());

    // El valor no debe estar accesible
    Action act = () => _ = result.Value;
    act.Should().Throw<InvalidOperationException>();
}
```

---

## Test de Integración

Los **tests de integración** prueban múltiples componentes trabajando juntos (sin mocks o con mocks limitados).

### WebApplicationFactory

ASP.NET Core proporciona `WebApplicationFactory<T>` para crear un servidor de pruebas en memoria. 

**Configuración:**

```csharp
using Microsoft.AspNetCore.Mvc.Testing;
using NUnit.Framework;

namespace FunkosApi.Tests. Integration;

public class IntegrationTestBase
{
    protected WebApplicationFactory<Program> Factory = null!;
    protected HttpClient Client = null!;

    [OneTimeSetUp]
    public void OneTimeSetup()
    {
        Factory = new WebApplicationFactory<Program>();
        Client = Factory.CreateClient();
    }

    [OneTimeTearDown]
    public void OneTimeTeardown()
    {
        Client.Dispose();
        Factory.Dispose();
    }
}
```

---

### Test de Integración de Controladores

```csharp
using System.Net;
using System.Net.Http.Json;
using FluentAssertions;
using NUnit.Framework;

namespace FunkosApi.Tests. Integration;

[TestFixture]
public class FunkosControllerIntegrationTests :  IntegrationTestBase
{
    private const string BaseUrl = "/api/funkos";

    [Test]
    public async Task GetAll_ReturnsOkWithFunkos()
    {
        // Act
        var response = await Client. GetAsync(BaseUrl);

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.OK);

        var funkos = await response.Content.ReadFromJsonAsync<List<FunkoResponseDto>>();
        funkos.Should().NotBeNull();
        funkos. Should().NotBeEmpty();
    }

    [Test]
    public async Task GetById_WithValidId_ReturnsOk()
    {
        // Arrange
        var funkoId = 1;

        // Act
        var response = await Client.GetAsync($"{BaseUrl}/{funkoId}");

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.OK);

        var funko = await response.Content.ReadFromJsonAsync<FunkoResponseDto>();
        funko.Should().NotBeNull();
        funko! .Id.Should().Be(funkoId);
    }

    [Test]
    public async Task GetById_WithInvalidId_ReturnsNotFound()
    {
        // Arrange
        var invalidId = 9999;

        // Act
        var response = await Client.GetAsync($"{BaseUrl}/{invalidId}");

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.NotFound);
    }

    [Test]
    public async Task Create_WithValidData_ReturnsCreated()
    {
        // Arrange
        var newFunko = new CreateFunkoDto(
            "Test Funko",
            19.99m,
            10,
            "Marvel",
            null
        );

        // Act
        var response = await Client.PostAsJsonAsync(BaseUrl, newFunko);

        // Assert
        response. StatusCode.Should().Be(HttpStatusCode.Created);

        var createdFunko = await response.Content.ReadFromJsonAsync<FunkoResponseDto>();
        createdFunko.Should().NotBeNull();
        createdFunko!.Nombre. Should().Be(newFunko.Nombre);
        createdFunko. Precio.Should().Be(newFunko.Precio);

        // Verificar header Location
        response.Headers.Location. Should().NotBeNull();
    }

    [Test]
    public async Task Create_WithInvalidData_ReturnsBadRequest()
    {
        // Arrange (precio negativo)
        var invalidFunko = new CreateFunkoDto(
            "Invalid Funko",
            -10m,
            5,
            "DC",
            null
        );

        // Act
        var response = await Client.PostAsJsonAsync(BaseUrl, invalidFunko);

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.BadRequest);
    }

    [Test]
    public async Task Update_WithValidData_ReturnsOk()
    {
        // Arrange
        var funkoId = 1;
        var updateDto = new UpdateFunkoDto(
            "Updated Funko",
            29.99m,
            15,
            "DC",
            null
        );

        // Act
        var response = await Client.PutAsJsonAsync($"{BaseUrl}/{funkoId}", updateDto);

        // Assert
        response. StatusCode.Should().Be(HttpStatusCode.OK);

        var updatedFunko = await response.Content.ReadFromJsonAsync<FunkoResponseDto>();
        updatedFunko.Should().NotBeNull();
        updatedFunko!.Nombre. Should().Be(updateDto.Nombre);
        updatedFunko.Precio.Should().Be(updateDto.Precio);
    }

    [Test]
    public async Task Delete_WithValidId_ReturnsNoContent()
    {
        // Arrange
        // Primero crear un funko para eliminar
        var newFunko = new CreateFunkoDto("To Delete", 10m, 1, "Test", null);
        var createResponse = await Client.PostAsJsonAsync(BaseUrl, newFunko);
        var created = await createResponse.Content.ReadFromJsonAsync<FunkoResponseDto>();

        // Act
        var response = await Client.DeleteAsync($"{BaseUrl}/{created! .Id}");

        // Assert
        response.StatusCode.Should().Be(HttpStatusCode.NoContent);

        // Verificar que ya no existe
        var getResponse = await Client.GetAsync($"{BaseUrl}/{created. Id}");
        getResponse. StatusCode.Should().Be(HttpStatusCode.NotFound);
    }
}
```

---

## Testeando Controladores con MockMvc

Aunque en .  NET Core no existe un equivalente exacto a `MockMvc` de Spring, podemos lograr lo mismo de dos formas:

### Test Unitario del Controlador

Mockeando las dependencias y testeando solo el controlador:

```csharp
using Moq;
using NUnit.Framework;
using FluentAssertions;
using Microsoft.AspNetCore.Mvc;
using CSharpFunctionalExtensions;

namespace FunkosApi.Tests.Controllers;

[TestFixture]
public class FunkosControllerUnitTests
{
    private Mock<IFunkoService> _serviceMock = null!;
    private Mock<ILogger<FunkosController>> _loggerMock = null!;
    private FunkosController _controller = null!;

    [SetUp]
    public void Setup()
    {
        _serviceMock = new Mock<IFunkoService>();
        _loggerMock = new Mock<ILogger<FunkosController>>();
        _controller = new FunkosController(_serviceMock.Object, _loggerMock.Object);
    }

    [Test]
    public async Task GetAll_ReturnsOkWithFunkos()
    {
        // Arrange
        var funkos = new List<Funko>
        {
            new() { Id = 1, Nombre = "Funko 1", Precio = 10, Cantidad = 5, Categoria = "Marvel" },
            new() { Id = 2, Nombre = "Funko 2", Precio = 20, Cantidad = 3, Categoria = "DC" }
        };

        _serviceMock
            .Setup(s => s.GetAllAsync(null))
            .ReturnsAsync(Result. Success<IEnumerable<Funko>>(funkos));

        // Act
        var result = await _controller.GetAll(null);

        // Assert
        var okResult = result.Result. Should().BeOfType<OkObjectResult>().Subject;
        var returnedFunkos = okResult.Value. Should().BeAssignableTo<IEnumerable<FunkoResponseDto>>().Subject;
        returnedFunkos.Should().HaveCount(2);

        _serviceMock.Verify(s => s.GetAllAsync(null), Times.Once);
    }

    [Test]
    public async Task GetById_WithValidId_ReturnsOk()
    {
        // Arrange
        var funkoId = 1;
        var funko = new Funko { Id = funkoId, Nombre = "Test", Precio = 15m, Cantidad = 10, Categoria = "Marvel" };

        _serviceMock
            .Setup(s => s.GetByIdAsync(funkoId))
            .ReturnsAsync(Result.Success<Funko, FunkoError>(funko));

        // Act
        var result = await _controller.GetById(funkoId);

        // Assert
        var okResult = result.Result.Should().BeOfType<OkObjectResult>().Subject;
        var returnedFunko = okResult. Value.Should().BeOfType<FunkoResponseDto>().Subject;
        returnedFunko.Id.Should().Be(funkoId);

        _serviceMock.Verify(s => s.GetByIdAsync(funkoId), Times.Once);
    }

    [Test]
    public async Task GetById_WithInvalidId_ReturnsNotFound()
    {
        // Arrange
        var invalidId = 9999;
        var error = FunkoError.NotFound(invalidId);

        _serviceMock
            .Setup(s => s.GetByIdAsync(invalidId))
            .ReturnsAsync(Result.Failure<Funko, FunkoError>(error));

        // Act
        var result = await _controller.GetById(invalidId);

        // Assert
        var notFoundResult = result.Result.Should().BeOfType<NotFoundObjectResult>().Subject;
        var problemDetails = notFoundResult.Value.Should().BeOfType<ProblemDetails>().Subject;
        problemDetails.Status.Should().Be(404);
        problemDetails.Title.Should().Be(error.Code);

        _serviceMock.Verify(s => s.GetByIdAsync(invalidId), Times.Once);
    }

    [Test]
    public async Task Create_WithValidData_ReturnsCreated()
    {
        // Arrange
        var dto = new CreateFunkoDto("New Funko", 25m, 10, "Marvel", null);
        var createdFunko = new Funko 
        { 
            Id = 1, 
            Nombre = dto.Nombre, 
            Precio = dto.Precio, 
            Cantidad = dto. Cantidad, 
            Categoria = dto.Categoria 
        };

        _serviceMock
            .Setup(s => s.CreateAsync(dto))
            .ReturnsAsync(Result.Success<Funko, FunkoError>(createdFunko));

        // Act
        var result = await _controller.Create(dto);

        // Assert
        var createdResult = result.Result.Should().BeOfType<CreatedAtActionResult>().Subject;
        createdResult.ActionName.Should().Be(nameof(FunkosController. GetById));
        createdResult.RouteValues! ["id"].Should().Be(createdFunko.Id);

        var returnedFunko = createdResult.Value.Should().BeOfType<FunkoResponseDto>().Subject;
        returnedFunko. Nombre.Should().Be(dto.Nombre);

        _serviceMock.Verify(s => s.CreateAsync(dto), Times.Once);
    }

    [Test]
    public async Task Create_WithInvalidData_ReturnsBadRequest()
    {
        // Arrange
        var dto = new CreateFunkoDto("Invalid", -10m, 5, "DC", null);
        var error = FunkoError.InvalidData("Precio", "debe ser mayor a 0");

        _serviceMock
            .Setup(s => s.CreateAsync(dto))
            .ReturnsAsync(Result.Failure<Funko, FunkoError>(error));

        // Act
        var result = await _controller.Create(dto);

        // Assert
        var badRequestResult = result.Result. Should().BeOfType<BadRequestObjectResult>().Subject;
        var problemDetails = badRequestResult.Value.Should().BeOfType<ProblemDetails>().Subject;
        problemDetails.Status.Should().Be(400);

        _serviceMock.Verify(s => s.CreateAsync(dto), Times.Once);
    }

    [Test]
    public async Task Delete_WithValidId_ReturnsNoContent()
    {
        // Arrange
        var funkoId = 1;

        _serviceMock
            . Setup(s => s.DeleteAsync(funkoId))
            .ReturnsAsync(Result.Success<Unit, FunkoError>(Unit.Value));

        // Act
        var result = await _controller. Delete(funkoId);

        // Assert
        result.Should().BeOfType<NoContentResult>();

        _serviceMock.Verify(s => s.DeleteAsync(funkoId), Times.Once);
    }
}
```

---

### Test de Integración HTTP

Ya lo vimos anteriormente con `WebApplicationFactory`.

---

## Cobertura de Código

**Instalar herramienta de cobertura:**

```bash
dotnet add package coverlet.collector
```

**Ejecutar tests con cobertura:**

```bash
dotnet test /p:CollectCoverage=true /p:CoverletOutputFormat=opencover
```

**Generar reporte HTML con ReportGenerator:**

```bash
dotnet tool install -g dotnet-reportgenerator-globaltool

reportgenerator -reports:coverage. opencover.xml -targetdir:coveragereport -reporttypes:Html
```

**En Rider:**

1. Click derecho en el proyecto de tests → **Cover Unit Tests**
2. Ver reporte de cobertura en la ventana **Coverage**

---

## Práctica de clase:   Testing Completo

**Objetivo:** Crear una suite completa de tests para la API de Funkos. 

**Tareas obligatorias:**

1. ✅ **Testear el Repositorio**: 
   - Todos los métodos CRUD
   - Casos exitosos y casos de error

2. ✅ **Testear el Mapeador**:
   - Conversión de modelo a DTO
   - Conversión de DTO a modelo

3. ✅ **Testear el Servicio** (con Moq):
   - Mockear el repositorio y la caché
   - Probar todos los métodos
   - Validar que devuelven `Result<T, FunkoError>` correctamente
   - Casos de éxito y error
   - Verificar llamadas al repositorio

4. ✅ **Testear el Controlador** (con Moq):
   - Mockear el servicio
   - Probar todos los endpoints
   - Verificar códigos HTTP correctos (200, 201, 204, 400, 404, 409)
   - Validar DTOs (restricciones de Data Annotations / FluentValidation)
   - Verificar respuestas con `ProblemDetails`

5. ✅ **Test de Integración** (con `WebApplicationFactory`):
   - Probar flujo completo de cada endpoint
   - Verificar respuestas HTTP reales

6. ✅ **Cobertura de código**:
   - Objetivo: >80% de cobertura

**Criterios de evaluación:**

- ✅ Tests bien estructurados (Arrange-Act-Assert)
- ✅ Uso correcto de NUnit y Moq
- ✅ Uso de FluentAssertions para aserciones claras
- ✅ Tests independientes (no dependen unos de otros)
- ✅ Nombres descriptivos de tests
- ✅ Cobertura de casos exitosos y de error
- ✅ Tests de integración funcionan correctamente
- ✅ Todos los tests pasan ✅

---

## Proyecto del curso

**Estructura del proyecto:**

```
FunkosApi. sln
├── FunkosApi/
│   ├── Controllers/
│   ├── Services/
│   ├── Repositories/
│   ├── Models/
│   ├── DTOs/
│   ├── Mappers/
│   ├── Validators/
│   ├── Exceptions/
│   └── Program.cs
└── FunkosApi.Tests/
    ├── Controllers/
    │   ├── FunkosControllerUnitTests.cs
    │   └── FunkosControllerIntegrationTests.cs
    ├── Services/
    │   └── FunkoServiceTests.cs
    ├── Repositories/
    │   └── FunkoRepositoryTests.cs
    ├── Mappers/
    │   └── FunkoMapperTests. cs
    └── IntegrationTestBase.cs
```

---

