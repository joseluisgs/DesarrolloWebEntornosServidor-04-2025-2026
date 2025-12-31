# Railway Oriented Programming (ROP) en . NET

---

- [Railway Oriented Programming (ROP) en . NET](#railway-oriented-programming-rop-en--net)
  - [Introducción a Railway Oriented Programming](#introducción-a-railway-oriented-programming)
  - [Estructuras de Datos:   Result y Either](#estructuras-de-datos---result-y-either)
  - [CSharpFunctionalExtensions:   La librería recomendada](#csharpfunctionalextensions---la-librería-recomendada)
  - [Happy Path:   Encadenamiento de operaciones](#happy-path---encadenamiento-de-operaciones)
  - [Comparación:    Excepciones vs Result](#comparación----excepciones-vs-result)
  - [Patrones avanzados con Result](#patrones-avanzados-con-result)
  - [Integration con ASP.  NET Core](#integration-con-asp--net-core)

---

## Introducción a Railway Oriented Programming

**Railway Oriented Programming (ROP)** es un estilo de programación que modela el flujo de datos como vías de tren:   una vía representa el **camino del éxito** y otra el **camino del error**. 

![ROP](../images/rop.webp)

**Ventajas sobre excepciones:**

✅ **Claridad**:  El flujo de datos es explícito, no hay sorpresas ocultas.  

✅ **Composición**:  Es fácil encadenar funciones que pueden fallar.  

✅ **Errores como valores**: Los errores se manejan en el tipo de dato, no en el flujo de control. 

✅ **Testabilidad**: Más fácil de testear al no depender de excepciones. 

---

## Estructuras de Datos:   Result y Either

En . NET, usamos principalmente **`Result<T>`** y **`Result<T, TError>`** para implementar ROP.

**Conceptos:**

- **`Result<T>`**: Representa una operación que puede fallar con un mensaje de error (string). 
- **`Result<T, TError>`**: Representa una operación que puede fallar con un tipo de error personalizado.

---

## CSharpFunctionalExtensions:   La librería recomendada

**CSharpFunctionalExtensions** es la librería más popular para ROP en .  NET. 

**Instalación:**

```bash
dotnet add package CSharpFunctionalExtensions
```

**Ejemplo básico:**

```csharp
using CSharpFunctionalExtensions;

// Función que puede fallar
public Result<int> Dividir(int a, int b)
{
    if (b == 0)
        return Result.Failure<int>("No se puede dividir por cero");
    
    return Result.Success(a / b);
}

// Uso
var resultado = Dividir(10, 2);

if (resultado.IsSuccess)
{
    Console.WriteLine($"Resultado: {resultado.Value}");
}
else
{
    Console.WriteLine($"Error: {resultado.Error}");
}

// O con pattern matching
var mensaje = resultado.Match(
    onSuccess: valor => $"Resultado: {valor}",
    onFailure: error => $"Error:  {error}"
);
```

**Creación de Results:**

```csharp
// Success
Result<int> exitoso = Result.Success(42);

// Failure con mensaje
Result<int> fallido = Result.Failure<int>("Algo salió mal");

// Success sin valor (para operaciones void)
Result operacionExitosa = Result.Success();

// Failure sin valor
Result operacionFallida = Result.Failure("Operación fallida");

// Crear Result desde una condición
Result<string> resultado = Result. SuccessIf(
    condition: edad >= 18,
    value: "Acceso permitido",
    error: "Debe ser mayor de edad"
);

// Crear Result desde una función que puede lanzar excepción
Result<int> resultado = Result.Try(() => int.Parse("123"));
```

---

## Happy Path:   Encadenamiento de operaciones

El "happy path" es el flujo donde todo funciona correctamente.   ROP mantiene este flujo limpio. 

**Ejemplo: Procesamiento de solicitud de usuario**

```csharp
public record Usuario(string Nombre, string Email, int Edad);

public class UsuarioService
{
    // 1. Validar solicitud
    public Result<string> ValidarSolicitud(string request)
    {
        if (string.IsNullOrWhiteSpace(request))
            return Result.Failure<string>("La solicitud no puede estar vacía");
        
        return Result.Success(request);
    }

    // 2. Transformar datos
    public Result<int> TransformarDatos(string data)
    {
        if (int.TryParse(data, out int numero))
            return Result.Success(numero);
        
        return Result.Failure<int>("Formato de número inválido");
    }

    // 3. Guardar en base de datos
    public Result<bool> GuardarEnBaseDeDatos(int numero)
    {
        if (numero > 0)
            return Result.Success(true);
        
        return Result.Failure<bool>("El número debe ser positivo");
    }

    // Encadenar todas las operaciones
    public Result<bool> ProcesarSolicitud(string request)
    {
        return ValidarSolicitud(request)
            . Bind(solicitudValida => TransformarDatos(solicitudValida))
            .Bind(numero => GuardarEnBaseDeDatos(numero));
    }
}

// Uso
var servicio = new UsuarioService();
var resultado = servicio.ProcesarSolicitud("123");

resultado.Match(
    onSuccess: _ => Console.WriteLine("✅ Solicitud procesada exitosamente"),
    onFailure: error => Console.WriteLine($"❌ Error: {error}")
);
```

**Operadores principales:**

```csharp
// Bind - Encadenar operaciones que devuelven Result
Result<int> resultado = Result.Success(5)
    .Bind(x => Dividir(x, 2))
    .Bind(x => Multiplicar(x, 3));

// Map - Transformar el valor de éxito
Result<string> resultado = Result.Success(42)
    .Map(x => x.  ToString())
    .Map(x => $"El resultado es: {x}");

// Ensure - Validar una condición
Result<int> resultado = Result.Success(10)
    .Ensure(x => x > 0, "El valor debe ser positivo")
    .Ensure(x => x < 100, "El valor debe ser menor que 100");

// Tap - Ejecutar una acción sin modificar el resultado
Result<int> resultado = Result.Success(42)
    .Tap(x => Console.WriteLine($"Valor intermedio: {x}"))
    .Map(x => x * 2);

// OnSuccess / OnFailure - Ejecutar acciones según el resultado
resultado
    .OnSuccess(valor => Console.WriteLine($"Éxito:   {valor}"))
    .OnFailure(error => Console. WriteLine($"Error: {error}"));

// Finally - Ejecutar una acción siempre
resultado. Finally(r => Console.WriteLine("Operación finalizada"));

// Combine - Combinar múltiples Results
var resultado1 = Result. Success(10);
var resultado2 = Result. Success(20);
var resultado3 = Result.Success(30);

var combinado = Result.Combine(resultado1, resultado2, resultado3);
// Si alguno falla, el resultado combinado es Failure

// Check - Validar sin cambiar el valor
Result<int> resultado = Result.Success(10)
    .Check(x => x > 0 ?  Result.Success() : Result.Failure("Debe ser positivo"));
```

**Ejemplo completo:  Registro de usuarios**

```csharp
public record RegistroRequest(string NombreUsuario, string Contraseña, string Email);
public record Usuario(Guid Id, string NombreUsuario, string Email);

public class UsuarioService
{
    private readonly IUsuarioRepository _repository;

    public UsuarioService(IUsuarioRepository repository)
    {
        _repository = repository;
    }

    public async Task<Result<Usuario>> RegistrarUsuarioAsync(RegistroRequest request)
    {
        return await Result.Success(request)
            .Ensure(r => ! string.IsNullOrWhiteSpace(r.NombreUsuario), "El nombre de usuario es requerido")
            .Ensure(r => r.Contraseña.Length >= 8, "La contraseña debe tener al menos 8 caracteres")
            .Ensure(r => r.Email. Contains("@"), "El email debe ser válido")
            .Bind(r => VerificarDisponibilidadUsuario(r.NombreUsuario))
            .Bind(nombreUsuario => CrearUsuario(nombreUsuario, request.Email, request.Contraseña))
            .Tap(usuario => Console.WriteLine($"Usuario {usuario.NombreUsuario} registrado exitosamente"))
            . Bind(usuario => GuardarUsuarioAsync(usuario));
    }

    private Result<string> VerificarDisponibilidadUsuario(string nombreUsuario)
    {
        var existe = _repository.ExisteUsuario(nombreUsuario);
        
        return existe
            ? Result. Failure<string>("El nombre de usuario ya está en uso")
            : Result.Success(nombreUsuario);
    }

    private Result<Usuario> CrearUsuario(string nombreUsuario, string email, string contraseña)
    {
        // Lógica de creación (hashear contraseña, etc.)
        var usuario = new Usuario(Guid.NewGuid(), nombreUsuario, email);
        return Result.Success(usuario);
    }

    private async Task<Result<Usuario>> GuardarUsuarioAsync(Usuario usuario)
    {
        try
        {
            await _repository.GuardarAsync(usuario);
            return Result. Success(usuario);
        }
        catch (Exception ex)
        {
            return Result.Failure<Usuario>($"Error al guardar:  {ex.Message}");
        }
    }
}

// Uso
var servicio = new UsuarioService(repository);
var resultado = await servicio.RegistrarUsuarioAsync(new RegistroRequest(
    NombreUsuario:  "usuario123",
    Contraseña: "contraseñaSegura123",
    Email: "usuario@example.com"
));

resultado.Match(
    onSuccess: usuario => Console.WriteLine($"✅ Usuario registrado: {usuario. NombreUsuario}"),
    onFailure: error => Console.WriteLine($"❌ Error: {error}")
);
```

---

## Comparación:    Excepciones vs Result

**Ejemplo con excepciones (enfoque tradicional):**

```csharp
public class UsuarioServiceConExcepciones
{
    public async Task<Usuario> RegistrarUsuarioAsync(RegistroRequest request)
    {
        // Validación - lanza excepciones
        if (string.IsNullOrWhiteSpace(request.NombreUsuario))
            throw new ArgumentException("El nombre de usuario es requerido");
        
        if (request.Contraseña.Length < 8)
            throw new ArgumentException("La contraseña debe tener al menos 8 caracteres");
        
        // Verificar disponibilidad - lanza excepción
        if (_repository.ExisteUsuario(request.NombreUsuario))
            throw new InvalidOperationException("El nombre de usuario ya está en uso");
        
        // Crear usuario - puede lanzar excepción
        var usuario = new Usuario(Guid. NewGuid(), request.NombreUsuario, request.Email);
        
        // Guardar - puede lanzar excepción
        await _repository.GuardarAsync(usuario);
        
        return usuario;
    }
}

// Uso - necesita try-catch
try
{
    var usuario = await servicio.RegistrarUsuarioAsync(request);
    Console.WriteLine($"✅ Usuario registrado: {usuario.NombreUsuario}");
}
catch (ArgumentException ex)
{
    Console.WriteLine($"❌ Error de validación: {ex.Message}");
}
catch (InvalidOperationException ex)
{
    Console.WriteLine($"❌ Error de negocio: {ex.Message}");
}
catch (Exception ex)
{
    Console.WriteLine($"❌ Error inesperado: {ex.Message}");
}
```

**Mismo ejemplo con Result:**

```csharp
public class UsuarioServiceConResult
{
    public async Task<Result<Usuario>> RegistrarUsuarioAsync(RegistroRequest request)
    {
        return await Result.Success(request)
            .Ensure(r => ! string.IsNullOrWhiteSpace(r.  NombreUsuario), 
                "El nombre de usuario es requerido")
            .Ensure(r => r.Contraseña.Length >= 8, 
                "La contraseña debe tener al menos 8 caracteres")
            .Bind(r => VerificarDisponibilidadUsuario(r.NombreUsuario))
            .Map(nombreUsuario => new Usuario(Guid.NewGuid(), nombreUsuario, request.Email))
            .Bind(usuario => GuardarUsuarioAsync(usuario));
    }
}

// Uso - sin try-catch
var resultado = await servicio.RegistrarUsuarioAsync(request);

resultado.Match(
    onSuccess: usuario => Console.WriteLine($"✅ Usuario registrado: {usuario.NombreUsuario}"),
    onFailure: error => Console.WriteLine($"❌ Error: {error}")
);
```

**Comparación:**

| **Aspecto**           | **Excepciones**                              | **Result (ROP)**                   |
| :-------------------- | :------------------------------------------- | :--------------------------------- |
| **Flujo de control**  | Implícito, puede saltar en cualquier momento | Explícito, sigue el flujo de datos |
| **Composición**       | Difícil, cada función necesita try-catch     | Fácil, se encadenan con Bind/Map   |
| **Manejo de errores** | Disperso en bloques catch                    | Centralizado en Match              |
| **Testabilidad**      | Difícil testear caminos de error             | Fácil, los errores son valores     |
| **Performance**       | Overhead de stack unwinding                  | Sin overhead, son valores normales |
| **Legibilidad**       | Mezclado con lógica de negocio               | Separado claramente del happy path |

---

## Patrones avanzados con Result

**1. Result con tipos de error personalizados:**

```csharp
public enum ErrorType
{
    Validacion,
    NoEncontrado,
    NoAutorizado,
    ErrorInterno
}

public record Error(ErrorType Tipo, string Mensaje, string?  Detalle = null);

public Result<Usuario, Error> ObtenerUsuario(Guid id)
{
    var usuario = _repository.ObtenerPorId(id);
    
    if (usuario == null)
        return Result.Failure<Usuario, Error>(new Error(
            ErrorType.NoEncontrado,
            "Usuario no encontrado",
            $"No existe usuario con ID: {id}"
        ));
    
    return Result.Success<Usuario, Error>(usuario);
}
```

**2. Acumulación de errores:**

```csharp
public Result ValidarUsuario(Usuario usuario)
{
    var resultados = new[]
    {
        Result. SuccessIf(! string.IsNullOrEmpty(usuario. Nombre), "El nombre es requerido"),
        Result.  SuccessIf(usuario. Edad >= 18, "Debe ser mayor de edad"),
        Result.SuccessIf(usuario.Email.Contains("@"), "Email inválido")
    };

    // Combina todos los resultados
    // Si hay errores, los acumula separados por coma
    return Result.Combine(resultados);
}
```

**3. Maybe - para valores opcionales:**

```csharp
using CSharpFunctionalExtensions;

public Maybe<Usuario> BuscarUsuarioPorEmail(string email)
{
    var usuario = _repository.BuscarPorEmail(email);
    
    return usuario != null
        ? Maybe<Usuario>.From(usuario)
        : Maybe<Usuario>.None;
}

// Uso
var resultado = BuscarUsuarioPorEmail("usuario@example.com")
    .ToResult("Usuario no encontrado")
    .Map(usuario => usuario.Nombre);
```

**4. Validación con FluentValidation + Result:**

```csharp
using FluentValidation;

public class RegistroRequestValidator : AbstractValidator<RegistroRequest>
{
    public RegistroRequestValidator()
    {
        RuleFor(x => x.NombreUsuario)
            .NotEmpty().WithMessage("El nombre de usuario es requerido")
            .MinimumLength(3).WithMessage("Mínimo 3 caracteres");

        RuleFor(x => x.Contraseña)
            .NotEmpty()
            .MinimumLength(8).WithMessage("Mínimo 8 caracteres");

        RuleFor(x => x.  Email)
            .EmailAddress().WithMessage("Email inválido");
    }
}

public Result<RegistroRequest> ValidarRequest(RegistroRequest request)
{
    var validator = new RegistroRequestValidator();
    var validationResult = validator.Validate(request);

    if (!validationResult.IsValid)
    {
        var errores = string.Join(", ", validationResult.Errors.Select(e => e.ErrorMessage));
        return Result.Failure<RegistroRequest>(errores);
    }

    return Result.Success(request);
}
```

---

## Integration con ASP.  NET Core

**Extensión para convertir Result a IActionResult:**

```csharp
public static class ResultExtensions
{
    public static IActionResult ToActionResult<T>(this Result<T> result)
    {
        return result.IsSuccess
            ? new OkObjectResult(result.Value)
            : new BadRequestObjectResult(new { error = result.Error });
    }

    public static IActionResult ToActionResult(this Result result)
    {
        return result.IsSuccess
            ? new OkResult()
            : new BadRequestObjectResult(new { error = result. Error });
    }

    public static IActionResult ToActionResult<T>(this Result<T> result, Func<T, IActionResult> onSuccess)
    {
        return result.IsSuccess
            ? onSuccess(result.Value)
            : new BadRequestObjectResult(new { error = result.Error });
    }
}
```

**Controller con Result:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    private readonly IUsuarioService _service;

    public UsuariosController(IUsuarioService service)
    {
        _service = service;
    }

    [HttpPost]
    public async Task<IActionResult> RegistrarUsuario([FromBody] RegistroRequest request)
    {
        var resultado = await _service.RegistrarUsuarioAsync(request);

        return resultado.ToActionResult(usuario => CreatedAtAction(
            nameof(ObtenerUsuario),
            new { id = usuario. Id },
            usuario
        ));
    }

    [HttpGet("{id}")]
    public async Task<IActionResult> ObtenerUsuario(Guid id)
    {
        var resultado = await _service. ObtenerUsuarioPorIdAsync(id);

        return resultado.Match(
            onSuccess: usuario => Ok(usuario),
            onFailure: error => NotFound(new { error })
        );
    }

    [HttpPut("{id}")]
    public async Task<IActionResult> ActualizarUsuario(Guid id, [FromBody] ActualizarUsuarioRequest request)
    {
        var resultado = await _service.ActualizarUsuarioAsync(id, request);

        return resultado.Match<IActionResult>(
            onSuccess: usuario => Ok(usuario),
            onFailure: error => error switch
            {
                "Usuario no encontrado" => NotFound(new { error }),
                _ => BadRequest(new { error })
            }
        );
    }

    [HttpDelete("{id}")]
    public async Task<IActionResult> EliminarUsuario(Guid id)
    {
        var resultado = await _service. EliminarUsuarioAsync(id);

        return resultado.ToActionResult();
    }
}
```

**Middleware para manejo global de errores:**

```csharp
public class ResultExceptionMiddleware
{
    private readonly RequestDelegate _next;

    public ResultExceptionMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    public async Task InvokeAsync(HttpContext context)
    {
        try
        {
            await _next(context);
        }
        catch (Exception ex)
        {
            await HandleExceptionAsync(context, ex);
        }
    }

    private static Task HandleExceptionAsync(HttpContext context, Exception exception)
    {
        context.Response.ContentType = "application/json";
        context.Response.StatusCode = StatusCodes.Status500InternalServerError;

        var result = Result.Failure(exception.Message);

        return context.Response.WriteAsJsonAsync(new
        {
            error = result. Error,
            stackTrace = exception.  StackTrace
        });
    }
}

// Registro en Program.cs
app.UseMiddleware<ResultExceptionMiddleware>();
```

---
