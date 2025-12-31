# Almacenamiento de Ficheros en ASP.NET Core

- [Almacenamiento de Ficheros en ASP.NET Core](#almacenamiento-de-ficheros-en-aspnet-core)
  - [Introducción](#introducción)
  - [Configuración Inicial](#configuración-inicial)
    - [Configurar el Directorio de Almacenamiento](#configurar-el-directorio-de-almacenamiento)
    - [Limitar Tamaño de Archivos](#limitar-tamaño-de-archivos)
  - [Servicio de Almacenamiento](#servicio-de-almacenamiento)
    - [Interfaz IStorageService](#interfaz-istorageservice)
    - [Implementación FileSystemStorageService](#implementación-filesystemstorageservice)
    - [Excepciones Personalizadas](#excepciones-personalizadas)
  - [Controlador de Archivos](#controlador-de-archivos)
    - [Subir Archivo](#subir-archivo)
    - [Descargar/Servir Archivo](#descargarservir-archivo)
    - [Eliminar Archivo](#eliminar-archivo)
  - [Integración con Entidades](#integración-con-entidades)
    - [Modelo con Campo de Imagen](#modelo-con-campo-de-imagen)
    - [Endpoint para Actualizar Imagen](#endpoint-para-actualizar-imagen)
    - [Consideraciones Importantes](#consideraciones-importantes)
  - [Validaciones de Archivos](#validaciones-de-archivos)
    - [Validar Tipo de Archivo](#validar-tipo-de-archivo)
    - [Validar Tamaño](#validar-tamaño)
    - [Atributo de Validación Personalizado](#atributo-de-validación-personalizado)
  - [Configuración de Inicio](#configuración-de-inicio)
    - [Inicializar Storage al Arrancar](#inicializar-storage-al-arrancar)
  - [Testing del Servicio de Almacenamiento](#testing-del-servicio-de-almacenamiento)
    - [Test del Servicio](#test-del-servicio)
    - [Test del Controlador](#test-del-controlador)
  - [Almacenamiento en la Nube (Azure Blob Storage)](#almacenamiento-en-la-nube-azure-blob-storage)
    - [Configuración](#configuración)
    - [Implementación BlobStorageService](#implementación-blobstorageservice)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![Storage Banner](../images/banner08.jpg)

---

## Introducción

El **almacenamiento de archivos** es una funcionalidad común en aplicaciones web modernas.    En ASP.NET Core, podemos implementar un servicio de almacenamiento flexible que: 

✅ Guarde archivos en el sistema de archivos local
✅ Genere URLs accesibles para descargar archivos
✅ Valide tipos y tamaños de archivos
✅ Se pueda extender para usar almacenamiento en la nube (Azure, AWS, etc.)

---

## Configuración Inicial

### Configurar el Directorio de Almacenamiento

**appsettings.json:**

```json
{
  "Storage": {
    "RootPath": "uploads",
    "DeleteOnStartup": false,
    "MaxFileSize":  5242880,
    "AllowedExtensions": [".jpg", ".jpeg", ".png", ".gif", ".webp"]
  }
}
```

**appsettings.Development.json:**

```json
{
  "Storage": {
    "DeleteOnStartup": true
  }
}
```

**Clase de configuración:**

```csharp
namespace FunkosApi.Configuration;

public class StorageSettings
{
    public string RootPath { get; set; } = "uploads";
    public bool DeleteOnStartup { get; set; }
    public long MaxFileSize { get; set; } = 5 * 1024 * 1024; // 5 MB
    public string[] AllowedExtensions { get; set; } = { ".jpg", ".jpeg", ".png", ". gif", ".webp" };
}
```

**Registrar en Program.cs:**

```csharp
builder.Services.Configure<StorageSettings>(
    builder.Configuration.GetSection("Storage")
);
```

---

### Limitar Tamaño de Archivos

Por defecto, ASP.NET Core limita el tamaño de las peticiones.    Puedes configurarlo globalmente: 

```csharp
builder.Services.Configure<FormOptions>(options =>
{
    options.MultipartBodyLengthLimit = 10 * 1024 * 1024; // 10 MB
});

builder.WebHost.ConfigureKestrel(options =>
{
    options. Limits.MaxRequestBodySize = 10 * 1024 * 1024; // 10 MB
});
```

---

## Servicio de Almacenamiento

### Interfaz IStorageService

```csharp
namespace FunkosApi.Services. Storage;

public interface IStorageService
{
    /// <summary>
    /// Inicializa el almacenamiento (crea directorios, etc.)
    /// </summary>
    Task InitAsync();

    /// <summary>
    /// Almacena un archivo y devuelve el nombre generado
    /// </summary>
    Task<string> StoreAsync(IFormFile file, CancellationToken cancellationToken = default);

    /// <summary>
    /// Carga un archivo como stream
    /// </summary>
    Task<Stream> LoadAsStreamAsync(string filename, CancellationToken cancellationToken = default);

    /// <summary>
    /// Obtiene la ruta completa del archivo
    /// </summary>
    string GetFilePath(string filename);

    /// <summary>
    /// Obtiene la URL pública del archivo
    /// </summary>
    string GetUrl(string filename);

    /// <summary>
    /// Elimina un archivo
    /// </summary>
    Task<bool> DeleteAsync(string filename, CancellationToken cancellationToken = default);

    /// <summary>
    /// Elimina todos los archivos
    /// </summary>
    Task DeleteAllAsync(CancellationToken cancellationToken = default);

    /// <summary>
    /// Verifica si un archivo existe
    /// </summary>
    bool Exists(string filename);
}
```

---

### Implementación FileSystemStorageService

```csharp
using Microsoft.Extensions.Options;
using Microsoft.AspNetCore.Mvc;

namespace FunkosApi.Services. Storage;

public class FileSystemStorageService : IStorageService
{
    private readonly StorageSettings _settings;
    private readonly IWebHostEnvironment _environment;
    private readonly ILogger<FileSystemStorageService> _logger;
    private readonly string _rootPath;

    public FileSystemStorageService(
        IOptions<StorageSettings> settings,
        IWebHostEnvironment environment,
        ILogger<FileSystemStorageService> logger)
    {
        _settings = settings. Value;
        _environment = environment;
        _logger = logger;
        _rootPath = Path.Combine(_environment.ContentRootPath, _settings.RootPath);
    }

    public Task InitAsync()
    {
        try
        {
            if (!Directory.Exists(_rootPath))
            {
                Directory.CreateDirectory(_rootPath);
                _logger.LogInformation("Directorio de almacenamiento creado: {Path}", _rootPath);
            }

            return Task.CompletedTask;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al inicializar el almacenamiento");
            throw new StorageException("No se pudo inicializar el almacenamiento", ex);
        }
    }

    public async Task<string> StoreAsync(IFormFile file, CancellationToken cancellationToken = default)
    {
        if (file is null || file.Length == 0)
            throw new StorageException("El archivo está vacío");

        // Validar extensión
        var extension = Path.GetExtension(file.FileName).ToLowerInvariant();
        if (!_settings.AllowedExtensions.Contains(extension))
            throw new StorageException($"Extensión no permitida: {extension}");

        // Validar tamaño
        if (file. Length > _settings.MaxFileSize)
            throw new StorageException($"El archivo excede el tamaño máximo permitido ({_settings.MaxFileSize / 1024 / 1024} MB)");

        // Generar nombre único
        var filename = $"{Guid.NewGuid()}{extension}";
        var filePath = Path.Combine(_rootPath, filename);

        try
        {
            await using var stream = new FileStream(filePath, FileMode.Create);
            await file.CopyToAsync(stream, cancellationToken);

            _logger.LogInformation("Archivo almacenado:  {Filename}", filename);
            return filename;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al almacenar el archivo");
            throw new StorageException("No se pudo almacenar el archivo", ex);
        }
    }

    public Task<Stream> LoadAsStreamAsync(string filename, CancellationToken cancellationToken = default)
    {
        var filePath = GetFilePath(filename);

        if (!File.Exists(filePath))
            throw new StorageFileNotFoundException($"Archivo no encontrado: {filename}");

        try
        {
            var stream = new FileStream(filePath, FileMode.Open, FileAccess. Read, FileShare.Read);
            return Task.FromResult<Stream>(stream);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al cargar el archivo:  {Filename}", filename);
            throw new StorageException($"No se pudo cargar el archivo: {filename}", ex);
        }
    }

    public string GetFilePath(string filename)
    {
        if (string.IsNullOrWhiteSpace(filename))
            throw new ArgumentException("El nombre del archivo no puede estar vacío", nameof(filename));

        // Prevenir path traversal attacks
        var sanitizedFilename = Path.GetFileName(filename);
        return Path.Combine(_rootPath, sanitizedFilename);
    }

    public string GetUrl(string filename)
    {
        // Genera URL relativa para acceder al archivo
        return $"/api/files/{filename}";
    }

    public Task<bool> DeleteAsync(string filename, CancellationToken cancellationToken = default)
    {
        var filePath = GetFilePath(filename);

        if (!File.Exists(filePath))
        {
            _logger.LogWarning("Intento de eliminar archivo inexistente: {Filename}", filename);
            return Task.FromResult(false);
        }

        try
        {
            File.Delete(filePath);
            _logger.LogInformation("Archivo eliminado: {Filename}", filename);
            return Task.FromResult(true);
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al eliminar el archivo: {Filename}", filename);
            throw new StorageException($"No se pudo eliminar el archivo: {filename}", ex);
        }
    }

    public Task DeleteAllAsync(CancellationToken cancellationToken = default)
    {
        try
        {
            if (Directory.Exists(_rootPath))
            {
                Directory.Delete(_rootPath, recursive: true);
                _logger. LogWarning("Todos los archivos eliminados del almacenamiento");
            }

            return Task.CompletedTask;
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al eliminar todos los archivos");
            throw new StorageException("No se pudo eliminar todos los archivos", ex);
        }
    }

    public bool Exists(string filename)
    {
        var filePath = GetFilePath(filename);
        return File.Exists(filePath);
    }
}
```

---

### Excepciones Personalizadas

```csharp
namespace FunkosApi. Exceptions;

public class StorageException : Exception
{
    public StorageException(string message) : base(message) { }
    public StorageException(string message, Exception innerException) : base(message, innerException) { }
}

public class StorageFileNotFoundException : StorageException
{
    public StorageFileNotFoundException(string message) : base(message) { }
}
```

**Registrar en Program.cs:**

```csharp
builder.Services. AddScoped<IStorageService, FileSystemStorageService>();
```

---

## Controlador de Archivos

### Subir Archivo

```csharp
using Microsoft.AspNetCore. Mvc;

namespace FunkosApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class FilesController : ControllerBase
{
    private readonly IStorageService _storageService;
    private readonly ILogger<FilesController> _logger;

    public FilesController(IStorageService storageService, ILogger<FilesController> logger)
    {
        _storageService = storageService;
        _logger = logger;
    }

    /// <summary>
    /// Sube un archivo
    /// </summary>
    [HttpPost]
    [ProducesResponseType(typeof(FileUploadResponse), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<ActionResult<FileUploadResponse>> UploadFile(IFormFile file)
    {
        if (file is null || file.Length == 0)
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Archivo inválido",
                Detail = "El archivo está vacío o no se proporcionó"
            });

        try
        {
            var filename = await _storageService.StoreAsync(file);
            var url = _storageService.GetUrl(filename);

            var response = new FileUploadResponse
            {
                Filename = filename,
                Url = url,
                Size = file.Length,
                ContentType = file.ContentType
            };

            return CreatedAtAction(
                nameof(ServeFile),
                new { filename },
                response
            );
        }
        catch (StorageException ex)
        {
            _logger.LogError(ex, "Error al subir el archivo");
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Error al subir el archivo",
                Detail = ex.Message
            });
        }
    }
}

public record FileUploadResponse
{
    public string Filename { get; init; } = string.Empty;
    public string Url { get.  init; } = string.Empty;
    public long Size { get.  init; }
    public string ContentType { get.  init; } = string.Empty;
}
```

---

### Descargar/Servir Archivo

```csharp
/// <summary>
/// Descarga un archivo
/// </summary>
[HttpGet("{filename}")]
[ProducesResponseType(StatusCodes.Status200OK)]
[ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
public async Task<IActionResult> ServeFile(string filename)
{
    try
    {
        var stream = await _storageService.LoadAsStreamAsync(filename);
        var contentType = GetContentType(filename);

        return File(stream, contentType, enableRangeProcessing: true);
    }
    catch (StorageFileNotFoundException)
    {
        return NotFound(new ProblemDetails
        {
            Status = StatusCodes.Status404NotFound,
            Title = "Archivo no encontrado",
            Detail = $"El archivo '{filename}' no existe"
        });
    }
}

private static string GetContentType(string filename)
{
    var extension = Path.GetExtension(filename).ToLowerInvariant();
    return extension switch
    {
        ".jpg" or ".jpeg" => "image/jpeg",
        ". png" => "image/png",
        ".gif" => "image/gif",
        ".webp" => "image/webp",
        ". svg" => "image/svg+xml",
        _ => "application/octet-stream"
    };
}
```

---

### Eliminar Archivo

```csharp
/// <summary>
/// Elimina un archivo
/// </summary>
[HttpDelete("{filename}")]
[ProducesResponseType(StatusCodes.Status204NoContent)]
[ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
public async Task<IActionResult> DeleteFile(string filename)
{
    var deleted = await _storageService.DeleteAsync(filename);

    if (!deleted)
        return NotFound(new ProblemDetails
        {
            Status = StatusCodes.Status404NotFound,
            Title = "Archivo no encontrado",
            Detail = $"El archivo '{filename}' no existe"
        });

    return NoContent();
}
```

---

## Integración con Entidades

### Modelo con Campo de Imagen

```csharp
public class Funko
{
    public int Id { get.  set; }
    public string Nombre { get. set; } = string.Empty;
    public decimal Precio { get. set; }
    public int Cantidad { get. set; }
    public string Categoria { get. set; } = string.Empty;

    // Campo para almacenar el nombre del archivo de imagen
    [StringLength(500)]
    public string?   Imagen { get. set; }

    // Propiedad calculada para obtener la URL completa
    [NotMapped]
    public string?   ImagenUrl => !string.IsNullOrEmpty(Imagen) ? $"/api/files/{Imagen}" : null;
}
```

---

### Endpoint para Actualizar Imagen

```csharp
[ApiController]
[Route("api/[controller]")]
public class FunkosController : ControllerBase
{
    private readonly IFunkoService _service;
    private readonly IStorageService _storageService;
    private readonly ILogger<FunkosController> _logger;

    public FunkosController(
        IFunkoService service,
        IStorageService storageService,
        ILogger<FunkosController> logger)
    {
        _service = service;
        _storageService = storageService;
        _logger = logger;
    }

    /// <summary>
    /// Actualiza la imagen de un funko
    /// </summary>
    [HttpPatch("{id: int}/imagen")]
    [Consumes("multipart/form-data")]
    [ProducesResponseType(typeof(FunkoResponseDto), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status404NotFound)]
    public async Task<ActionResult<FunkoResponseDto>> UpdateImage(int id, IFormFile file)
    {
        if (file is null || file.Length == 0)
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Archivo inválido",
                Detail = "No se proporcionó ningún archivo"
            });

        // Buscar el funko
        var result = await _service.GetByIdAsync(id);

        if (result. IsFailure)
            return NotFound(new ProblemDetails
            {
                Status = StatusCodes. Status404NotFound,
                Title = result.Error.Code,
                Detail = result.Error.Message
            });

        var funko = result.Value;

        try
        {
            // Eliminar imagen anterior si existe
            if (!string.IsNullOrEmpty(funko. Imagen))
            {
                await _storageService.DeleteAsync(funko.Imagen);
            }

            // Guardar nueva imagen
            var filename = await _storageService.StoreAsync(file);
            var url = _storageService.GetUrl(filename);

            // Actualizar funko
            var updateDto = new UpdateFunkoDto(
                funko. Nombre,
                funko. Precio,
                funko. Cantidad,
                funko. Categoria,
                filename // Guardar solo el nombre del archivo
            );

            var updateResult = await _service.UpdateAsync(id, updateDto);

            return updateResult.Match(
                onSuccess: updated => Ok(updated. ToResponseDto()),
                onFailure:  error => StatusCode(500, new ProblemDetails
                {
                    Status = StatusCodes. Status500InternalServerError,
                    Title = "Error al actualizar",
                    Detail = error.Message
                })
            );
        }
        catch (StorageException ex)
        {
            _logger.LogError(ex, "Error al actualizar la imagen del funko {Id}", id);
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Error al actualizar la imagen",
                Detail = ex. Message
            });
        }
    }
}
```

---

### Consideraciones Importantes

**1. Eliminar imagen al eliminar la entidad:**

```csharp
public async Task<Result<Unit, FunkoError>> DeleteAsync(int id)
{
    var funko = await _repository.GetByIdAsync(id);
    if (funko is null)
        return Result. Failure<Unit, FunkoError>(FunkoError.NotFound(id));

    // Eliminar imagen si existe
    if (!string.IsNullOrEmpty(funko. Imagen))
    {
        try
        {
            await _storageService.DeleteAsync(funko.Imagen);
        }
        catch (Exception ex)
        {
            _logger.LogWarning(ex, "No se pudo eliminar la imagen del funko {Id}", id);
            // Continuar con la eliminación del funko aunque falle la imagen
        }
    }

    var deleted = await _repository.DeleteAsync(id);
    return deleted
        ? Result.Success<Unit, FunkoError>(Unit.Value)
        : Result.Failure<Unit, FunkoError>(FunkoError.NotFound(id));
}
```

**2. Sobrescribir imagen en lugar de eliminar:**

Si prefieres sobrescribir la imagen, puedes guardar con el mismo nombre:

```csharp
// En StoreAsync, permitir sobrescribir
public async Task<string> StoreAsync(IFormFile file, string?   existingFilename = null, CancellationToken cancellationToken = default)
{
    // Validaciones... 

    var filename = existingFilename ?? $"{Guid.NewGuid()}{extension}";
    var filePath = Path.Combine(_rootPath, filename);

    // Sobrescribe si ya existe
    await using var stream = new FileStream(filePath, FileMode.Create);
    await file.CopyToAsync(stream, cancellationToken);

    return filename;
}
```

---

## Validaciones de Archivos

### Validar Tipo de Archivo

```csharp
public static class FileValidationExtensions
{
    private static readonly Dictionary<string, List<byte[]>> FileSignatures = new()
    {
        {
            ".jpg", new List<byte[]>
            {
                new byte[] { 0xFF, 0xD8, 0xFF, 0xE0 },
                new byte[] { 0xFF, 0xD8, 0xFF, 0xE1 },
                new byte[] { 0xFF, 0xD8, 0xFF, 0xE8 }
            }
        },
        {
            ".png", new List<byte[]>
            {
                new byte[] { 0x89, 0x50, 0x4E, 0x47, 0x0D, 0x0A, 0x1A, 0x0A }
            }
        },
        {
            ".gif", new List<byte[]>
            {
                new byte[] { 0x47, 0x49, 0x46, 0x38 }
            }
        }
    };

    public static bool IsValidImageFile(this IFormFile file)
    {
        if (file is null || file.Length == 0)
            return false;

        var extension = Path.GetExtension(file.FileName).ToLowerInvariant();

        if (!FileSignatures.ContainsKey(extension))
            return false;

        using var reader = new BinaryReader(file. OpenReadStream());
        var signatures = FileSignatures[extension];
        var headerBytes = reader.ReadBytes(signatures.Max(m => m.Length));

        return signatures.Any(signature =>
            headerBytes.Take(signature.Length).SequenceEqual(signature));
    }
}
```

---

### Validar Tamaño

```csharp
public static class FileSizeExtensions
{
    public static bool IsWithinSizeLimit(this IFormFile file, long maxSizeInBytes)
    {
        return file is not null && file.Length > 0 && file.Length <= maxSizeInBytes;
    }
}
```

---

### Atributo de Validación Personalizado

```csharp
using System.ComponentModel.DataAnnotations;

public class AllowedExtensionsAttribute : ValidationAttribute
{
    private readonly string[] _extensions;

    public AllowedExtensionsAttribute(params string[] extensions)
    {
        _extensions = extensions;
    }

    protected override ValidationResult?  IsValid(object?   value, ValidationContext validationContext)
    {
        if (value is not IFormFile file)
            return ValidationResult.Success;

        var extension = Path.GetExtension(file.FileName).ToLowerInvariant();

        if (!_extensions.Contains(extension))
        {
            return new ValidationResult(
                $"Extensión no permitida.  Extensiones permitidas: {string.Join(", ", _extensions)}"
            );
        }

        return ValidationResult.Success;
    }
}

public class MaxFileSizeAttribute : ValidationAttribute
{
    private readonly long _maxFileSize;

    public MaxFileSizeAttribute(long maxFileSize)
    {
        _maxFileSize = maxFileSize;
    }

    protected override ValidationResult?  IsValid(object?  value, ValidationContext validationContext)
    {
        if (value is not IFormFile file)
            return ValidationResult.Success;

        if (file.Length > _maxFileSize)
        {
            return new ValidationResult(
                $"El tamaño del archivo excede el límite de {_maxFileSize / 1024 / 1024} MB"
            );
        }

        return ValidationResult.Success;
    }
}

// Uso en DTO
public class UploadFileRequest
{
    [Required]
    [AllowedExtensions(". jpg", ".jpeg", ".png", ".gif")]
    [MaxFileSize(5 * 1024 * 1024)] // 5 MB
    public IFormFile File { get.  set; } = null!;
}
```

---

## Configuración de Inicio

### Inicializar Storage al Arrancar

```csharp
// Program.cs
var app = builder.Build();

// Inicializar almacenamiento
await InitializeStorageAsync(app. Services);

async Task InitializeStorageAsync(IServiceProvider services)
{
    using var scope = services.CreateScope();
    var storageService = scope.ServiceProvider.GetRequiredService<IStorageService>();
    var storageSettings = scope.ServiceProvider.GetRequiredService<IOptions<StorageSettings>>().Value;
    var logger = scope.ServiceProvider.GetRequiredService<ILogger<Program>>();

    // Eliminar archivos si está configurado
    if (storageSettings.DeleteOnStartup)
    {
        logger.LogWarning("Eliminando archivos del almacenamiento.. .");
        await storageService.DeleteAllAsync();
    }

    // Inicializar directorios
    await storageService.InitAsync();
    logger.LogInformation("Almacenamiento inicializado correctamente");
}

app.Run();
```

---

## Testing del Servicio de Almacenamiento

### Test del Servicio

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Options;
using Moq;
using NUnit.Framework;
using FluentAssertions;

[TestFixture]
public class FileSystemStorageServiceTests
{
    private FileSystemStorageService _service = null!;
    private string _testDirectory = null!;

    [SetUp]
    public void Setup()
    {
        _testDirectory = Path.Combine(Path. GetTempPath(), Guid.NewGuid().ToString());
        Directory.CreateDirectory(_testDirectory);

        var settings = Options.Create(new StorageSettings
        {
            RootPath = _testDirectory,
            MaxFileSize = 5 * 1024 * 1024,
            AllowedExtensions = new[] { ".jpg", ".png" }
        });

        var mockEnv = new Mock<IWebHostEnvironment>();
        mockEnv.Setup(e => e.ContentRootPath).Returns(Path.GetTempPath());

        var mockLogger = new Mock<ILogger<FileSystemStorageService>>();

        _service = new FileSystemStorageService(settings, mockEnv.Object, mockLogger.Object);
    }

    [TearDown]
    public void TearDown()
    {
        if (Directory.Exists(_testDirectory))
            Directory.Delete(_testDirectory, recursive: true);
    }

    [Test]
    public async Task InitAsync_CreatesDirectory()
    {
        // Arrange
        if (Directory.Exists(_testDirectory))
            Directory.Delete(_testDirectory);

        // Act
        await _service.InitAsync();

        // Assert
        Directory.Exists(_testDirectory).Should().BeTrue();
    }

    [Test]
    public async Task StoreAsync_WithValidFile_SavesFile()
    {
        // Arrange
        await _service.InitAsync();

        var content = "Test content"u8.ToArray();
        var stream = new MemoryStream(content);
        var file = new FormFile(stream, 0, content.Length, "file", "test.jpg")
        {
            Headers = new HeaderDictionary(),
            ContentType = "image/jpeg"
        };

        // Act
        var filename = await _service.StoreAsync(file);

        // Assert
        filename.Should().NotBeNullOrEmpty();
        _service. Exists(filename).Should().BeTrue();
    }

    [Test]
    public void StoreAsync_WithInvalidExtension_ThrowsException()
    {
        // Arrange
        var content = "Test content"u8.ToArray();
        var stream = new MemoryStream(content);
        var file = new FormFile(stream, 0, content. Length, "file", "test. txt")
        {
            Headers = new HeaderDictionary(),
            ContentType = "text/plain"
        };

        // Act & Assert
        Assert.ThrowsAsync<StorageException>(async () => await _service.StoreAsync(file));
    }

    [Test]
    public async Task DeleteAsync_WithExistingFile_DeletesFile()
    {
        // Arrange
        await _service.InitAsync();

        var content = "Test content"u8.ToArray();
        var stream = new MemoryStream(content);
        var file = new FormFile(stream, 0, content.Length, "file", "test.jpg");

        var filename = await _service.StoreAsync(file);

        // Act
        var deleted = await _service.DeleteAsync(filename);

        // Assert
        deleted.Should().BeTrue();
        _service.Exists(filename).Should().BeFalse();
    }
}
```

---

### Test del Controlador

```csharp
[TestFixture]
public class FilesControllerTests
{
    private Mock<IStorageService> _storageServiceMock = null!;
    private Mock<ILogger<FilesController>> _loggerMock = null!;
    private FilesController _controller = null!;

    [SetUp]
    public void Setup()
    {
        _storageServiceMock = new Mock<IStorageService>();
        _loggerMock = new Mock<ILogger<FilesController>>();
        _controller = new FilesController(_storageServiceMock.Object, _loggerMock.Object);
    }

    [Test]
    public async Task UploadFile_WithValidFile_ReturnsCreated()
    {
        // Arrange
        var content = "Test image content"u8.ToArray();
        var stream = new MemoryStream(content);
        var file = new FormFile(stream, 0, content.Length, "file", "test.jpg")
        {
            Headers = new HeaderDictionary(),
            ContentType = "image/jpeg"
        };

        var expectedFilename = "12345.jpg";
        var expectedUrl = $"/api/files/{expectedFilename}";

        _storageServiceMock
            .Setup(s => s. StoreAsync(file, default))
            .ReturnsAsync(expectedFilename);

        _storageServiceMock
            .Setup(s => s.GetUrl(expectedFilename))
            .Returns(expectedUrl);

        // Act
        var result = await _controller.UploadFile(file);

        // Assert
        var createdResult = result.Result. Should().BeOfType<CreatedAtActionResult>().Subject;
        var response = createdResult.Value. Should().BeOfType<FileUploadResponse>().Subject;

        response.Filename.Should().Be(expectedFilename);
        response.Url.Should().Be(expectedUrl);
        response.Size.Should().Be(content.Length);

        _storageServiceMock. Verify(s => s.StoreAsync(file, default), Times.Once);
    }

    [Test]
    public async Task UploadFile_WithEmptyFile_ReturnsBadRequest()
    {
        // Arrange
        IFormFile?   file = null;

        // Act
        var result = await _controller.UploadFile(file! );

        // Assert
        result.Result.Should().BeOfType<BadRequestObjectResult>();
    }
}
```

---

## Almacenamiento en la Nube (Azure Blob Storage)

### Configuración

```bash
dotnet add package Azure.Storage.Blobs
```

**appsettings.json:**

```json
{
  "AzureBlobStorage": {
    "ConnectionString": "DefaultEndpointsProtocol=https;AccountName=... ;AccountKey=...;EndpointSuffix=core.windows.net",
    "ContainerName": "funkos-images"
  }
}
```

---

### Implementación BlobStorageService

```csharp
using Azure.Storage.Blobs;
using Azure.Storage. Blobs.Models;

public class BlobStorageService :  IStorageService
{
    private readonly BlobServiceClient _blobServiceClient;
    private readonly string _containerName;
    private readonly ILogger<BlobStorageService> _logger;

    public BlobStorageService(
        IConfiguration configuration,
        ILogger<BlobStorageService> logger)
    {
        var connectionString = configuration["AzureBlobStorage:ConnectionString"]
            ?? throw new InvalidOperationException("Azure Blob Storage connection string not configured");

        _containerName = configuration["AzureBlobStorage:ContainerName"]
            ?? throw new InvalidOperationException("Azure Blob Storage container name not configured");

        _blobServiceClient = new BlobServiceClient(connectionString);
        _logger = logger;
    }

    public async Task InitAsync()
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        await containerClient.CreateIfNotExistsAsync(PublicAccessType. Blob);
        _logger.LogInformation("Contenedor de Azure Blob Storage inicializado:  {Container}", _containerName);
    }

    public async Task<string> StoreAsync(IFormFile file, CancellationToken cancellationToken = default)
    {
        if (file is null || file.Length == 0)
            throw new StorageException("El archivo está vacío");

        var extension = Path.GetExtension(file.FileName).ToLowerInvariant();
        var filename = $"{Guid.NewGuid()}{extension}";

        var containerClient = _blobServiceClient. GetBlobContainerClient(_containerName);
        var blobClient = containerClient.GetBlobClient(filename);

        await using var stream = file.OpenReadStream();
        await blobClient. UploadAsync(stream, new BlobHttpHeaders { ContentType = file.ContentType }, cancellationToken:  cancellationToken);

        _logger.LogInformation("Archivo subido a Azure Blob Storage: {Filename}", filename);
        return filename;
    }

    public async Task<Stream> LoadAsStreamAsync(string filename, CancellationToken cancellationToken = default)
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        var blobClient = containerClient.GetBlobClient(filename);

        if (!await blobClient.ExistsAsync(cancellationToken))
            throw new StorageFileNotFoundException($"Archivo no encontrado: {filename}");

        var response = await blobClient. DownloadAsync(cancellationToken);
        return response.Value. Content;
    }

    public string GetFilePath(string filename) => filename;

    public string GetUrl(string filename)
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        var blobClient = containerClient.GetBlobClient(filename);
        return blobClient.Uri.ToString();
    }

    public async Task<bool> DeleteAsync(string filename, CancellationToken cancellationToken = default)
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        var blobClient = containerClient.GetBlobClient(filename);

        var result = await blobClient.DeleteIfExistsAsync(cancellationToken:  cancellationToken);
        return result.Value;
    }

    public async Task DeleteAllAsync(CancellationToken cancellationToken = default)
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        await foreach (var blob in containerClient. GetBlobsAsync(cancellationToken:  cancellationToken))
        {
            await containerClient.DeleteBlobAsync(blob.Name, cancellationToken: cancellationToken);
        }

        _logger.LogWarning("Todos los archivos eliminados de Azure Blob Storage");
    }

    public bool Exists(string filename)
    {
        var containerClient = _blobServiceClient.GetBlobContainerClient(_containerName);
        var blobClient = containerClient.GetBlobClient(filename);
        return blobClient. Exists().Value;
    }
}
```

**Registrar en Program.cs:**

```csharp
// Elegir implementación según configuración
if (builder.Configuration.GetValue<bool>("UseAzureBlobStorage"))
{
    builder.Services.AddScoped<IStorageService, BlobStorageService>();
}
else
{
    builder.Services.AddScoped<IStorageService, FileSystemStorageService>();
}
```

---

## Buenas Prácticas

✅ **Validar archivos en el servidor**:  Nunca confíes solo en validaciones del cliente

✅ **Nombres únicos**:  Usa GUIDs para evitar colisiones

✅ **Limitar tamaño**:  Configura límites de tamaño de archivo

✅ **Validar tipos**:  Verifica extensión y firma del archivo (magic bytes)

✅ **Eliminar archivos huérfanos**:  Limpia archivos al eliminar entidades

✅ **Seguridad**:  Prevenir path traversal attacks sanitizando nombres

✅ **Logging**:  Registra operaciones de archivos

✅ **Flexibilidad**:  Diseña con interfaz para cambiar almacenamiento fácilmente

---

## Práctica de Clase

**Objetivo:** Implementar almacenamiento de archivos completo para Funkos.  

**Tareas:**

1. ✅ Implementar `IStorageService` y `FileSystemStorageService`
2. ✅ Crear controlador `FilesController` con endpoints de subida, descarga y eliminación
3. ✅ Añadir campo `Imagen` a la entidad `Funko`
4. ✅ Crear endpoint `PATCH /api/funkos/{id}/imagen` para actualizar imagen
5. ✅ Validar tipo y tamaño de archivos
6. ✅ Eliminar imagen al eliminar funko
7. ✅ Inicializar almacenamiento al arrancar la aplicación
8. ✅ Testear servicio y controlador

**Criterios de evaluación:**

- ✅ Servicio de almacenamiento funciona correctamente
- ✅ Validaciones de archivo implementadas
- ✅ Integración con entidad Funko
- ✅ Limpieza de archivos al eliminar
- ✅ Endpoints documentados con Swagger
- ✅ Tests completos (unitarios e integración)

---

## Proyecto del Curso

Puedes encontrar el proyecto con Entity Framework Core en el repositorio del curso.
- [Proyecto con Storage](https://github.com/joseluisgs/ProductosStorageMongoRestNet)
- [Proyecto Integrador](https://github.com/joseluisgs/TiendaDawApi-NetCore)