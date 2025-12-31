# HTTP, REST y Clientes HTTP en .  NET

---

- [HTTP, REST y Clientes HTTP en .  NET](#http-rest-y-clientes-http-en---net)
  - [Protocolo HTTP](#protocolo-http)
    - [Verbos HTTP](#verbos-http)
    - [Códigos de respuesta HTTP más comunes](#códigos-de-respuesta-http-más-comunes)
    - [Qué es una API REST](#qué-es-una-api-rest)
  - [JWT (JSON Web Tokens)](#jwt-json-web-tokens)
    - [Necesidad en HTTP](#necesidad-en-http)
    - [Estructura de un JWT](#estructura-de-un-jwt)
  - [HttpClient en .NET](#httpclient-en-net)
  - [Refit: Cliente HTTP tipado](#refit-cliente-http-tipado)
  - [Ejemplo completo:   CRUD con Refit](#ejemplo-completo---crud-con-refit)
  - [IHttpClientFactory y mejores prácticas](#ihttpclientfactory-y-mejores-prácticas)
  - [Manejo de autenticación JWT](#manejo-de-autenticación-jwt)
  - [Testing de clientes HTTP](#testing-de-clientes-http)

---

## Protocolo HTTP

**HTTP (HyperText Transfer Protocol)** es el protocolo fundamental de la web. Es un protocolo **sin estado** (stateless), lo que significa que cada solicitud es independiente. 

**Importancia:**

✅ **Arquitecturas distribuidas**: Base de microservicios y sistemas distribuidos.  

✅ **APIs REST**: Fundamento para crear APIs escalables y mantenibles.  

✅ **Interoperabilidad**: Permite comunicación entre diferentes tecnologías. 

![API REST](../images/apirest.png)

### Verbos HTTP

| **Verbo**  | **Propósito**                              | **Idempotente** | **Safe** |
|: -----------|:-------------------------------------------|:----------------|:---------|
| **GET**    | Obtener recursos                           | Sí              | Sí       |
| **POST**   | Crear nuevos recursos                      | No              | No       |
| **PUT**    | Actualizar/reemplazar recursos completos   | Sí              | No       |
| **PATCH**  | Actualizar parcialmente recursos           | No              | No       |
| **DELETE** | Eliminar recursos                          | Sí              | No       |
| **HEAD**   | Obtener headers sin el cuerpo              | Sí              | Sí       |
| **OPTIONS**| Obtener métodos HTTP soportados            | Sí              | Sí       |

### Códigos de respuesta HTTP más comunes

**2xx - Éxito:**

- `200 OK`: Solicitud exitosa
- `201 Created`: Recurso creado exitosamente
- `204 No Content`: Solicitud exitosa sin contenido de respuesta

**4xx - Errores del cliente:**

- `400 Bad Request`: Solicitud mal formada
- `401 Unauthorized`: Autenticación requerida
- `403 Forbidden`: Sin permisos
- `404 Not Found`: Recurso no encontrado
- `409 Conflict`: Conflicto con el estado actual
- `422 Unprocessable Entity`: Errores de validación

**5xx - Errores del servidor:**

- `500 Internal Server Error`: Error genérico del servidor
- `502 Bad Gateway`: Respuesta inválida del servidor upstream
- `503 Service Unavailable`: Servicio temporalmente no disponible

### Qué es una API REST

Una **API REST (Representational State Transfer)** es un estilo arquitectónico para diseñar servicios web. 

**Principios REST:**

1. **Cliente-Servidor**:  Separación de responsabilidades
2. **Sin estado (Stateless)**: Cada request contiene toda la información necesaria
3. **Cacheable**: Las respuestas deben indicar si pueden ser cacheadas
4. **Interfaz uniforme**: Uso consistente de recursos y métodos HTTP
5. **Sistema en capas**: La arquitectura puede tener intermediarios (proxies, load balancers)

---

## JWT (JSON Web Tokens)

**JWT** es un estándar abierto (RFC 7519) para crear tokens de acceso que permiten la autenticación y autorización.  

![JWT](../images/jwt01.jpg)

### Necesidad en HTTP

✅ **Seguridad**: Solo solicitudes autenticadas acceden a recursos protegidos.  

✅ **Eficiencia**: No requiere almacenar sesiones en el servidor (stateless). 

✅ **Escalabilidad**: Facilita sistemas distribuidos sin sesiones compartidas. 

✅ **Interoperabilidad**: Funciona en cualquier plataforma y lenguaje. 

### Estructura de un JWT

Un JWT consta de **tres partes** separadas por puntos (`.`):

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

![JWT Structure](../images/jwt02.png)

**1. Header (Encabezado):**

```json
{
  "alg": "HS256",
  "typ": "JWT"
}
```

**2. Payload (Carga útil):**

```json
{
  "sub": "1234567890",
  "name": "John Doe",
  "email": "john@example.com",
  "role": "admin",
  "exp": 1516239022
}
```

**Claims comunes:**

- `sub`: Sujeto (ID del usuario)
- `iss`: Emisor del token
- `aud`: Audiencia (destinatario del token)
- `exp`: Tiempo de expiración (Unix timestamp)
- `iat`: Tiempo de emisión
- `nbf`: No válido antes de... 

**3. Signature (Firma):**

```
HMACSHA256(
  base64UrlEncode(header) + "." + base64UrlEncode(payload),
  secret
)
```

**Uso en HTTP:**

```http
GET /api/usuarios HTTP/1.1
Host: api.example.com
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... 
```

---

## HttpClient en .NET

**`HttpClient`** es la clase base para realizar solicitudes HTTP en . NET. 

**Ejemplo básico:**

```csharp
using System. Net.Http;
using System.Text. Json;

public class UsuarioService
{
    private readonly HttpClient _httpClient;

    public UsuarioService(HttpClient httpClient)
    {
        _httpClient = httpClient;
        _httpClient.BaseAddress = new Uri("https://api.example.com/");
    }

    // GET - Obtener todos los usuarios
    public async Task<List<Usuario>> ObtenerUsuariosAsync()
    {
        var response = await _httpClient.GetAsync("api/usuarios");
        response.EnsureSuccessStatusCode();

        var json = await response.Content.ReadAsStringAsync();
        return JsonSerializer.Deserialize<List<Usuario>>(json);
    }

    // GET - Obtener usuario por ID
    public async Task<Usuario? > ObtenerUsuarioPorIdAsync(int id)
    {
        var response = await _httpClient. GetAsync($"api/usuarios/{id}");
        
        if (response.StatusCode == System.Net.HttpStatusCode.NotFound)
            return null;

        response.EnsureSuccessStatusCode();
        return await response.Content.ReadFromJsonAsync<Usuario>();
    }

    // POST - Crear usuario
    public async Task<Usuario> CrearUsuarioAsync(CrearUsuarioRequest request)
    {
        var response = await _httpClient.PostAsJsonAsync("api/usuarios", request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadFromJsonAsync<Usuario>() 
            ?? throw new Exception("Respuesta vacía");
    }

    // PUT - Actualizar usuario
    public async Task<Usuario> ActualizarUsuarioAsync(int id, ActualizarUsuarioRequest request)
    {
        var response = await _httpClient. PutAsJsonAsync($"api/usuarios/{id}", request);
        response.EnsureSuccessStatusCode();

        return await response.Content.ReadFromJsonAsync<Usuario>() 
            ?? throw new Exception("Respuesta vacía");
    }

    // DELETE - Eliminar usuario
    public async Task EliminarUsuarioAsync(int id)
    {
        var response = await _httpClient.DeleteAsync($"api/usuarios/{id}");
        response.EnsureSuccessStatusCode();
    }
}
```

---

## Refit: Cliente HTTP tipado

**Refit** es una librería que convierte interfaces C# en clientes HTTP REST vivos,   similar a Retrofit en Java.  

**Instalación:**

```bash
dotnet add package Refit
dotnet add package Refit.HttpClientFactory
```

**Ventajas de Refit:**

✅ **Menos código boilerplate**: Define la API como interfaz.  

✅ **Tipado fuerte**: Errores en tiempo de compilación. 

✅ **Fácil de testear**: Se puede mockear la interfaz fácilmente. 

✅ **Integración con IHttpClientFactory**: Mejores prácticas automáticamente. 

---

## Ejemplo completo:   CRUD con Refit

**Modelo de datos:**

```csharp
public record Usuario(
    int Id,
    string Nombre,
    string Email,
    string?  Telefono
);

public record CrearUsuarioRequest(
    string Nombre,
    string Email,
    string?  Telefono = null
);

public record ActualizarUsuarioRequest(
    string Nombre,
    string Email,
    string? Telefono
);

public record ApiResponse<T>(
    bool Success,
    T?   Data,
    string? Error
);
```

**Interfaz de Refit:**

```csharp
using Refit;

public interface IUsuarioApi
{
    // GET - Obtener todos los usuarios
    [Get("/api/usuarios")]
    Task<List<Usuario>> ObtenerUsuariosAsync();

    // GET - Obtener usuario por ID
    [Get("/api/usuarios/{id}")]
    Task<Usuario> ObtenerUsuarioPorIdAsync(int id);

    // POST - Crear usuario
    [Post("/api/usuarios")]
    Task<Usuario> CrearUsuarioAsync([Body] CrearUsuarioRequest request);

    // PUT - Actualizar usuario
    [Put("/api/usuarios/{id}")]
    Task<Usuario> ActualizarUsuarioAsync(int id, [Body] ActualizarUsuarioRequest request);

    // PATCH - Actualizar parcialmente
    [Patch("/api/usuarios/{id}")]
    Task<Usuario> ActualizarParcialmenteAsync(int id, [Body] Dictionary<string, object> cambios);

    // DELETE - Eliminar usuario
    [Delete("/api/usuarios/{id}")]
    Task EliminarUsuarioAsync(int id);

    // GET con query parameters
    [Get("/api/usuarios")]
    Task<List<Usuario>> BuscarUsuariosAsync(
        [Query] string? nombre = null,
        [Query] int? pagina = null,
        [Query] int? tamanoPagina = null
    );

    // POST con headers personalizados
    [Post("/api/usuarios")]
    [Headers("X-Custom-Header: valor")]
    Task<Usuario> CrearUsuarioConHeaderAsync([Body] CrearUsuarioRequest request);

    // GET con respuesta ApiResponse envuelta
    [Get("/api/usuarios/{id}")]
    Task<ApiResponse<Usuario>> ObtenerUsuarioConRespuestaAsync(int id);
}
```

**Registro en Program.cs:**

```csharp
using Refit;

var builder = WebApplication.CreateBuilder(args);

// Registrar Refit client
builder.Services.AddRefitClient<IUsuarioApi>()
    .ConfigureHttpClient(c =>
    {
        c.BaseAddress = new Uri("https://api.example.com");
        c. Timeout = TimeSpan.FromSeconds(30);
    });

var app = builder.  Build();
app.Run();
```

**Repositorio que usa Refit:**

```csharp
using CSharpFunctionalExtensions;

public class UsuarioRepository
{
    private readonly IUsuarioApi _api;
    private readonly ILogger<UsuarioRepository> _logger;

    public UsuarioRepository(IUsuarioApi api, ILogger<UsuarioRepository> logger)
    {
        _api = api;
        _logger = logger;
    }

    public async Task<Result<List<Usuario>>> ObtenerTodosAsync()
    {
        try
        {
            var usuarios = await _api.ObtenerUsuariosAsync();
            return Result.Success(usuarios);
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.  HttpStatusCode.NotFound)
        {
            return Result. Failure<List<Usuario>>("No se encontraron usuarios");
        }
        catch (ApiException ex)
        {
            _logger.LogError(ex, "Error al obtener usuarios");
            return Result.Failure<List<Usuario>>($"Error de API: {ex.StatusCode}");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error inesperado");
            return Result.Failure<List<Usuario>>("Error inesperado al obtener usuarios");
        }
    }

    public async Task<Result<Usuario>> ObtenerPorIdAsync(int id)
    {
        try
        {
            var usuario = await _api.ObtenerUsuarioPorIdAsync(id);
            return Result.Success(usuario);
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net. HttpStatusCode.NotFound)
        {
            return Result. Failure<Usuario>($"Usuario con ID {id} no encontrado");
        }
        catch (ApiException ex)
        {
            return Result.Failure<Usuario>($"Error de API: {ex. StatusCode}");
        }
        catch (Exception ex)
        {
            _logger.LogError(ex, "Error al obtener usuario {Id}", id);
            return Result.Failure<Usuario>("Error inesperado");
        }
    }

    public async Task<Result<Usuario>> CrearAsync(CrearUsuarioRequest request)
    {
        try
        {
            var usuario = await _api. CrearUsuarioAsync(request);
            return Result.Success(usuario);
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.BadRequest)
        {
            return Result.Failure<Usuario>("Datos de usuario inválidos");
        }
        catch (ApiException ex)
        {
            return Result. Failure<Usuario>($"Error de API: {ex.StatusCode}");
        }
    }

    public async Task<Result> EliminarAsync(int id)
    {
        try
        {
            await _api.EliminarUsuarioAsync(id);
            return Result.Success();
        }
        catch (ApiException ex) when (ex.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            return Result.  Failure($"Usuario con ID {id} no encontrado");
        }
        catch (ApiException ex)
        {
            return Result.Failure($"Error de API: {ex.StatusCode}");
        }
    }
}
```

---

## IHttpClientFactory y mejores prácticas

**`IHttpClientFactory`** resuelve problemas comunes de HttpClient:

✅ **Evita socket exhaustion**: Reutiliza conexiones HTTP.  

✅ **Maneja DNS refresh**: Actualiza DNS automáticamente. 

✅ **Facilita configuración centralizada**: Políticas de retry, timeouts, etc. 

```csharp
// Program.cs
builder.Services.AddHttpClient("ApiCliente", client =>
{
    client.BaseAddress = new Uri("https://api.example.com");
    client.Timeout = TimeSpan. FromSeconds(30);
    client.DefaultRequestHeaders.Add("User-Agent", "MiApp/1.0");
})
. AddTransientHttpErrorPolicy(policy => 
    policy.WaitAndRetryAsync(3, retryAttempt => 
        TimeSpan.FromSeconds(Math.Pow(2, retryAttempt)))) // Retry exponencial
.AddPolicyHandler(Policy.TimeoutAsync<HttpResponseMessage>(10)); // Timeout por request
```

---

## Manejo de autenticación JWT

**DelegatingHandler para agregar token JWT automáticamente:**

```csharp
public class JwtAuthHandler : DelegatingHandler
{
    private readonly ITokenService _tokenService;

    public JwtAuthHandler(ITokenService tokenService)
    {
        _tokenService = tokenService;
    }

    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request,
        CancellationToken cancellationToken)
    {
        // Obtener token (desde memoria, cache, storage, etc.)
        var token = await _tokenService.  ObtenerTokenAsync();

        if (! string.IsNullOrEmpty(token))
        {
            request.Headers.Authorization = 
                new System.Net.Http.Headers.AuthenticationHeaderValue("Bearer", token);
        }

        return await base.SendAsync(request, cancellationToken);
    }
}

// Registro
builder.Services.AddTransient<JwtAuthHandler>();

builder.  Services.AddRefitClient<IUsuarioApi>()
    .ConfigureHttpClient(c => c.BaseAddress = new Uri("https://api.example.com"))
    .AddHttpMessageHandler<JwtAuthHandler>();
```

**Con Refit usando headers:**

```csharp
public interface IUsuarioApi
{
    [Get("/api/usuarios")]
    Task<List<Usuario>> ObtenerUsuariosAsync([Header("Authorization")] string authorization);
}

// Uso
var token = await _tokenService.ObtenerTokenAsync();
var usuarios = await _api.ObtenerUsuariosAsync($"Bearer {token}");
```

---

## Testing de clientes HTTP

```csharp
using Moq;
using Refit;
using NUnit.Framework;
using FluentAssertions;

[TestFixture]
public class UsuarioRepositoryTests
{
    private Mock<IUsuarioApi> _apiMock = null!  ;
    private UsuarioRepository _repository = null! ;

    [SetUp]
    public void SetUp()
    {
        _apiMock = new Mock<IUsuarioApi>();
        _repository = new UsuarioRepository(
            _apiMock.Object,
            Mock.Of<ILogger<UsuarioRepository>>()
        );
    }

    [Test]
    public async Task ObtenerTodos_DebeRetornarUsuarios()
    {
        // Arrange
        var usuariosEsperados = new List<Usuario>
        {
            new(1, "Usuario 1", "usuario1@example.com", null),
            new(2, "Usuario 2", "usuario2@example.com", null)
        };

        _apiMock
            .Setup(api => api. ObtenerUsuariosAsync())
            .ReturnsAsync(usuariosEsperados);

        // Act
        var resultado = await _repository.ObtenerTodosAsync();

        // Assert
        resultado.IsSuccess.Should().BeTrue();
        resultado.Value.Should(). HaveCount(2);
        resultado.Value. Should().BeEquivalentTo(usuariosEsperados);

        _apiMock.Verify(api => api.ObtenerUsuariosAsync(), Times.Once);
    }

    [Test]
    public async Task ObtenerPorId_CuandoNoExiste_DebeRetornarFailure()
    {
        // Arrange
        _apiMock
            .Setup(api => api.ObtenerUsuarioPorIdAsync(It.IsAny<int>()))
            .ThrowsAsync(ApiException.Create(
                new HttpRequestMessage(),
                HttpMethod.Get,
                new HttpResponseMessage(System.  Net.HttpStatusCode.NotFound)
            ).Result);

        // Act
        var resultado = await _repository.ObtenerPorIdAsync(999);

        // Assert
        resultado. IsFailure.Should().BeTrue();
        resultado.Error.Should().Contain("no encontrado");
    }
}
```

