# Servicios REST en . NET

- [Servicios REST en . NET](#servicios-rest-en--net)
  - [Características de REST](#características-de-rest)
    - [Principios fundamentales de REST:](#principios-fundamentales-de-rest)
  - [Componentes de una API REST](#componentes-de-una-api-rest)
    - [Request (Solicitud)](#request-solicitud)
    - [Response (Respuesta)](#response-respuesta)
  - [Ventajas y Desventajas](#ventajas-y-desventajas)
    - [Ventajas de REST](#ventajas-de-rest)
    - [Desventajas de REST](#desventajas-de-rest)
  - [API REST (RESTful) en ASP.NET Core](#api-rest-restful-en-aspnet-core)
  - [Recursos y Endpoints](#recursos-y-endpoints)
    - [Convenciones para diseñar endpoints:](#convenciones-para-diseñar-endpoints)
    - [Ejemplos de endpoints:](#ejemplos-de-endpoints)
  - [Métodos HTTP](#métodos-http)
    - [Descripción detallada:](#descripción-detallada)
  - [Respuestas HTTP](#respuestas-http)
    - [Códigos más comunes:](#códigos-más-comunes)
    - [Ejemplo de respuestas en ASP.NET Core:](#ejemplo-de-respuestas-en-aspnet-core)
  - [Manejo de Errores (Error Handling)](#manejo-de-errores-error-handling)
  - [Versionado de APIs](#versionado-de-apis)
  - [Ejemplo de diseño de acceso a un recurso](#ejemplo-de-diseño-de-acceso-a-un-recurso)
  - [Práctica de clase](#práctica-de-clase)

![banner](../images/banner02.webp)

---

## Características de REST

**REST (Representational State Transfer)** es un estilo de arquitectura para sistemas de software que se utiliza principalmente en el desarrollo de servicios web. Los servicios que siguen los principios de REST se denominan **servicios web RESTful**.

### Principios fundamentales de REST: 

1. **Arquitectura Cliente-Servidor**: 
   - El **cliente** envía solicitudes HTTP al servidor. 
   - El **servidor** procesa la solicitud y devuelve una respuesta HTTP. 
   - La separación de responsabilidades permite que cliente y servidor evolucionen de forma independiente.

2. **Sin Estado (Stateless)**:
   - Cada solicitud del cliente al servidor debe contener **toda la información necesaria** para procesarla.
   - El servidor **no almacena ningún estado** del cliente entre solicitudes. 
   - Esto mejora la **escalabilidad** y simplifica la arquitectura del servidor.

3. **Cacheable**:
   - Las respuestas del servidor pueden ser almacenadas en caché por el cliente.
   - Esto mejora la **eficiencia** y reduce la carga del servidor al evitar solicitudes repetitivas de la misma información. 
   - Los encabezados HTTP (`Cache-Control`, `ETag`) controlan el comportamiento del caché.

4. **Sistema en Capas**:
   - Un servicio REST puede estar compuesto por **varias capas** de servidores (proxies, balanceadores de carga, gateways).
   - Cada capa tiene una responsabilidad específica y puede interactuar solo con las capas adyacentes.
   - Esto mejora la **escalabilidad** y **seguridad**. 

5. **Interfaz Uniforme**:
   - Los servicios REST utilizan un conjunto **limitado y bien definido de métodos HTTP** (GET, POST, PUT, DELETE, PATCH).
   - Los recursos se identifican por **URIs (Uniform Resource Identifiers)** únicas.
   - Se utilizan **representaciones estándar** (JSON, XML) para intercambiar datos.

---

## Componentes de una API REST

### Request (Solicitud)

Una solicitud REST se compone de: 

1. **Método HTTP**: Indica la acción a realizar (GET, POST, PUT, DELETE, PATCH).
2. **URI**: Identifica el recurso sobre el que se va a actuar (ej:  `/api/usuarios/1`).
3. **Headers**: Contienen metadatos sobre la solicitud: 
   - `Content-Type`: Tipo de contenido enviado (ej: `application/json`).
   - `Authorization`: Token de autenticación (ej:  `Bearer <JWT>`).
   - `Accept`: Tipo de contenido que el cliente puede aceptar. 
   - `User-Agent`: Información sobre el cliente. 
4. **Body**: (Opcional) Contiene los datos enviados al servidor (en POST, PUT, PATCH).

**Ejemplo de solicitud:**

```http
POST /api/usuarios HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... 

{
  "nombre": "Juan Pérez",
  "email": "juan@example.com"
}
```

---

### Response (Respuesta)

Una respuesta REST incluye:

1. **Código de Estado HTTP**: Indica el resultado de la solicitud (200, 201, 400, 404, 500, etc.).
2. **Headers**: Contienen metadatos sobre la respuesta:
   - `Content-Type`: Tipo de contenido devuelto. 
   - `Cache-Control`: Control de caché.
   - `Location`: URL del recurso creado (en respuestas 201).
3. **Body**: Contiene la representación del recurso solicitado (generalmente en JSON).

**Ejemplo de respuesta:**

```http
HTTP/1.1 201 Created
Content-Type: application/json
Location: /api/usuarios/1

{
  "id": 1,
  "nombre": "Juan Pérez",
  "email": "juan@example.com"
}
```

---

## Ventajas y Desventajas

### Ventajas de REST

✅ **Simplicidad**: Fácil de entender y usar (basado en HTTP estándar).

✅ **Escalabilidad**: Su naturaleza stateless permite escalar fácilmente.

✅ **Flexibilidad**: Soporta múltiples formatos de datos (JSON, XML, texto plano).

✅ **Compatibilidad**: Funciona con la mayoría de lenguajes, plataformas y clientes.

✅ **Cacheable**: Mejora el rendimiento mediante el uso de caché HTTP.

### Desventajas de REST

❌ **No apto para tiempo real**: No es ideal para streaming de datos o comunicación bidireccional en tiempo real (usa WebSockets para eso).

❌ **Seguridad**:  Requiere implementación manual de seguridad (HTTPS, JWT, OAuth).

❌ **Over-fetching / Under-fetching**: Puede devolver más o menos datos de los necesarios (GraphQL resuelve esto).

❌ **No mantiene estado**: Aplicaciones que requieren estado complejo necesitan mecanismos adicionales (cookies, JWT).

---

## API REST (RESTful) en ASP.NET Core

Una **API REST** en ASP.NET Core se construye usando **Web API Controllers** y sigue los principios REST para exponer recursos a través de HTTP.

**Componentes de una API RESTful:**

1. **URL Endpoint**: Representa el recurso al que se quiere acceder.
   - Ejemplo: `https://api.example.com/api/usuarios`

2. **Verbo HTTP**: Indica la acción a realizar sobre el recurso.
   - Ejemplos: `GET`, `POST`, `PUT`, `DELETE`, `PATCH`

3. **Body del mensaje**: (Opcional) Contiene los datos para crear o actualizar un recurso. 

**Ejemplo básico de API REST en ASP.NET Core:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    private static List<Usuario> _usuarios = new();

    [HttpGet]
    public ActionResult<IEnumerable<Usuario>> GetAll()
    {
        return Ok(_usuarios);
    }

    [HttpGet("{id}")]
    public ActionResult<Usuario> GetById(int id)
    {
        var usuario = _usuarios. FirstOrDefault(u => u. Id == id);
        if (usuario == null)
            return NotFound();

        return Ok(usuario);
    }

    [HttpPost]
    public ActionResult<Usuario> Create(Usuario usuario)
    {
        usuario.Id = _usuarios.Count + 1;
        _usuarios. Add(usuario);
        return CreatedAtAction(nameof(GetById), new { id = usuario. Id }, usuario);
    }

    [HttpPut("{id}")]
    public IActionResult Update(int id, Usuario usuario)
    {
        var existente = _usuarios.FirstOrDefault(u => u.Id == id);
        if (existente == null)
            return NotFound();

        existente.Nombre = usuario.Nombre;
        existente.Email = usuario.Email;
        return NoContent();
    }

    [HttpDelete("{id}")]
    public IActionResult Delete(int id)
    {
        var usuario = _usuarios.FirstOrDefault(u => u.Id == id);
        if (usuario == null)
            return NotFound();

        _usuarios.Remove(usuario);
        return NoContent();
    }
}

public record Usuario(int Id, string Nombre, string Email);
```

![REST Architecture](../images/rest.webp)

---

## Recursos y Endpoints

Un **recurso** en REST es cualquier objeto que queremos gestionar (usuarios, productos, pedidos, etc.). Cada recurso tiene una **URL única** (endpoint).

### Convenciones para diseñar endpoints: 

✅ **Usa sustantivos en plural** para los nombres de recursos:
- ✅ Correcto: `/api/usuarios`, `/api/productos`
- ❌ Incorrecto: `/api/usuario`, `/api/crearUsuario`

✅ **Evita verbos en las URLs**.  Los verbos se representan con **métodos HTTP**:
- ✅ Correcto: `GET /api/usuarios` (obtener usuarios)
- ❌ Incorrecto: `/api/obtenerUsuarios`

✅ **Usa IDs en la URL para identificar recursos específicos**:
- `/api/usuarios/{id}` → Accede al usuario con ID específico. 

✅ **Usa recursos anidados para relaciones**:
- `/api/usuarios/{id}/pedidos` → Pedidos de un usuario específico.

### Ejemplos de endpoints:

| Recurso | Endpoint | Descripción |
|: --------|:---------|:------------|
| Usuarios | `GET /api/usuarios` | Obtener todos los usuarios |
| Usuario específico | `GET /api/usuarios/1` | Obtener usuario con ID 1 |
| Crear usuario | `POST /api/usuarios` | Crear un nuevo usuario |
| Actualizar usuario | `PUT /api/usuarios/1` | Actualizar usuario con ID 1 |
| Eliminar usuario | `DELETE /api/usuarios/1` | Eliminar usuario con ID 1 |
| Pedidos de un usuario | `GET /api/usuarios/1/pedidos` | Obtener pedidos del usuario con ID 1 |

---

## Métodos HTTP

Los **métodos HTTP** representan las acciones que se pueden realizar sobre un recurso. 

| Método      | Propósito                                 | Idempotente | Seguro |
| :---------- | :---------------------------------------- | :---------- | :----- |
| **GET**     | Obtener un recurso                        | ✅ Sí        | ✅ Sí   |
| **POST**    | Crear un nuevo recurso                    | ❌ No        | ❌ No   |
| **PUT**     | Actualizar/reemplazar un recurso completo | ✅ Sí        | ❌ No   |
| **PATCH**   | Actualizar parcialmente un recurso        | ❌ No        | ❌ No   |
| **DELETE**  | Eliminar un recurso                       | ✅ Sí        | ❌ No   |
| **HEAD**    | Obtener solo headers (sin body)           | ✅ Sí        | ✅ Sí   |
| **OPTIONS** | Obtener métodos HTTP soportados           | ✅ Sí        | ✅ Sí   |

### Descripción detallada:

**1. GET**:  Obtener información de un recurso. 

```csharp
[HttpGet("{id}")]
public ActionResult<Usuario> GetById(int id)
{
    var usuario = _usuarios.FirstOrDefault(u => u.Id == id);
    return usuario == null ? NotFound() : Ok(usuario);
}
```

**2. POST**: Crear un nuevo recurso.

```csharp
[HttpPost]
public ActionResult<Usuario> Create(Usuario usuario)
{
    _usuarios.Add(usuario);
    return CreatedAtAction(nameof(GetById), new { id = usuario.Id }, usuario);
}
```

**3. PUT**: Actualizar un recurso completo (requiere enviar todos los campos).

```csharp
[HttpPut("{id}")]
public IActionResult Update(int id, Usuario usuario)
{
    var existente = _usuarios.FirstOrDefault(u => u.Id == id);
    if (existente == null) return NotFound();

    existente.Nombre = usuario.Nombre;
    existente.Email = usuario.Email;
    return NoContent();
}
```

**4. PATCH**: Actualizar parcialmente un recurso (solo los campos modificados).

```csharp
[HttpPatch("{id}")]
public IActionResult PartialUpdate(int id, JsonPatchDocument<Usuario> patchDoc)
{
    var usuario = _usuarios.FirstOrDefault(u => u.Id == id);
    if (usuario == null) return NotFound();

    patchDoc.ApplyTo(usuario);
    return NoContent();
}
```

**5. DELETE**: Eliminar un recurso. 

```csharp
[HttpDelete("{id}")]
public IActionResult Delete(int id)
{
    var usuario = _usuarios. FirstOrDefault(u => u. Id == id);
    if (usuario == null) return NotFound();

    _usuarios.Remove(usuario);
    return NoContent();
}
```

---

## Respuestas HTTP

Los **códigos de estado HTTP** indican el resultado de la solicitud. 

### Códigos más comunes:

**2xx - Éxito:**

- `200 OK`: Solicitud exitosa. 
- `201 Created`: Recurso creado exitosamente (con `Location` header).
- `204 No Content`: Solicitud exitosa sin contenido de respuesta.

**4xx - Errores del Cliente:**

- `400 Bad Request`: Solicitud mal formada.
- `401 Unauthorized`: Autenticación requerida.
- `403 Forbidden`: Sin permisos. 
- `404 Not Found`: Recurso no encontrado. 
- `405 Method Not Allowed`: Método HTTP no permitido.
- `406 Not Acceptable`: El servidor no puede generar el tipo de contenido solicitado. 
- `408 Request Timeout`: Solicitud tardó demasiado tiempo.
- `409 Conflict`: Conflicto con el estado actual del recurso.
- `422 Unprocessable Entity`: Errores de validación. 

**5xx - Errores del Servidor:**

- `500 Internal Server Error`: Error genérico del servidor.
- `502 Bad Gateway`: Respuesta inválida del servidor upstream.
- `503 Service Unavailable`: Servicio temporalmente no disponible. 

### Ejemplo de respuestas en ASP.NET Core:

```csharp
[HttpGet("{id}")]
public ActionResult<Usuario> GetById(int id)
{
    var usuario = _usuarios.FirstOrDefault(u => u.Id == id);
    
    if (usuario == null)
        return NotFound(); // 404

    return Ok(usuario); // 200
}

[HttpPost]
public ActionResult<Usuario> Create(Usuario usuario)
{
    if (! ModelState.IsValid)
        return BadRequest(ModelState); // 400

    _usuarios.Add(usuario);
    return CreatedAtAction(nameof(GetById), new { id = usuario.Id }, usuario); // 201
}
```

---

## Manejo de Errores (Error Handling)

Es fundamental proporcionar **mensajes de error claros** y códigos HTTP apropiados. 

**Ejemplo de manejo de errores:**

```csharp
[HttpGet("{id}")]
public ActionResult<Usuario> GetById(int id)
{
    var usuario = _usuarios.FirstOrDefault(u => u.Id == id);
    
    if (usuario == null)
    {
        return NotFound(new 
        { 
            error = "Usuario no encontrado",
            timestamp = DateTime. UtcNow,
            path = $"/api/usuarios/{id}"
        });
    }

    return Ok(usuario);
}
```

---

## Versionado de APIs

Es recomendable **versionar la API** para permitir cambios sin romper clientes existentes.

**Métodos de versionado:**

1. **URL**: `/api/v1/usuarios`, `/api/v2/usuarios`
2. **Query String**: `/api/usuarios?version=1`
3. **Header**: `Accept: application/vnd.myapi.v1+json`

**Ejemplo con versionado en URL:**

```csharp
[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("1.0")]
public class UsuariosV1Controller : ControllerBase
{
    // Endpoints versión 1
}

[ApiController]
[Route("api/v{version:apiVersion}/[controller]")]
[ApiVersion("2.0")]
public class UsuariosV2Controller : ControllerBase
{
    // Endpoints versión 2 con cambios
}
```

---

## Ejemplo de diseño de acceso a un recurso

| Endpoint              | Método HTTP | Body                                                  | Código Respuesta | Body Respuesta                                                 | Posibles Errores |
| :-------------------- | :---------- | :---------------------------------------------------- | :--------------- | :------------------------------------------------------------- | :--------------- |
| `/api/productos`      | GET         | N/A                                                   | 200 OK           | `[{"id": 1, "nombre": "Producto 1", "precio": 10.99}, {... }]` | -                |
| `/api/productos`      | POST        | `{"nombre": "Producto Nuevo", "precio": 15.99}`       | 201 Created      | `{"id": 3, "nombre": "Producto Nuevo", "precio": 15.99}`       | 400, 422         |
| `/api/productos/{id}` | GET         | N/A                                                   | 200 OK           | `{"id": 1, "nombre": "Producto 1", "precio": 10.99}`           | 404              |
| `/api/productos/{id}` | PUT         | `{"nombre": "Producto Actualizado", "precio": 12.99}` | 200 OK           | `{"id": 1, "nombre": "Producto Actualizado", "precio": 12.99}` | 400, 404, 422    |
| `/api/productos/{id}` | PATCH       | `{"precio": 12.99}`                                   | 200 OK           | `{"id": 1, "nombre": "Producto 1", "precio": 12.99}`           | 400, 404, 422    |
| `/api/productos/{id}` | DELETE      | N/A                                                   | 204 No Content   | N/A                                                            | 404              |

---

## Práctica de clase

**Objetivo**: Diseñar una API REST completa para gestionar **Funkos** (figuras coleccionables).

**Tareas:**

1. Diseña los **endpoints completos** para realizar un **CRUD** sobre el recurso `Funkos`.
2. Para cada endpoint, especifica:
   - **Método HTTP** (GET, POST, PUT, PATCH, DELETE)
   - **URL del endpoint**
   - **Body de la petición** (si aplica)
   - **Código de respuesta HTTP** (200, 201, 204, 400, 404, etc.)
   - **Body de la respuesta** (ejemplo en JSON)
   - **Posibles errores** (códigos y mensajes)

**Modelo de datos del Funko:**

```json
{
  "id": 1,
  "nombre": "Funko Pop!  Batman",
  "categoria": "DC Comics",
  "precio": 15.99,
  "stock":  50,
  "fechaLanzamiento": "2023-01-15"
}
```

**Ejemplo de tabla a completar:**

| Endpoint      | Método HTTP | Body                       | Código Respuesta | Body Respuesta                                 | Posibles Errores |
| :------------ | :---------- | :------------------------- | :--------------- | :--------------------------------------------- | :--------------- |
| `/api/funkos` | GET         | N/A                        | 200 OK           | `[{"id": 1, "nombre": "Batman", ... }, {...}]` | -                |
| `/api/funkos` | POST        | `{"nombre": ".. .", ... }` | 201 Created      | `{"id": 2, "nombre": ".. .", ...}`             | 400, 422         |
| ...           | ...         | ...                        | ...              | ...                                            | ...              |

---

