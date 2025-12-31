# Seguridad en Aplicaciones de Servidor en . NET

---

- [Seguridad en Aplicaciones de Servidor en . NET](#seguridad-en-aplicaciones-de-servidor-en--net)
  - [Autenticación y Autorización](#autenticación-y-autorización)
    - [Conceptos fundamentales](#conceptos-fundamentales)
    - [Gestión de sesiones](#gestión-de-sesiones)
  - [JWT (JSON Web Tokens) en .NET](#jwt-json-web-tokens-en-net)
    - [Implementación de JWT](#implementación-de-jwt)
    - [Middleware de autenticación JWT](#middleware-de-autenticación-jwt)
  - [SSL/TLS en . NET](#ssltls-en--net)
    - [Handshake SSL/TLS](#handshake-ssltls)
    - [Configuración de HTTPS en ASP.NET Core](#configuración-de-https-en-aspnet-core)
  - [Generación y gestión de certificados](#generación-y-gestión-de-certificados)
  - [Ejemplo completo:  Servidor seguro con JWT y SSL/TLS](#ejemplo-completo--servidor-seguro-con-jwt-y-ssltls)
  - [Mejores prácticas de seguridad](#mejores-prácticas-de-seguridad)

---

## Autenticación y Autorización

### Conceptos fundamentales

**1. Autenticación**

La **autenticación** es el proceso de verificar la identidad de un usuario o entidad. Es esencial para garantizar que solo usuarios legítimos accedan a los recursos. 

```csharp
// Ejemplo conceptual de autenticación
public interface IAuthenticationService
{
    Task<AuthenticationResult> AuthenticateAsync(string username, string password);
}

public record AuthenticationResult(
    bool IsSuccess,
    string?  Token,
    string? ErrorMessage,
    Usuario? Usuario
);

public class AuthenticationService : IAuthenticationService
{
    private readonly IUsuarioRepository _usuarioRepository;
    private readonly IPasswordHasher _passwordHasher;

    public async Task<AuthenticationResult> AuthenticateAsync(string username, string password)
    {
        // 1. Buscar usuario
        var usuario = await _usuarioRepository.ObtenerPorNombreUsuarioAsync(username);
        
        if (usuario == null)
            return new AuthenticationResult(false, null, "Usuario no encontrado", null);

        // 2. Verificar contraseña
        if (! _passwordHasher.VerifyPassword(password, usuario. PasswordHash))
            return new AuthenticationResult(false, null, "Contraseña incorrecta", null);

        // 3. Generar token (veremos JWT más adelante)
        var token = GenerarToken(usuario);

        return new AuthenticationResult(true, token, null, usuario);
    }
}
```

**2. Autorización**

La **autorización** determina qué acciones o recursos están permitidos para un usuario autenticado.

```csharp
// Ejemplo de autorización basada en roles
public interface IAuthorizationService
{
    bool TienePermiso(Usuario usuario, string recurso, string accion);
}

public class AuthorizationService : IAuthorizationService
{
    public bool TienePermiso(Usuario usuario, string recurso, string accion)
    {
        return recurso switch
        {
            "Usuarios" when accion == "Leer" => 
                usuario.Roles.Any(r => r == "Admin" || r == "Usuario"),
            
            "Usuarios" when accion == "Escribir" => 
                usuario. Roles.Contains("Admin"),
            
            "Productos" when accion == "Leer" => 
                true, // Todos pueden leer productos
            
            "Productos" when accion == "Escribir" => 
                usuario.Roles.Any(r => r == "Admin" || r == "Vendedor"),
            
            _ => false
        };
    }
}
```

### Gestión de sesiones

En ASP.NET Core, las sesiones se gestionan de forma diferente según el enfoque:

**1. Sesiones tradicionales con cookies:**

```csharp
// Program.cs
builder.Services.AddDistributedMemoryCache();
builder.Services.AddSession(options =>
{
    options.IdleTimeout = TimeSpan.FromMinutes(30);
    options.Cookie.HttpOnly = true;
    options.Cookie.IsEssential = true;
    options.Cookie.SecurePolicy = CookieSecurePolicy.Always;
});

app.UseSession();

// Uso en un controller
public class UsuarioController : ControllerBase
{
    [HttpPost("login")]
    public IActionResult Login([FromBody] LoginRequest request)
    {
        // Autenticar usuario... 
        
        // Guardar en sesión
        HttpContext.Session.SetString("UsuarioId", usuario.Id.ToString());
        HttpContext.Session.SetString("UsuarioNombre", usuario. Nombre);

        return Ok(new { mensaje = "Login exitoso" });
    }

    [HttpGet("perfil")]
    public IActionResult ObtenerPerfil()
    {
        var usuarioId = HttpContext.Session.GetString("UsuarioId");
        
        if (string.IsNullOrEmpty(usuarioId))
            return Unauthorized();

        // Obtener datos del usuario... 
        return Ok(usuario);
    }

    [HttpPost("logout")]
    public IActionResult Logout()
    {
        HttpContext.Session.Clear();
        return Ok(new { mensaje = "Logout exitoso" });
    }
}
```

**2. Sesiones con tokens JWT (stateless - recomendado):**

Con JWT, el servidor no mantiene estado de sesión.  Toda la información está en el token.

---

## JWT (JSON Web Tokens) en .NET

![JWT](../images/jwt01.jpg)

**JWT** es un estándar (RFC 7519) para crear tokens de acceso que contienen información del usuario de forma segura.

### Implementación de JWT

**Instalación:**

```bash
dotnet add package Microsoft.AspNetCore.Authentication.JwtBearer
dotnet add package System.IdentityModel.Tokens.Jwt
```

**Servicio para generar y validar JWT:**

```csharp
using System.IdentityModel. Tokens.Jwt;
using System.Security.Claims;
using System.Text;
using Microsoft.IdentityModel. Tokens;

public class JwtService
{
    private readonly IConfiguration _configuration;

    public JwtService(IConfiguration configuration)
    {
        _configuration = configuration;
    }

    public string GenerarToken(Usuario usuario)
    {
        var secretKey = _configuration["Jwt:SecretKey"] 
            ?? throw new InvalidOperationException("JWT SecretKey no configurada");
        
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));
        var credentials = new SigningCredentials(key, SecurityAlgorithms.HmacSha256);

        // Claims (información del usuario en el token)
        var claims = new[]
        {
            new Claim(JwtRegisteredClaimNames.Sub, usuario.Id.ToString()),
            new Claim(JwtRegisteredClaimNames.Email, usuario.Email),
            new Claim(JwtRegisteredClaimNames.Name, usuario.Nombre),
            new Claim(JwtRegisteredClaimNames. Jti, Guid.NewGuid().ToString()),
            new Claim("roles", string.Join(",", usuario.Roles))
        };

        var token = new JwtSecurityToken(
            issuer: _configuration["Jwt:Issuer"],
            audience: _configuration["Jwt:Audience"],
            claims:  claims,
            expires: DateTime. UtcNow.AddHours(8),
            signingCredentials:  credentials
        );

        return new JwtSecurityTokenHandler().WriteToken(token);
    }

    public ClaimsPrincipal?  ValidarToken(string token)
    {
        var secretKey = _configuration["Jwt: SecretKey"]! ;
        var key = new SymmetricSecurityKey(Encoding.UTF8.GetBytes(secretKey));

        var validationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = _configuration["Jwt:Issuer"],
            ValidAudience = _configuration["Jwt: Audience"],
            IssuerSigningKey = key
        };

        try
        {
            var handler = new JwtSecurityTokenHandler();
            var principal = handler. ValidateToken(token, validationParameters, out _);
            return principal;
        }
        catch
        {
            return null;
        }
    }
}
```

**Configuración en appsettings.json:**

```json
{
  "Jwt": {
    "SecretKey": "tu-clave-secreta-muy-larga-y-segura-de-al-menos-32-caracteres",
    "Issuer": "MiAplicacion",
    "Audience": "MiAplicacionUsuarios"
  }
}
```

### Middleware de autenticación JWT

**Configuración en Program.cs:**

```csharp
using Microsoft.AspNetCore.Authentication. JwtBearer;
using Microsoft. IdentityModel.Tokens;
using System.Text;

var builder = WebApplication.CreateBuilder(args);

// Registrar JwtService
builder.Services.AddSingleton<JwtService>();

// Configurar autenticación JWT
builder.Services.AddAuthentication(options =>
{
    options.DefaultAuthenticateScheme = JwtBearerDefaults.AuthenticationScheme;
    options.DefaultChallengeScheme = JwtBearerDefaults.AuthenticationScheme;
})
.AddJwtBearer(options =>
{
    var secretKey = builder.Configuration["Jwt:SecretKey"]! ;
    var key = Encoding.UTF8.GetBytes(secretKey);

    options.TokenValidationParameters = new TokenValidationParameters
    {
        ValidateIssuer = true,
        ValidateAudience = true,
        ValidateLifetime = true,
        ValidateIssuerSigningKey = true,
        ValidIssuer = builder.Configuration["Jwt:Issuer"],
        ValidAudience = builder.Configuration["Jwt:Audience"],
        IssuerSigningKey = new SymmetricSecurityKey(key),
        ClockSkew = TimeSpan.Zero // Eliminar margen de tiempo de expiración
    };
});

// Configurar autorización
builder.Services.AddAuthorization(options =>
{
    options. AddPolicy("RequiereAdmin", policy => 
        policy.RequireClaim("roles", "Admin"));
    
    options.AddPolicy("RequiereVendedor", policy => 
        policy.RequireClaim("roles", "Vendedor", "Admin"));
});

builder.Services.AddControllers();

var app = builder.Build();

app.UseAuthentication(); // ⚠️ Debe ir ANTES de UseAuthorization
app.UseAuthorization();

app.MapControllers();
app.Run();
```

**Controller con autenticación JWT:**

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore. Mvc;

[ApiController]
[Route("api/[controller]")]
public class AuthController : ControllerBase
{
    private readonly JwtService _jwtService;
    private readonly IAuthenticationService _authService;

    public AuthController(JwtService jwtService, IAuthenticationService authService)
    {
        _jwtService = jwtService;
        _authService = authService;
    }

    [HttpPost("login")]
    public async Task<IActionResult> Login([FromBody] LoginRequest request)
    {
        var resultado = await _authService.AuthenticateAsync(request.Username, request.Password);

        if (!resultado.IsSuccess)
            return Unauthorized(new { error = resultado.ErrorMessage });

        var token = _jwtService. GenerarToken(resultado.Usuario! );

        return Ok(new
        {
            token,
            usuario = new
            {
                resultado.Usuario.Id,
                resultado.Usuario.Nombre,
                resultado.Usuario.Email
            }
        });
    }

    [Authorize] // ✅ Requiere token JWT válido
    [HttpGet("perfil")]
    public IActionResult ObtenerPerfil()
    {
        // User. Claims contiene la información del token
        var usuarioId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        var email = User.FindFirst(ClaimTypes.Email)?.Value;

        return Ok(new { usuarioId, email });
    }

    [Authorize(Policy = "RequiereAdmin")] // ✅ Solo admins
    [HttpGet("admin-only")]
    public IActionResult RecursoAdmin()
    {
        return Ok(new { mensaje = "Solo admins pueden ver esto" });
    }
}
```

**Uso del token desde el cliente:**

```http
POST /api/auth/login
Content-Type: application/json

{
  "username": "usuario@example.com",
  "password":  "contraseña123"
}

Response: 
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "usuario": {
    "id": 1,
    "nombre": "Usuario",
    "email": "usuario@example.com"
  }
}

---

GET /api/auth/perfil
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... 
```

---

## SSL/TLS en . NET

![TLS](../images/tsl. png)

**SSL/TLS** son protocolos que proporcionan cifrado y autenticación para comunicaciones seguras.

### Handshake SSL/TLS

El **handshake** es el proceso de establecimiento de conexión segura: 

1. **Cliente** → Servidor: "ClientHello" (versión TLS, algoritmos soportados)
2. **Servidor** → Cliente: "ServerHello" + Certificado
3. **Cliente** verifica certificado
4. **Cliente** → Servidor:  Clave de sesión cifrada con clave pública del servidor
5. **Servidor** descifra clave de sesión con su clave privada
6. Ambos acuerdan parámetros de cifrado
7. **Conexión segura establecida** ✅

### Configuración de HTTPS en ASP.NET Core

**Program.cs:**

```csharp
var builder = WebApplication.CreateBuilder(args);

// Configurar Kestrel para HTTPS
builder.WebHost.ConfigureKestrel(options =>
{
    options.ListenAnyIP(5000); // HTTP
    options.ListenAnyIP(5001, listenOptions =>
    {
        listenOptions.UseHttps("certificados/servidor.pfx", "contraseña-del-certificado");
    });
});

var app = builder.Build();

// Redirigir HTTP a HTTPS
app.UseHttpsRedirection();

app.Run();
```

**appsettings.json (alternativa):**

```json
{
  "Kestrel": {
    "Endpoints": {
      "Http": {
        "Url":  "http://localhost:5000"
      },
      "Https": {
        "Url":  "https://localhost:5001",
        "Certificate": {
          "Path": "certificados/servidor.pfx",
          "Password": "contraseña-del-certificado"
        }
      }
    }
  }
}
```

---

## Generación y gestión de certificados

**Generar certificado autofirmado con PowerShell:**

```powershell
# Generar certificado
$cert = New-SelfSignedCertificate `
    -DnsName "localhost", "miapp.local" `
    -CertStoreLocation "cert:\LocalMachine\My" `
    -KeyExportPolicy Exportable `
    -KeySpec Signature `
    -KeyLength 2048 `
    -KeyAlgorithm RSA `
    -HashAlgorithm SHA256

# Exportar a archivo . pfx
$password = ConvertTo-SecureString -String "MiContraseña123!" -Force -AsPlainText
Export-PfxCertificate `
    -Cert $cert `
    -FilePath "certificados\servidor.pfx" `
    -Password $password
```

**Generar certificado con OpenSSL (alternativa multiplataforma):**

```bash
# Generar clave privada
openssl genrsa -out servidor.key 2048

# Generar certificado autofirmado
openssl req -new -x509 -key servidor.key -out servidor.crt -days 365

# Convertir a formato . pfx
openssl pkcs12 -export -out servidor.pfx -inkey servidor.key -in servidor.crt
```

**Usar certificado de desarrollo de . NET:**

```bash
# Generar certificado de desarrollo
dotnet dev-certs https --trust

# Exportar certificado
dotnet dev-certs https -ep certificados/servidor.pfx -p MiContraseña123! 
```

---

## Ejemplo completo:  Servidor seguro con JWT y SSL/TLS

**Program.cs:**

```csharp
using Microsoft.AspNetCore. Authentication.JwtBearer;
using Microsoft.IdentityModel. Tokens;
using System.Text;

var builder = WebApplication. CreateBuilder(args);

// ===== CONFIGURAR HTTPS =====
builder.WebHost. ConfigureKestrel(options =>
{
    options.ListenAnyIP(5001, listenOptions =>
    {
        listenOptions.UseHttps("certificados/servidor.pfx", "MiContraseña123!");
    });
});

// ===== CONFIGURAR JWT =====
builder.Services.AddSingleton<JwtService>();

builder.Services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
    .AddJwtBearer(options =>
    {
        var key = Encoding.UTF8.GetBytes(builder.Configuration["Jwt:SecretKey"]!);
        
        options.TokenValidationParameters = new TokenValidationParameters
        {
            ValidateIssuer = true,
            ValidateAudience = true,
            ValidateLifetime = true,
            ValidateIssuerSigningKey = true,
            ValidIssuer = builder.Configuration["Jwt:Issuer"],
            ValidAudience = builder.Configuration["Jwt:Audience"],
            IssuerSigningKey = new SymmetricSecurityKey(key)
        };
    });

builder.Services.AddAuthorization();
builder.Services.AddControllers();

var app = builder.Build();

// ===== MIDDLEWARE DE SEGURIDAD =====
app.UseHttpsRedirection(); // Redirigir HTTP → HTTPS
app.UseAuthentication();
app.UseAuthorization();

app.MapControllers();

Console.WriteLine("🔒 Servidor seguro iniciado en https://localhost:5001");
app.Run();
```

**Controller de ejemplo:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class ProductosController : ControllerBase
{
    [HttpGet]
    public IActionResult ObtenerTodos()
    {
        // Público, no requiere autenticación
        return Ok(new[] { "Producto 1", "Producto 2" });
    }

    [Authorize] // ✅ Requiere JWT válido
    [HttpPost]
    public IActionResult Crear([FromBody] CrearProductoRequest request)
    {
        var usuarioId = User.FindFirst(ClaimTypes.NameIdentifier)?.Value;
        return Ok(new { mensaje = $"Producto creado por usuario {usuarioId}" });
    }

    [Authorize(Roles = "Admin")] // ✅ Solo admins
    [HttpDelete("{id}")]
    public IActionResult Eliminar(int id)
    {
        return Ok(new { mensaje = $"Producto {id} eliminado" });
    }
}
```

---

## Mejores prácticas de seguridad

✅ **1. Usar HTTPS siempre en producción**

✅ **2. No almacenar claves secretas en código**
```csharp
// ❌ MAL
var secretKey = "mi-clave-secreta";

// ✅ BIEN
var secretKey = builder.Configuration["Jwt:SecretKey"]; // desde appsettings
var secretKey = Environment.GetEnvironmentVariable("JWT_SECRET"); // variable de entorno
```

✅ **3. Usar contraseñas hasheadas**
```csharp
// Usar ASP.NET Core Identity o BCrypt. Net
using BCrypt.Net;

var hash = BCrypt.HashPassword("contraseña123");
bool esValida = BCrypt.Verify("contraseña123", hash);
```

✅ **4. Configurar expiración de tokens JWT**

✅ **5. Implementar refresh tokens para sesiones largas**

✅ **6. Validar y sanitizar todas las entradas**

✅ **7. Usar CORS de forma restrictiva**
```csharp
builder.Services.AddCors(options =>
{
    options.AddPolicy("MiPolitica", policy =>
    {
        policy.WithOrigins("https://miapp.com")
              .AllowAnyMethod()
              .AllowAnyHeader();
    });
});
```

✅ **8. Implementar rate limiting para prevenir ataques**

✅ **9. Registrar intentos de autenticación fallidos**

✅ **10. Mantener certificados actualizados**

---

