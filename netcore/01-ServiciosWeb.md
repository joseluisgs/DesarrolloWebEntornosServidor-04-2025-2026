# Servicios Web en . NET

- [Servicios Web en . NET](#servicios-web-en--net)
  - [Servicios orientados a la conexión vs No orientados a la conexión](#servicios-orientados-a-la-conexión-vs-no-orientados-a-la-conexión)
    - [Comparación:  Servicios orientados a la conexión vs Sin conexión](#comparación--servicios-orientados-a-la-conexión-vs-sin-conexión)
  - [Servicios Web](#servicios-web)
    - [El protocolo HTTP y sus ventajas](#el-protocolo-http-y-sus-ventajas)
    - [Características clave de HTTP:](#características-clave-de-http)
  - [Componentes de un Servicio Web](#componentes-de-un-servicio-web)
    - [Request (Solicitud)](#request-solicitud)
    - [Response (Respuesta)](#response-respuesta)
    - [Ejemplos de Servicios Web](#ejemplos-de-servicios-web)
    - [Arquitecturas de Servicios Web](#arquitecturas-de-servicios-web)
  - [Práctica de clase](#práctica-de-clase)

![banner](../images/banner01.png)

---

## Servicios orientados a la conexión vs No orientados a la conexión

Un **servicio** es una funcionalidad que se ofrece a través de una interfaz (API) y que puede ser utilizada por otros componentes de software, ya sea para acceder a datos, manejar recursos, etc. 

Los servicios pueden ser **orientados a la conexión** o **sin conexión** (connectionless), y cada enfoque tiene características distintas:

- **Servicios orientados a la conexión**:  Requieren que el cliente y el servidor establezcan una conexión antes de que pueda comenzar la comunicación (similar a una llamada telefónica o FTP). Esta conexión se mantiene activa hasta que se cierra explícitamente.

- **Servicios sin conexión**: No requieren una conexión establecida previamente. Los mensajes se envían y reciben de forma independiente (como el correo electrónico o las páginas web). Por ejemplo, cuando un cliente solicita una página web, el servidor responde y luego se desconecta.  Este proceso se repite para cada nueva solicitud.

![Servicios Web](../images/webservice.png)

---

### Comparación:  Servicios orientados a la conexión vs Sin conexión

**Servicios orientados a la conexión:**

**Pros:**

1. **Fiabilidad**: Los datos se entregan en el orden correcto y sin errores, ya que cada paquete enviado necesita ser reconocido por el receptor (ej: TCP).
2. **Control de Flujo**: Previene que el remitente sobrecargue al receptor con demasiados datos. 
3. **Control de Congestión**: Ayuda a prevenir problemas cuando la red está saturada. 

**Contras:**

1. **Tiempo de Configuración**: Requiere un tiempo inicial para establecer la conexión antes de enviar datos.
2. **Overhead**: Mayor sobrecarga debido a la necesidad de enviar y recibir confirmaciones (ACKs) para cada paquete.
3. **Menos Eficiente para Datos Pequeños**: Para mensajes individuales o datos pequeños, el costo de establecer y cerrar una conexión puede superar los beneficios.

---

**Servicios No Orientados a la Conexión:**

**Pros:**

1. **Rapidez**: Los datos se pueden enviar inmediatamente sin necesidad de establecer una conexión previa (ej: UDP, HTTP).
2. **Eficiencia para Datos Pequeños**: Ideal para enviar mensajes individuales sin overhead de configuración.
3. **Resiliencia**: Más resistentes a problemas de red, ya que no dependen de una conexión constante.

**Contras:**

1. **Menos Fiabilidad**: No garantizan que los datos se entreguen en orden o sin errores.
2. **Sin Control de Flujo o Congestión**:  Pueden provocar problemas si el receptor no puede manejar la cantidad de datos o si la red está sobrecargada.
3. **Requiere Mayor Gestión de Errores**: Al no haber garantías de entrega, es necesario implementar mecanismos adicionales para el manejo de errores.

---

## Servicios Web

Los **servicios web** son sistemas de software diseñados para permitir la **interacción interoperable** entre máquinas a través de una red, utilizando principalmente el protocolo **HTTP**.

Proporcionan una forma estandarizada de integrar aplicaciones basadas en web usando formatos como **XML**, **JSON**, y protocolos como **SOAP**, **REST**, **GraphQL**, **gRPC**, etc.

**Ventajas de los servicios web:**

✅ **Interoperabilidad**: Permiten la comunicación entre diferentes aplicaciones, sistemas operativos y lenguajes de programación. 

✅ **Reducción de costos**: Facilitan la integración de sistemas sin necesidad de desarrollar interfaces personalizadas.

✅ **Escalabilidad**: Pueden manejar un gran número de solicitudes simultáneas (especialmente con arquitecturas stateless como REST).

**Desventajas:**

❌ **Complejidad**:  Algunos enfoques (como SOAP) pueden ser complejos de implementar. 

❌ **Seguridad**: Requieren medidas adicionales (HTTPS, autenticación JWT, OAuth) para proteger los datos transmitidos.

---

### El protocolo HTTP y sus ventajas

**HTTP (Hypertext Transfer Protocol)** es la base de cualquier intercambio de datos en la Web. Es un protocolo **sin estado** (stateless) y **basado en texto**. 

**Ventajas de usar HTTP en servicios web:**

1. **Interoperabilidad**: HTTP es universalmente aceptado.  Los servicios web que lo usan pueden interactuar con cualquier cliente o servidor, independientemente del lenguaje, plataforma o sistema operativo.

2. **Simplicidad**: HTTP es fácil de usar y entender.  Los mensajes HTTP son legibles por humanos, lo que facilita la depuración. 

3. **Compatibilidad con REST**: HTTP es el protocolo subyacente de las APIs RESTful, que son simples, escalables y eficientes.

4. **Seguridad**: A través de **HTTPS**, HTTP proporciona cifrado de datos en tránsito, protegiendo información sensible.

5. **Caching**: HTTP soporta almacenamiento en caché, mejorando el rendimiento al reutilizar respuestas previas.

6. **Soporte de Sesión y Cookies**: Aunque HTTP es stateless, se pueden usar cookies y sesiones para mantener el estado entre solicitudes (útil para autenticación y seguimiento de usuarios).

7. **Escalabilidad**: Su naturaleza sin estado permite manejar un gran número de solicitudes simultáneas de forma eficiente.

---

### Características clave de HTTP: 

**Arquitectura Cliente-Servidor:**

- El **cliente** (navegador, aplicación móvil, Postman, etc.) realiza una **solicitud HTTP**.
- El **servidor** (ASP.NET Core Web API, por ejemplo) procesa la solicitud y devuelve una **respuesta HTTP**.

**Ejemplo de flujo:**

1. Cliente envía:  `GET /api/usuarios/1` 
2. Servidor responde: `200 OK` con los datos del usuario en JSON. 

**Sin Estado (Stateless):**

- Cada solicitud HTTP se procesa de forma **independiente**.
- El servidor **no mantiene información** sobre solicitudes anteriores.
- Esto simplifica la escalabilidad, pero requiere mecanismos adicionales (como JWT o cookies) para mantener el estado del usuario en aplicaciones que lo necesiten.

---

## Componentes de un Servicio Web

La comunicación entre cliente y servidor se basa en **peticiones (requests)** y **respuestas (responses)** enviadas a través de HTTP.

![Request-Response](../images/request-response.png)

---

### Request (Solicitud)

Un **request** o **solicitud HTTP** es un mensaje que un cliente envía a un servidor para solicitar una acción específica. 

**Componentes de una solicitud HTTP:**

1. **Método HTTP**: Indica la acción a realizar. 
   - `GET`: Obtener un recurso. 
   - `POST`: Crear un nuevo recurso.
   - `PUT`: Actualizar un recurso completo.
   - `PATCH`: Actualizar parcialmente un recurso.
   - `DELETE`: Eliminar un recurso. 
   - `HEAD`: Obtener solo los encabezados (sin cuerpo).
   - `OPTIONS`: Obtener los métodos HTTP soportados.

2. **URL (Uniform Resource Locator)**: Especifica el recurso solicitado.
   - Ejemplo: `https://api.example.com/api/usuarios/1`

3. **Encabezados (Headers)**: Proporcionan información adicional sobre la solicitud.
   - `Content-Type`: Tipo de contenido enviado (ej: `application/json`).
   - `Authorization`: Token de autenticación (ej:  `Bearer <JWT>`).
   - `Accept`: Tipo de contenido que el cliente puede aceptar. 
   - `User-Agent`: Información sobre el cliente.

4. **Cuerpo (Body)**: Contiene los datos enviados al servidor (en solicitudes POST, PUT, PATCH).
   - Ejemplo (JSON):
     ```json
     {
       "nombre": "Juan",
       "email": "juan@example.com"
     }
     ```

**Ejemplo de solicitud HTTP:**

```http
POST /api/usuarios HTTP/1.1
Host: api.example.com
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9... 

{
  "nombre": "Juan Pérez",
  "email":  "juan@example.com"
}
```

---

### Response (Respuesta)

Un **response** o **respuesta HTTP** es un mensaje que el servidor envía al cliente en respuesta a una solicitud. 

**Componentes de una respuesta HTTP:**

1. **Código de Estado (Status Code)**: Indica el resultado de la solicitud.
   - **2xx - Éxito**:
     - `200 OK`: Solicitud exitosa.
     - `201 Created`: Recurso creado exitosamente. 
     - `204 No Content`: Solicitud exitosa sin contenido de respuesta.
   - **4xx - Errores del Cliente**:
     - `400 Bad Request`: Solicitud mal formada.
     - `401 Unauthorized`: Autenticación requerida.
     - `403 Forbidden`: Sin permisos. 
     - `404 Not Found`: Recurso no encontrado.
   - **5xx - Errores del Servidor**:
     - `500 Internal Server Error`: Error genérico del servidor.
     - `503 Service Unavailable`: Servicio no disponible temporalmente.

2. **Encabezados (Headers)**: Información adicional sobre la respuesta. 
   - `Content-Type`: Tipo de contenido del cuerpo (ej: `application/json`).
   - `Cache-Control`: Control de caché.
   - `Set-Cookie`: Establecer cookies. 

3. **Cuerpo (Body)**: Contiene los datos solicitados (si están disponibles).
   - Ejemplo (JSON):
     ```json
     {
       "id": 1,
       "nombre": "Juan Pérez",
       "email":  "juan@example.com"
     }
     ```

**Ejemplo de respuesta HTTP:**

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

### Ejemplos de Servicios Web

Algunos ejemplos populares de servicios web: 

- **API de Google Maps**: Permite acceder a datos de mapas y geolocalización.
- **API de Twitter (X)**: Permite interactuar con la plataforma (publicar tweets, leer perfiles, etc.).
- **API de Stripe**: Para procesar pagos. 
- **API de OpenWeather**: Para obtener datos meteorológicos.
- **API de GitHub**: Para interactuar con repositorios y usuarios de GitHub. 

---

### Arquitecturas de Servicios Web

Existen diferentes arquitecturas de servicios web, cada una con sus propios protocolos y estándares:

![APIs](../images/apis.gif)

---

**1.  SOAP (Simple Object Access Protocol)**

**Características:**
- Protocolo basado en **XML** para el intercambio de mensajes.
- Independiente del lenguaje y del protocolo de transporte (HTTP, SMTP, TCP).
- Usa **WSDL (Web Services Description Language)** para describir la funcionalidad del servicio. 

**Pros:**
- Altamente extensible y flexible.
- Soporta operaciones complejas (transacciones, seguridad avanzada).
- Alto nivel de seguridad (WS-Security).

**Contras:**
- Más lento y consume más recursos debido a su verbosidad.
- Complejo de implementar y aprender. 

**Ejemplo de uso:** Sistemas empresariales (ERP, CRM, sistemas financieros).

---

**2. REST (Representational State Transfer)**

**Características:**
- Estilo arquitectónico que usa **HTTP** y sus métodos (GET, POST, PUT, DELETE).
- Utiliza **JSON** (o XML) para el intercambio de datos.
- Stateless (sin estado).

**Pros:**
- Simple de entender e implementar.
- Eficiente y rápido.
- Soporta caché.

**Contras:**
- Menos seguro que SOAP (requiere HTTPS y JWT).
- No adecuado para operaciones muy complejas.

**Ejemplo de uso:** APIs públicas (Twitter, GitHub, Stripe), aplicaciones móviles y web.

**Ejemplo en ASP.NET Core:**

```csharp
[ApiController]
[Route("api/[controller]")]
public class UsuariosController : ControllerBase
{
    [HttpGet("{id}")]
    public IActionResult GetUsuario(int id)
    {
        var usuario = new { Id = id, Nombre = "Juan", Email = "juan@example.com" };
        return Ok(usuario);
    }
}
```

---

**3. GraphQL**

**Características:**
- Lenguaje de consulta para APIs que permite a los clientes **especificar exactamente qué datos necesitan**.
- Un solo endpoint maneja todas las solicitudes. 

**Pros:**
- Evita over-fetching y under-fetching de datos.
- Más eficiente que REST en algunos escenarios. 

**Contras:**
- Más complejo de implementar.
- Menor adopción que REST.

**Ejemplo de uso:** Facebook, GitHub API v4. 

---

**4. gRPC (Google Remote Procedure Call)**

**Características:**
- Protocolo moderno que usa **HTTP/2** y **Protocol Buffers** (binario).
- Comunicación bidireccional y en tiempo real. 

**Pros:**
- Muy rápido (binario).
- Soporta streaming. 

**Contras:**
- Menos legible por humanos.
- Menor compatibilidad con navegadores.

**Ejemplo de uso:** Microservicios internos, sistemas de alto rendimiento.

---

**5. WebSockets**

**Características:**
- Protocolo que proporciona **comunicación bidireccional y en tiempo real** sobre una **conexión persistente**. 

**Pros:**
- Comunicación en tiempo real.
- Baja latencia.

**Contras:**
- Consume más recursos (conexión siempre abierta).
- No soportado por todos los navegadores/servidores antiguos.

**Ejemplo de uso:** Chat en vivo, juegos online, dashboards en tiempo real.

**Ejemplo en ASP.NET Core con SignalR:**

```csharp
public class ChatHub : Hub
{
    public async Task SendMessage(string user, string message)
    {
        await Clients. All.SendAsync("ReceiveMessage", user, message);
    }
}
```

---

## Práctica de clase

**Objetivo:** Analizar y citar distintos servicios web que uses diariamente, ya sea directamente o a través de aplicaciones que los consuman.

**Tareas:**

1. Identifica **al menos 3 servicios web** que uses (ej: WhatsApp, Spotify, Google Maps, Instagram, etc.).

2. Para cada servicio, investiga y documenta:
   - **Recursos y endpoints** que podrían usar (ej: `/api/users`, `/api/messages`).
   - **Métodos HTTP** utilizados (GET, POST, PUT, DELETE).
   - **Respuestas** típicas (códigos de estado, formato JSON/XML).
   - **Errores** comunes que devuelven (404, 401, 500, etc.).

3. Presenta tu análisis en un documento o presentación.

**Ejemplo:**

| Servicio | Recurso/Endpoint | Método HTTP | Respuesta Exitosa | Errores Posibles |
|: ---------|:-----------------|:------------|:------------------|:-----------------|
| Spotify  | `/api/tracks`    | GET         | 200 OK + JSON con lista de canciones | 401 Unauthorized, 404 Not Found |
| WhatsApp | `/api/messages`  | POST        | 201 Created + JSON con mensaje enviado | 400 Bad Request, 429 Too Many Requests |

---
