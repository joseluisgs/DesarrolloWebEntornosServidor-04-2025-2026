# Testing en . NET

---

- [Testing en . NET](#testing-en--net)
  - [Test unitarios con NUnit](#test-unitarios-con-nunit)
  - [Test con dobles usando Moq](#test-con-dobles-usando-moq)
  - [FluentAssertions para aserciones expresivas](#fluentassertions-para-aserciones-expresivas)
  - [Test de integración con Testcontainers](#test-de-integración-con-testcontainers)

---

## Test unitarios con NUnit

Testear y probar es algo que debemos acostumbrarnos a hacer siempre. El código no se debería escribir sin testearlo.  Para ello usaremos **NUnit**, que es un framework de testing para .NET para la realización de test unitarios, y **Moq** para test con dobles.

Un **test unitario** es una forma de comprobar el correcto funcionamiento de una unidad individual de código fuente. Esta "unidad" puede ser una función, un método, una clase, un módulo, etc.  Los tests unitarios son una parte fundamental de la metodología de desarrollo conocida como **Test-Driven Development (TDD)**.

**Instalación:**

```bash
dotnet add package NUnit
dotnet add package NUnit3TestAdapter
dotnet add package Microsoft.NET.Test.Sdk
```

**Ejemplo básico de test unitario con NUnit:**

```csharp
using NUnit. Framework;

namespace MiAplicacion.Tests;

public class Calculadora
{
    public int Sumar(int a, int b) => a + b;
    public int Restar(int a, int b) => a - b;
    public int Multiplicar(int a, int b) => a * b;
    
    public int Dividir(int a, int b)
    {
        if (b == 0)
            throw new OperacionNoValidaException("No se puede dividir por cero");
        return a / b;
    }
}

public class OperacionNoValidaException : Exception
{
    public OperacionNoValidaException(string mensaje) : base(mensaje) { }
}

[TestFixture]
public class CalculadoraTests
{
    private Calculadora _calculadora = null!;

    // Se ejecuta antes de cada test
    [SetUp]
    public void SetUp()
    {
        _calculadora = new Calculadora();
    }

    // Se ejecuta después de cada test
    [TearDown]
    public void TearDown()
    {
        // Limpiar recursos si es necesario
    }

    [Test]
    public void Sumar_DosNumeros_DebeRetornarLaSuma()
    {
        // Arrange
        int a = 2;
        int b = 3;
        int esperado = 5;

        // Act
        int resultado = _calculadora.Sumar(a, b);

        // Assert
        Assert.That(resultado, Is.EqualTo(esperado));
    }

    [Test]
    public void Sumar_VariosEscenarios_DebeRetornarResultadoCorrecto()
    {
        // Assert. Multiple permite agrupar múltiples aserciones
        Assert.Multiple(() =>
        {
            Assert.That(_calculadora. Sumar(2, 3), Is.EqualTo(5), "2 + 3 = 5");
            Assert.That(_calculadora.Sumar(3, 2), Is.EqualTo(5), "3 + 2 = 5");
            Assert.That(_calculadora.Sumar(0, 0), Is.EqualTo(0), "0 + 0 = 0");
            Assert.That(_calculadora.Sumar(2, -3), Is.EqualTo(-1), "2 + (-3) = -1");
        });
    }

    // TestCase permite ejecutar el mismo test con diferentes parámetros
    [TestCase(6, 3, 2)]
    [TestCase(10, 5, 2)]
    [TestCase(-6, 3, -2)]
    [TestCase(0, 5, 0)]
    public void Dividir_VariosEscenarios_DebeRetornarResultadoCorrecto(int a, int b, int esperado)
    {
        // Act
        int resultado = _calculadora.Dividir(a, b);

        // Assert
        Assert.That(resultado, Is.EqualTo(esperado));
    }

    [Test]
    public void Dividir_PorCero_DebeLanzarExcepcion()
    {
        // Act & Assert
        var ex = Assert.Throws<OperacionNoValidaException>(() => _calculadora.Dividir(2, 0));
        
        Assert.That(ex.Message, Is.EqualTo("No se puede dividir por cero"));
    }

    [Test]
    [Category("Operaciones Básicas")]
    public void Multiplicar_DosNumeros_DebeRetornarElProducto()
    {
        // Arrange
        int a = 4;
        int b = 5;

        // Act
        int resultado = _calculadora.Multiplicar(a, b);

        // Assert
        Assert.That(resultado, Is.EqualTo(20));
    }
}
```

**Aserciones comunes en NUnit:**

```csharp
[TestFixture]
public class EjemplosAserciones
{
    [Test]
    public void Aserciones_Igualdad()
    {
        int valor = 42;
        
        Assert.That(valor, Is.EqualTo(42));
        Assert.That(valor, Is. Not.EqualTo(100));
    }

    [Test]
    public void Aserciones_Comparacion()
    {
        int valor = 10;
        
        Assert.That(valor, Is.GreaterThan(5));
        Assert.That(valor, Is.LessThan(20));
        Assert.That(valor, Is.GreaterThanOrEqualTo(10));
        Assert.That(valor, Is.InRange(5, 15));
    }

    [Test]
    public void Aserciones_Strings()
    {
        string texto = "Hola Mundo";
        
        Assert. That(texto, Is.EqualTo("Hola Mundo"));
        Assert.That(texto, Does.Contain("Mundo"));
        Assert.That(texto, Does.StartWith("Hola"));
        Assert.That(texto, Does. EndWith("Mundo"));
        Assert.That(texto, Is.Not.Empty);
    }

    [Test]
    public void Aserciones_Colecciones()
    {
        var lista = new List<int> { 1, 2, 3, 4, 5 };
        
        Assert.That(lista, Has.Count.EqualTo(5));
        Assert.That(lista, Does.Contain(3));
        Assert.That(lista, Is. Ordered);
        Assert.That(lista, Is. All. GreaterThan(0));
        Assert.That(lista, Is. Unique);
    }

    [Test]
    public void Aserciones_Null()
    {
        string?  textoNulo = null;
        string textoNoNulo = "Valor";
        
        Assert.That(textoNulo, Is. Null);
        Assert.That(textoNoNulo, Is.Not.Null);
    }

    [Test]
    public void Aserciones_Tipos()
    {
        object objeto = "texto";
        
        Assert.That(objeto, Is.TypeOf<string>());
        Assert.That(objeto, Is. InstanceOf<string>());
    }
}
```

---

## Test con dobles usando Moq

Los **"dobles de prueba"** (test doubles) son sustitutos de los componentes del sistema que tu código está diseñado para interactuar. Los dobles de prueba son útiles cuando los componentes reales son difíciles o imposibles de incorporar en un test unitario. 

Un **mock** es un tipo de doble de prueba que puede preprogramarse para actuar de cierta manera durante un test. **Moq** es el framework más popular para crear mocks en .NET.

**Instalación:**

```bash
dotnet add package Moq
```

**Ejemplo con mocks:**

```csharp
using Moq;
using NUnit.Framework;

namespace MiAplicacion.Tests;

// Interfaz del servicio dependiente
public interface ICalculadora
{
    int Sumar(int a, int b);
    int Restar(int a, int b);
    int Multiplicar(int a, int b);
    int Dividir(int a, int b);
}

// Servicio que usa la calculadora
public class ServicioMatematico
{
    private readonly ICalculadora _calculadora;

    public ServicioMatematico(ICalculadora calculadora)
    {
        _calculadora = calculadora;
    }

    public int DobleSuma(int a, int b)
    {
        return _calculadora.Sumar(a, b) * 2;
    }

    public int DobleResta(int a, int b)
    {
        return _calculadora.Restar(a, b) * 2;
    }

    public int OperacionCompleja(int a, int b, int c)
    {
        var suma = _calculadora.Sumar(a, b);
        var multiplicacion = _calculadora.Multiplicar(suma, c);
        return multiplicacion;
    }
}

[TestFixture]
public class ServicioMatematicoTests
{
    private Mock<ICalculadora> _calculadoraMock = null!;
    private ServicioMatematico _servicio = null!;

    [SetUp]
    public void SetUp()
    {
        // Crear el mock
        _calculadoraMock = new Mock<ICalculadora>();
        
        // Inyectar el mock en el servicio
        _servicio = new ServicioMatematico(_calculadoraMock.Object);
    }

    [Test]
    public void DobleSuma_DebeRetornarElDobleDelResultado()
    {
        // Arrange
        // Configurar el comportamiento del mock
        _calculadoraMock
            .Setup(c => c. Sumar(2, 3))
            .Returns(5);

        // Act
        int resultado = _servicio.DobleSuma(2, 3);

        // Assert
        Assert.That(resultado, Is.EqualTo(10));

        // Verificar que se llamó al método del mock
        _calculadoraMock.Verify(c => c. Sumar(2, 3), Times.Once);
    }

    [Test]
    public void DobleSuma_ConCualquierParametro_DebeRetornarElDoble()
    {
        // Arrange
        // Usar It.IsAny<T>() para cualquier valor
        _calculadoraMock
            .Setup(c => c. Sumar(It.IsAny<int>(), It.IsAny<int>()))
            .Returns(10);

        // Act
        int resultado = _servicio.DobleSuma(5, 7);

        // Assert
        Assert.That(resultado, Is.EqualTo(20));

        // Verificar que se llamó con cualquier parámetro
        _calculadoraMock.Verify(
            c => c.Sumar(It.IsAny<int>(), It.IsAny<int>()), 
            Times. Once
        );
    }

    [Test]
    public void DobleSuma_ConCondicion_DebeRetornarResultadoCondicional()
    {
        // Arrange
        // Usar It.Is<T>() para condiciones específicas
        _calculadoraMock
            .Setup(c => c.Sumar(It.Is<int>(x => x > 0), It.Is<int>(x => x > 0)))
            .Returns(15);

        // Act
        int resultado = _servicio.DobleSuma(3, 4);

        // Assert
        Assert.That(resultado, Is.EqualTo(30));
    }

    [Test]
    public void OperacionCompleja_DebeRealizarMultiplesLlamadasAlMock()
    {
        // Arrange
        _calculadoraMock. Setup(c => c.Sumar(2, 3)).Returns(5);
        _calculadoraMock.Setup(c => c.Multiplicar(5, 4)).Returns(20);

        // Act
        int resultado = _servicio.OperacionCompleja(2, 3, 4);

        // Assert
        Assert. That(resultado, Is.EqualTo(20));

        // Verificar el orden y número de llamadas
        _calculadoraMock.Verify(c => c.Sumar(2, 3), Times.Once);
        _calculadoraMock. Verify(c => c.Multiplicar(5, 4), Times.Once);
    }

    [Test]
    public void DividirPorCero_DebeLanzarExcepcion()
    {
        // Arrange
        // Configurar el mock para lanzar una excepción
        _calculadoraMock
            . Setup(c => c.Dividir(It.IsAny<int>(), 0))
            .Throws<OperacionNoValidaException>();

        // Act & Assert
        Assert.Throws<OperacionNoValidaException>(() => 
        {
            _calculadoraMock.Object.Dividir(10, 0);
        });
    }

    [Test]
    public void Callback_DebeEjecutarseAlLlamarAlMetodo()
    {
        // Arrange
        int llamadas = 0;

        _calculadoraMock
            .Setup(c => c. Sumar(It.IsAny<int>(), It.IsAny<int>()))
            .Returns(10)
            .Callback(() => llamadas++);

        // Act
        _servicio.DobleSuma(2, 3);
        _servicio.DobleSuma(4, 5);

        // Assert
        Assert.That(llamadas, Is.EqualTo(2));
    }
}
```

**Verificaciones avanzadas con Moq:**

```csharp
[TestFixture]
public class VerificacionesAvanzadas
{
    [Test]
    public void VerificarNumeroExactoLlamadas()
    {
        var mock = new Mock<ICalculadora>();
        mock.Setup(c => c.Sumar(It.IsAny<int>(), It.IsAny<int>())).Returns(10);

        var servicio = new ServicioMatematico(mock.Object);
        servicio.DobleSuma(1, 2);
        servicio.DobleSuma(3, 4);

        // Verificar exactamente 2 llamadas
        mock.Verify(c => c.Sumar(It.IsAny<int>(), It.IsAny<int>()), Times.Exactly(2));
    }

    [Test]
    public void VerificarQueNuncaSeLlamo()
    {
        var mock = new Mock<ICalculadora>();

        // Verificar que nunca se llamó
        mock.Verify(c => c.Dividir(It.IsAny<int>(), It.IsAny<int>()), Times.Never);
    }

    [Test]
    public void VerificarAlMenosUnaLlamada()
    {
        var mock = new Mock<ICalculadora>();
        mock.Setup(c => c.Sumar(It.IsAny<int>(), It.IsAny<int>())).Returns(10);

        var servicio = new ServicioMatematico(mock.Object);
        servicio.DobleSuma(1, 2);

        // Verificar al menos una llamada
        mock. Verify(c => c. Sumar(It.IsAny<int>(), It.IsAny<int>()), Times.AtLeastOnce);
    }

    [Test]
    public void CapturarArgumentosDeLlamada()
    {
        var mock = new Mock<ICalculadora>();
        int valorCapturado = 0;

        mock.Setup(c => c. Sumar(It.IsAny<int>(), It.IsAny<int>()))
            .Returns(10)
            .Callback<int, int>((a, b) => valorCapturado = a + b);

        var servicio = new ServicioMatematico(mock.Object);
        servicio.DobleSuma(3, 4);

        Assert.That(valorCapturado, Is.EqualTo(7));
    }
}
```

---

## FluentAssertions para aserciones expresivas

**FluentAssertions** proporciona una sintaxis más legible y expresiva para las aserciones. 

**Instalación:**

```bash
dotnet add package FluentAssertions
```

**Ejemplo:**

```csharp
using FluentAssertions;
using NUnit.Framework;

[TestFixture]
public class EjemplosFluentAssertions
{
    [Test]
    public void FluentAssertions_Numeros()
    {
        int valor = 42;

        valor.Should().Be(42);
        valor.Should().BeGreaterThan(40);
        valor.Should().BeLessThan(50);
        valor.Should().BeInRange(40, 50);
    }

    [Test]
    public void FluentAssertions_Strings()
    {
        string texto = "Hola Mundo";

        texto.Should().Be("Hola Mundo");
        texto.Should().Contain("Mundo");
        texto.Should().StartWith("Hola");
        texto.Should().EndWith("Mundo");
        texto.Should().NotBeNullOrEmpty();
    }

    [Test]
    public void FluentAssertions_Colecciones()
    {
        var lista = new List<int> { 1, 2, 3, 4, 5 };

        lista.Should().HaveCount(5);
        lista.Should().Contain(3);
        lista.Should().BeInAscendingOrder();
        lista.Should().OnlyHaveUniqueItems();
        lista.Should().AllSatisfy(x => x. Should().BePositive());
    }

    [Test]
    public void FluentAssertions_Objetos()
    {
        var producto = new Producto { Id = Guid.NewGuid(), Nombre = "Laptop", Precio = 1200m };

        producto.Should().NotBeNull();
        producto.Nombre.Should().Be("Laptop");
        producto.Precio. Should().BeGreaterThan(1000m);

        // Verificar múltiples propiedades
        producto.Should().Match<Producto>(p =>
            p. Nombre == "Laptop" &&
            p.Precio == 1200m
        );
    }

    [Test]
    public void FluentAssertions_Excepciones()
    {
        var calculadora = new Calculadora();

        Action accion = () => calculadora.Dividir(10, 0);

        accion.Should().Throw<OperacionNoValidaException>()
            .WithMessage("No se puede dividir por cero");
    }
}
```

---

## Test de integración con Testcontainers

Los **tests de integración** verifican que múltiples componentes funcionen correctamente juntos, incluyendo bases de datos reales. 

**Ejemplo:**

```csharp
using NUnit.Framework;
using Testcontainers.PostgreSql;
using Microsoft.EntityFrameworkCore;
using FluentAssertions;

[TestFixture]
public class ProductoRepositoryIntegrationTests
{
    private PostgreSqlContainer _postgresContainer = null!;
    private ApplicationDbContext _context = null!;
    private ProductoRepository _repository = null!;

    [OneTimeSetUp]
    public async Task OneTimeSetUp()
    {
        _postgresContainer = new PostgreSqlBuilder()
            .WithImage("postgres:16-alpine")
            .WithDatabase("testdb")
            .WithUsername("testuser")
            .WithPassword("testpass")
            .Build();

        await _postgresContainer.StartAsync();
    }

    [SetUp]
    public async Task SetUp()
    {
        var options = new DbContextOptionsBuilder<ApplicationDbContext>()
            .UseNpgsql(_postgresContainer.GetConnectionString())
            .Options;

        _context = new ApplicationDbContext(options);
        await _context.Database.EnsureCreatedAsync();

        _repository = new ProductoRepository(_context);
    }

    [TearDown]
    public async Task TearDown()
    {
        await _context.Database.EnsureDeletedAsync();
        await _context.DisposeAsync();
    }

    [OneTimeTearDown]
    public async Task OneTimeTearDown()
    {
        await _postgresContainer.StopAsync();
        await _postgresContainer.DisposeAsync();
    }

    [Test]
    public async Task AddAsync_NuevoProducto_DebeGuardarEnBaseDeDatos()
    {
        // Arrange
        var producto = new Producto
        {
            Nombre = "Laptop Test",
            Precio = 1200m,
            Cantidad = 10,
            Categoria = "Electrónica"
        };

        // Act
        await _repository.AddAsync(producto);

        // Assert
        var productoGuardado = await _repository.GetByIdAsync(producto.Id);
        
        productoGuardado.Should().NotBeNull();
        productoGuardado! .Nombre.Should().Be("Laptop Test");
        productoGuardado. Precio.Should().Be(1200m);
    }

    [Test]
    public async Task GetAllAsync_DebeDevolverTodosLosProductos()
    {
        // Arrange
        var productos = new[]
        {
            new Producto { Nombre = "Laptop", Precio = 1200m, Cantidad = 5, Categoria = "Electrónica" },
            new Producto { Nombre = "Mouse", Precio = 25m, Cantidad = 50, Categoria = "Accesorios" }
        };

        foreach (var p in productos)
        {
            await _repository.AddAsync(p);
        }

        // Act
        var resultado = await _repository.GetAllAsync();

        // Assert
        resultado.Should().HaveCount(2);
        resultado.Should().Contain(p => p.Nombre == "Laptop");
    }
}
```

---

