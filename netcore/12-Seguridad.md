# Autenticación y Autorización con JWT en ASP.NET Core

- [Autenticación y Autorización con JWT en ASP.NET Core](#autenticación-y-autorización-con-jwt-en-aspnet-core)
  - [Introducción a la Seguridad en ASP.NET Core](#introducción-a-la-seguridad-en-aspnet-core)
  - [Conceptos Clave](#conceptos-clave)
    - [Autenticación vs Autorización](#autenticación-vs-autorización)
    - [¿Qué es JWT?](#qué-es-jwt)
  - [Arquitectura del Sistema de Autenticación](#arquitectura-del-sistema-de-autenticación)
  - [Configuración Inicial](#configuración-inicial)
    - [Instalación de Paquetes](#instalación-de-paquetes)
    - [Configuración de JWT](#configuración-de-jwt)
  - [Modelo de Usuario](#modelo-de-usuario)
    - [Entidad Usuario](#entidad-usuario)
    - [Roles y Permisos](#roles-y-permisos)
  - [Servicio de JWT](#servicio-de-jwt)
    - [Interfaz](#interfaz)
    - [Implementación](#implementación)
  - [Servicio de Autenticación](#servicio-de-autenticación)
    - [Interfaz](#interfaz-1)
    - [Implementación](#implementación-1)
  - [Controlador de Autenticación](#controlador-de-autenticación)
  - [Middleware de Autenticación JWT](#middleware-de-autenticación-jwt)
  - [Configuración de Autorización](#configuración-de-autorización)
    - [Políticas de Autorización](#políticas-de-autorización)
    - [Atributos de Autorización](#atributos-de-autorización)
  - [Obtener Usuario Autenticado](#obtener-usuario-autenticado)
  - [Testing de Autenticación](#testing-de-autenticación)
    - [Test del Servicio JWT](#test-del-servicio-jwt)
    - [Test del Controlador](#test-del-controlador)
  - [HTTPS y Certificados SSL](#https-y-certificados-ssl)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![Security Banner](../images/banner13.jpg)

---

## Introducción a la Seguridad en ASP.NET Core

ASP.NET Core proporciona un sistema robusto de **autenticación** y **autorización** integrado que se puede extender con JWT (JSON Web Tokens) para aplicaciones modernas. 

**Características principales:**
- ✅ Autenticación flexible (Cookie, JWT, OAuth, etc.)
- ✅ Autorización basada en roles y políticas
- ✅ Protección contra ataques (CSRF, XSS, etc.)
- ✅ Integración con Identity para gestión de usuarios
- ✅ Soporte para múltiples esquemas de autenticación

---

## Conceptos Clave

### Autenticación vs Autorización

| Autenticación | Autorización |
|: --------------|:-------------|
| ¿**Quién** eres? | ¿**Qué puedes** hacer? |
| Verifica identidad (usuario/contraseña, JWT) | Controla acceso a recursos |
| Proceso de login | Permisos y roles |

---

### ¿Qué es JWT?

**JWT (JSON Web Token)** es un estándar abierto (RFC 7519) para transmitir información de forma segura como un objeto JSON. 

**Estructura de un JWT:**

```
header.payload.signature
```

**Ejemplo:**

```
eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.
eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.
SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c
```

**Partes:**
1. **Header**: Algoritmo de firma
2. **Payload**:  Datos del usuario (claims)
3. **Signature**: Firma para verificar integridad

---

## Arquitectura del Sistema de Autenticación

```
┌─────────────┐
│   Cliente   │
└──────┬──────┘
       │ 1. POST /api/auth/register
       │    { email, password }
       ▼
┌─────────────────────┐
│   AuthController    │
└──────────┬──────────┘
           │ 2. AuthService. RegisterAsync()
           ▼
┌─────────────────────┐
│   AuthService       │
└──────────┬──────────┘
           │ 3. Hash password
           │ 4. Save user
           │ 5. Generate JWT
           ▼
┌─────────────────────┐
│     JwtService      │
└──────────┬──────────┘
           │ 6. Create token
           ▼
┌─────────────────────┐
│   Cliente ← JWT     │
└─────────────────────┘
```

---

## Configuración Inicial

### Instalación de Paquetes

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet add package System.IdentityModel.Tokens.Jwt
dotnet add package BCrypt.Net-Next
```

---

### Configuración de JWT

**appsettings.json:**

```json
{
  "Jwt": {
    "Secret": "tu-clave-secreta-muy-larga-y-segura-de-al-menos-32-caracteres",
    "Issuer": "https://localhost:5001",
    "Audience": "https://localhost:5001",
    "ExpirationInMinutes": 60
  }
}
```

**Clase de configuración:**

```csharp
namespace FunkosApi.Configuration;

public class JwtSettings
{
    public string Secret { get.  set; } = string.Empty;
    public string Issuer { get. set; } = string.Empty;
    public string Audience { get.  set; } = string.Empty;
    public int ExpirationInMinutes { get. set; } = 60;
}
```

**Registrar en Program.cs:**

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft. IdentityModel.Tokens;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// Configurar JWT
var jwtSettings = builder.Configuration. GetSection("Jwt").Get<JwtSettings>();
builder.Services.Configure<JwtSettings>(builder.Configuration.GetSection("Jwt"));

builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = jwtSettings! .Issuer,
        ValidAudience = jwtSettings. Audience,
        IssuerSigningKey = new SymmetricSecurityKey(Encoding. UTF8.GetBytes(jwtSettings.Secret))
    };
});

builder.Services.AddAuthorization();

var app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();
app.Run();
```

---

## Modelo de Usuario

### Entidad Usuario

```csharp
using System.ComponentModel.DataAnnotations;

namespace FunkosApi. Models;

public class Usuario
{
    public int Id { get.  set; }

    [Required]
    [StringLength(100)]
    public string Nombre { get. set; } = string.Empty;

    [Required]
    [EmailAddress]
    [StringLength(200)]
    public string Email { get. set; } = string.Empty;

    [Required]
    public string PasswordHash { get. set; } = string.Empty;

    [Required]
    [StringLength(50)]
    public string Rol { get. set; } = Roles.User;

    public DateTime FechaCreacion { get. set; } = DateTime.UtcNow;
    public DateTime?  FechaActualizacion { get. set; }
    public bool Activo { get. set; } = true;
}
```

---

### Roles y Permisos

```csharp
namespace FunkosApi.Models;

public static class Roles
{
    public const string Admin = "ADMIN";
    public const string User = "USER";
    public const string Manager = "MANAGER";
}

public static class Permisos
{
    public const string FunkosRead = "FUNKOS_READ";
    public const string FunkosWrite = "FUNKOS_WRITE";
    public const string FunkosDelete = "FUNKOS_DELETE";
    public const string PedidosRead = "PEDIDOS_READ";
    public const string PedidosWrite = "PEDIDOS_WRITE";
}
```

---

## Servicio de JWT

### Interfaz

```csharp
namespace FunkosApi.Services. Auth;

public interface IJwtService
{
    string GenerateToken(Usuario usuario);
    ClaimsPrincipal?  ValidateToken(string token);
    string?  ExtractEmail(string token);
}
```

---

### Implementación

```csharp
using Microsoft.Extensions.Options;
using Microsoft. IdentityModel.Tokens;
using System.IdentityModel. Tokens.Jwt;
using System.Security.Claims;
using System.Text;

namespace FunkosApi.Services.Auth;

public class JwtService : IJwtService
{
    private readonly JwtSettings _jwtSettings;
    private readonly ILogger<JwtService> _logger;

    public JwtService(IOptions<JwtSettings> jwtSettings, ILogger<JwtService> logger)
    {
        _jwtSettings = jwtSettings.Value;
        _logger = logger;
    }

    public string GenerateToken(Usuario usuario)
    {
        var claims = new[]
        {
            new Claim(JwtRegisteredClaimNames.Sub, usuario.Email),
            new Claim(JwtRegisteredClaimNames.Email, usuario.Email),
            new Claim(JwtRegisteredClaimNames.Jti, Guid.NewGuid().ToString()),
            new Claim(ClaimTypes.NameIdentifier, usuario.Id.ToString()),
            new Claim(ClaimTypes. Name, usuario.Nombre),
            new Claim(ClaimTypes.Role, usuario.Rol)
        };

        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(_jwtSettings.Secret));
        var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        var token = new JwtSecurityToken(
            issuer: _jwtSettings.Issuer,
            audience: _jwtSettings.Audience,
            claims: claims,
            expires: DateTime. UtcNow.AddMinutes(_jwtSettings.ExpirationInMinutes),
            signingCredentials: credentials
        );

        var tokenString = new JwtSecurityTokenHandler().WriteToken(token);

        _logger.LogInformation("Token generado para usuario {Email}", usuario.Email);

        return tokenString;
    }

    public ClaimsPrincipal? ValidateToken(string token)
    {
        try
        {
            var tokenHandler = new JwtSecurityTokenHandler();
            var key = Encoding. UTF8.GetBytes(_jwtSettings.Secret);

            var validationParameters = new TokenValidationParameters
            {
                ValidateIssuer = true,
                ValidateAudience = true,
                ValidateLifetime = true,
                ValidateIssuerSigningKey = true,
                ValidIssuer = _jwtSettings.Issuer,
                ValidAudience = _jwtSettings.Audience,
                IssuerSigningKey = new SymmetricSecurityKey(key)
            };

            var principal = tokenHandler.ValidateToken(token, validationParameters, out _);
            return principal;
        }
        catch (Exception ex)
        {
            _logger.LogWarning(ex, "Error al validar token");
            return null;
        }
    }

    public string? ExtractEmail(string token)
    {
        try
        {
            var handler = new JwtSecurityTokenHandler();
            var jwtToken = handler.ReadJwtToken(token);
            return jwtToken.Claims.FirstOrDefault(c => c.Type == JwtRegisteredClaimNames.Email)?.Value;
        }
        catch (Exception ex)
        {
            _logger.LogWarning(ex, "Error al extraer email del token");
            return null;
        }
    }
}
```

---

## Servicio de Autenticación

### Interfaz

```csharp
namespace FunkosApi.Services. Auth;

public interface IAuthService
{
    Task<AuthResponse> RegisterAsync(RegisterRequest request);
    Task<AuthResponse> LoginAsync(LoginRequest request);
}

public record RegisterRequest(string Nombre, string Email, string Password);
public record LoginRequest(string Email, string Password);
public record AuthResponse(string Token, string Email, string Nombre, string Rol);
```

---

### Implementación

```csharp
using BCrypt.Net;

namespace FunkosApi.Services. Auth;

public class AuthService :  IAuthService
{
    private readonly IUsuarioRepository _usuarioRepository;
    private readonly IJwtService _jwtService;
    private readonly ILogger<AuthService> _logger;

    public AuthService(
        IUsuarioRepository usuarioRepository,
        IJwtService jwtService,
        ILogger<AuthService> logger)
    {
        _usuarioRepository = usuarioRepository;
        _jwtService = jwtService;
        _logger = logger;
    }

    public async Task<AuthResponse> RegisterAsync(RegisterRequest request)
    {
        // Verificar si ya existe
        if (await _usuarioRepository.ExistsByEmailAsync(request.Email))
        {
            throw new InvalidOperationException("El email ya está registrado");
        }

        // Crear usuario
        var usuario = new Usuario
        {
            Nombre = request.Nombre,
            Email = request.Email,
            PasswordHash = BCrypt.Net. BCrypt.HashPassword(request. Password),
            Rol = Roles.User,
            Activo = true
        };

        var created = await _usuarioRepository.CreateAsync(usuario);

        // Generar token
        var token = _jwtService.GenerateToken(created);

        _logger.LogInformation("Usuario registrado:  {Email}", created.Email);

        return new AuthResponse(token, created.Email, created.Nombre, created.Rol);
    }

    public async Task<AuthResponse> LoginAsync(LoginRequest request)
    {
        // Buscar usuario
        var usuario = await _usuarioRepository.GetByEmailAsync(request.Email);
        if (usuario is null)
        {
            throw new UnauthorizedAccessException("Email o contraseña incorrectos");
        }

        // Verificar contraseña
        if (!BCrypt.Net.BCrypt. Verify(request.Password, usuario. PasswordHash))
        {
            throw new UnauthorizedAccessException("Email o contraseña incorrectos");
        }

        // Verificar si está activo
        if (!usuario. Activo)
        {
            throw new UnauthorizedAccessException("Usuario inactivo");
        }

        // Generar token
        var token = _jwtService.GenerateToken(usuario);

        _logger.LogInformation("Usuario autenticado: {Email}", usuario.Email);

        return new AuthResponse(token, usuario. Email, usuario.Nombre, usuario.Rol);
    }
}
```

---

## Controlador de Autenticación

```csharp
using Microsoft.AspNetCore.Mvc;

namespace FunkosApi.Controllers;

[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly IAuthService _authService;

    public AuthController(IAuthService authService)
    {
        _authService = authService;
    }

    /// <summary>
    /// Registrar nuevo usuario
    /// </summary>
    [HttpPost("register")]
    [ProducesResponseType(typeof(AuthResponse), StatusCodes.Status201Created)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status400BadRequest)]
    public async Task<ActionResult<AuthResponse>> Register(RegisterRequest request)
    {
        try
        {
            var response = await _authService.RegisterAsync(request);
            return CreatedAtAction(nameof(Register), response);
        }
        catch (InvalidOperationException ex)
        {
            return BadRequest(new ProblemDetails
            {
                Status = StatusCodes.Status400BadRequest,
                Title = "Error al registrar",
                Detail = ex.Message
            });
        }
    }

    /// <summary>
    /// Iniciar sesión
    /// </summary>
    [HttpPost("login")]
    [ProducesResponseType(typeof(AuthResponse), StatusCodes.Status200OK)]
    [ProducesResponseType(typeof(ProblemDetails), StatusCodes.Status401Unauthorized)]
    public async Task<ActionResult<AuthResponse>> Login(LoginRequest request)
    {
        try
        {
            var response = await _authService. LoginAsync(request);
            return Ok(response);
        }
        catch (UnauthorizedAccessException ex)
        {
            return Unauthorized(new ProblemDetails
            {
                Status = StatusCodes.Status401Unauthorized,
                Title = "Credenciales inválidas",
                Detail = ex.Message
            });
        }
    }
}
```

---

## Middleware de Autenticación JWT

El middleware de JWT ya está configurado con `app.UseAuthentication()` en `Program.cs`.

Para acceder a endpoints protegidos, el cliente debe enviar el token en el header: 

```http
GET /api/funkos HTTP/1.1
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... 
```

---

## Configuración de Autorización

### Políticas de Autorización

```csharp
builder.Services.AddAuthorization(options =>
{
    options. AddPolicy("AdminOnly", policy => policy.RequireRole(Roles.Admin));
    options.AddPolicy("UserOrAdmin", policy => policy.RequireRole(Roles.User, Roles.Admin));
    options.AddPolicy("FunkosWrite", policy => policy.RequireClaim("Permission", Permisos.FunkosWrite));
});
```

---

### Atributos de Autorización

**Por rol:**

```csharp
[ApiController]
[Route("api/[controller]")]
[Authorize(Roles = Roles.Admin)] // Solo administradores
public class FunkosController : ControllerBase
{
    [HttpGet]
    [AllowAnonymous] // Permitir acceso anónimo a este endpoint
    public async Task<ActionResult<IEnumerable<FunkoResponseDto>>> GetAll()
    {
        // ... 
    }

    [HttpPost]
    // Solo administradores (heredado del controlador)
    public async Task<ActionResult<FunkoResponseDto>> Create(CreateFunkoDto dto)
    {
        // ... 
    }

    [HttpPut("{id}")]
    [Authorize(Roles = $"{Roles.Admin},{Roles.Manager}")] // Admin o Manager
    public async Task<ActionResult<FunkoResponseDto>> Update(int id, UpdateFunkoDto dto)
    {
        // ...
    }
}
```

**Por política:**

```csharp
[HttpDelete("{id}")]
[Authorize(Policy = "AdminOnly")]
public async Task<IActionResult> Delete(int id)
{
    // ...
}
```

---

## Obtener Usuario Autenticado

```csharp
using System.Security.Claims;

[ApiController]
[Route("api/[controller]")]
[Authorize]
public class PerfilController : ControllerBase
{
    [HttpGet("me")]
    public ActionResult<UsuarioDto> GetMyProfile()
    {
        // Obtener ID del usuario del token
        var userIdClaim = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        if (userIdClaim is null)
            return Unauthorized();

        var userId = int.Parse(userIdClaim);

        // Obtener email
        var email = User.FindFirst(ClaimTypes.Email)?.Value;

        // Obtener nombre
        var nombre = User.FindFirst(ClaimTypes.Name)?.Value;

        // Obtener rol
        var rol = User.FindFirst(ClaimTypes.Role)?.Value;

        return Ok(new
        {
            Id = userId,
            Email = email,
            Nombre = nombre,
            Rol = rol
        });
    }
}
```

**Alternativa con extensión:**

```csharp
public static class ClaimsPrincipalExtensions
{
    public static int GetUserId(this ClaimsPrincipal principal)
    {
        var userIdClaim = principal.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        return int.Parse(userIdClaim! );
    }

    public static string GetUserEmail(this ClaimsPrincipal principal)
    {
        return principal.FindFirst(ClaimTypes.Email)?.Value ?? string.Empty;
    }
}

// Uso
[HttpGet("me")]
public ActionResult<UsuarioDto> GetMyProfile()
{
    var userId = User.GetUserId();
    var email = User.GetUserEmail();
    // ...
}
```

---

## Testing de Autenticación

### Test del Servicio JWT

```csharp
[TestFixture]
public class JwtServiceTests
{
    private JwtService _jwtService = null!;
    private JwtSettings _settings = null!;

    [SetUp]
    public void Setup()
    {
        _settings = new JwtSettings
        {
            Secret = "clave-secreta-de-prueba-muy-larga-para-testing",
            Issuer = "https://test",
            Audience = "https://test",
            ExpirationInMinutes = 60
        };

        var options = Options.Create(_settings);
        var logger = new Mock<ILogger<JwtService>>().Object;

        _jwtService = new JwtService(options, logger);
    }

    [Test]
    public void GenerateToken_ReturnsValidToken()
    {
        // Arrange
        var usuario = new Usuario
        {
            Id = 1,
            Email = "test@example.com",
            Nombre = "Test User",
            Rol = Roles.User
        };

        // Act
        var token = _jwtService.GenerateToken(usuario);

        // Assert
        token.Should().NotBeNullOrEmpty();

        var principal = _jwtService.ValidateToken(token);
        principal. Should().NotBeNull();

        var emailClaim = principal! .FindFirst(ClaimTypes.Email)?. Value;
        emailClaim. Should().Be("test@example.com");
    }

    [Test]
    public void ValidateToken_WithInvalidToken_ReturnsNull()
    {
        // Arrange
        var invalidToken = "invalid. token.here";

        // Act
        var principal = _jwtService.ValidateToken(invalidToken);

        // Assert
        principal.Should().BeNull();
    }
}
```

---

### Test del Controlador

```csharp
[TestFixture]
public class AuthControllerTests
{
    private Mock<IAuthService> _authServiceMock = null!;
    private AuthController _controller = null! ;

    [SetUp]
    public void Setup()
    {
        _authServiceMock = new Mock<IAuthService>();
        _controller = new AuthController(_authServiceMock.Object);
    }

    [Test]
    public async Task Register_WithValidRequest_ReturnsCreated()
    {
        // Arrange
        var request = new RegisterRequest("Test User", "test@example.com", "Password123!");
        var expectedResponse = new AuthResponse("token123", "test@example.com", "Test User", Roles. User);

        _authServiceMock
            .Setup(s => s. RegisterAsync(request))
            .ReturnsAsync(expectedResponse);

        // Act
        var result = await _controller.Register(request);

        // Assert
        var createdResult = result.Result. Should().BeOfType<CreatedAtActionResult>().Subject;
        var response = createdResult.Value. Should().BeOfType<AuthResponse>().Subject;
        response.Email.Should().Be("test@example.com");
    }

    [Test]
    public async Task Login_WithInvalidCredentials_ReturnsUnauthorized()
    {
        // Arrange
        var request = new LoginRequest("test@example.com", "wrongpassword");

        _authServiceMock
            .Setup(s => s.LoginAsync(request))
            .ThrowsAsync(new UnauthorizedAccessException("Credenciales inválidas"));

        // Act
        var result = await _controller.Login(request);

        // Assert
        result. Result.Should().BeOfType<UnauthorizedObjectResult>();
    }
}
```

---

## HTTPS y Certificados SSL

**Generar certificado de desarrollo:**

```bash
dotnet dev-certs https --trust
```

**Configurar HTTPS en appsettings. json:**

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url": "http://localhost:5000"
      },
      "Https": {
        "Url": "https://localhost:5001",
        "Certificate": {
          "Path": "cert/server.pfx",
          "Password": "password123"
        }
      }
    }
  }
}
```

**Forzar HTTPS:**

```csharp
app.UseHttpsRedirection();
```

---

## Buenas Prácticas

✅ **Contraseñas seguras**:  Usa BCrypt para hashear contraseñas

✅ **Tokens seguros**: Usa claves secretas largas y aleatorias

✅ **Expiración de tokens**: Configura tiempos de expiración razonables

✅ **HTTPS**: Siempre usa HTTPS en producción

✅ **Validación**:  Valida todos los inputs del usuario

✅ **Logging**: Registra intentos de autenticación fallidos

✅ **Rate limiting**: Implementa límites de intentos de login

✅ **Refresh tokens**: Implementa refresh tokens para mejorar UX

✅ **Claims mínimos**: No incluyas información sensible en el JWT

---

## Práctica de Clase

**Objetivo**: Implementar sistema completo de autenticación y autorización con JWT.  

**Tareas:**

1. ✅ Configurar JWT en ASP.NET Core
2. ✅ Crear modelo de Usuario con roles
3. ✅ Implementar servicio JWT
4. ✅ Implementar servicio de autenticación (registro y login)
5. ✅ Crear controlador de autenticación
6. ✅ Proteger endpoints de Funkos (solo admins pueden crear/modificar/eliminar)
7. ✅ Proteger endpoints de Pedidos (solo admins y el usuario dueño)
8. ✅ Implementar endpoint `/api/perfil/me` para obtener perfil del usuario autenticado
9. ✅ Testear autenticación y autorización

**Criterios de evaluación:**

- ✅ JWT configurado correctamente
- ✅ Contraseñas hasheadas con BCrypt
- ✅ Registro y login funcionan correctamente
- ✅ Endpoints protegidos correctamente
- ✅ Roles y permisos implementados
- ✅ Usuario puede ver su perfil y sus pedidos
- ✅ Tests completos (unitarios e integración)
- ✅ Documentación Swagger con autorización

---

## Proyecto del Curso

Puedes encontrar el proyecto con Entity Framework Core en el repositorio del curso.
- [Proyecto con Seguridad](https://github.com/joseluisgs/ProductosJwtMongoRestNet)
- [Proyecto Integrador](https://github.com/joseluisgs/TiendaDawApi-NetCore)
