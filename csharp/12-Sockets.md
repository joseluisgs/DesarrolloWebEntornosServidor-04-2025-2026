# Programación Distribuida con Sockets en . NET

---

- [Programación Distribuida con Sockets en . NET](#programación-distribuida-con-sockets-en--net)
  - [Introducción a Sockets](#introducción-a-sockets)
  - [Sockets TCP en . NET](#sockets-tcp-en--net)
  - [Ejemplo completo: Servidor y Cliente](#ejemplo-completo-servidor-y-cliente)
  - [Servidor multihilo con TcpListener](#servidor-multihilo-con-tcplistener)
  - [Cliente asíncrono](#cliente-asíncrono)
  - [Protocolo de comunicación estructurado](#protocolo-de-comunicación-estructurado)
  - [Sockets UDP](#sockets-udp)
  - [WebSockets en .NET](#websockets-en-net)

---

## Introducción a Sockets

Los **sockets** son puntos finales de comunicación bidireccional entre dos programas que se ejecutan en una red. Permiten la comunicación entre aplicaciones utilizando **TCP/IP**. 

![Socket Programming](../images/socket-programming. png)

**Elementos principales:**

1. **Socket**: Punto final de una conexión de red (enviar y recibir datos).
2. **Servidor (TcpListener/Socket)**: Escucha conexiones entrantes en un puerto específico.
3. **Cliente (TcpClient/Socket)**: Inicia la conexión con el servidor.
4. **NetworkStream**: Flujo de datos para leer y escribir a través del socket. 

**Tipos de sockets:**

- **TCP (Transmission Control Protocol)**: Orientado a conexión, garantiza entrega ordenada y sin pérdidas.
- **UDP (User Datagram Protocol)**: Sin conexión, más rápido pero no garantiza entrega.

---

## Sockets TCP en . NET

. NET proporciona varias clases para trabajar con sockets: 

- **`TcpListener`**: Para crear servidores TCP. 
- **`TcpClient`**: Para crear clientes TCP.
- **`Socket`**: Clase de bajo nivel para control total. 
- **`NetworkStream`**: Para leer/escribir datos.

---

## Ejemplo completo: Servidor y Cliente

**Servidor básico:**

```csharp
using System.Net;
using System.Net. Sockets;
using System. Text;

public class ServidorBasico
{
    public static async Task Main()
    {
        var listener = new TcpListener(IPAddress.Any, 12345);
        listener.Start();
        Console.WriteLine("🚀 Servidor iniciado en puerto 12345");
        Console.WriteLine("Esperando conexiones...\n");

        while (true)
        {
            // Aceptar conexión del cliente
            var client = await listener.AcceptTcpClientAsync();
            var clientEndpoint = client.Client.RemoteEndPoint;
            Console.WriteLine($"✅ Cliente conectado: {clientEndpoint}");

            // Manejar cliente (de forma síncrona en este ejemplo simple)
            await ManejarClienteAsync(client);
        }
    }

    private static async Task ManejarClienteAsync(TcpClient client)
    {
        using var stream = client.GetStream();
        using var reader = new StreamReader(stream, Encoding.UTF8);
        using var writer = new StreamWriter(stream, Encoding.UTF8) { AutoFlush = true };

        try
        {
            string?  mensaje;
            while ((mensaje = await reader.ReadLineAsync()) != null)
            {
                if (mensaje.ToLower() == "salir")
                {
                    await writer.WriteLineAsync("Servidor: ¡Adiós!");
                    break;
                }

                Console.WriteLine($"📩 Mensaje recibido: {mensaje}");
                
                // Responder al cliente
                var respuesta = $"Servidor: {mensaje.ToUpper()}";
                await writer.WriteLineAsync(respuesta);
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"❌ Error:  {ex.Message}");
        }
        finally
        {
            client.Close();
            Console.WriteLine("🔌 Cliente desconectado\n");
        }
    }
}
```

**Cliente básico:**

```csharp
using System.Net. Sockets;
using System. Text;

public class ClienteBasico
{
    public static async Task Main()
    {
        using var client = new TcpClient();
        
        try
        {
            await client.ConnectAsync("localhost", 12345);
            Console.WriteLine("✅ Conectado al servidor\n");

            using var stream = client.GetStream();
            using var reader = new StreamReader(stream, Encoding.UTF8);
            using var writer = new StreamWriter(stream, Encoding.UTF8) { AutoFlush = true };

            // Enviar mensajes
            await EnviarMensajeAsync(writer, reader, "Hola, servidor!");
            await EnviarMensajeAsync(writer, reader, "¿Qué tal estás?");
            await EnviarMensajeAsync(writer, reader, "salir");
        }
        catch (Exception ex)
        {
            Console.WriteLine($"❌ Error: {ex.Message}");
        }
    }

    private static async Task EnviarMensajeAsync(StreamWriter writer, StreamReader reader, string mensaje)
    {
        Console.WriteLine($"📤 Enviando: {mensaje}");
        await writer.WriteLineAsync(mensaje);

        var respuesta = await reader.ReadLineAsync();
        Console.WriteLine($"📥 Respuesta:  {respuesta}\n");
    }
}
```

---

## Servidor multihilo con TcpListener

Un servidor que maneja múltiples clientes simultáneamente:

```csharp
using System. Collections.Concurrent;
using System. Net;
using System.Net. Sockets;
using System. Text;

public class ServidorMultihilo
{
    private static readonly ConcurrentDictionary<string, TcpClient> _clientes = new();
    private static int _contadorClientes = 0;

    public static async Task Main()
    {
        var listener = new TcpListener(IPAddress. Any, 12345);
        listener.Start();
        
        Console.WriteLine("🚀 Servidor multihilo iniciado");
        Console.WriteLine("Esperando conexiones...\n");

        while (true)
        {
            var client = await listener.AcceptTcpClientAsync();
            var clientId = $"Cliente-{Interlocked.Increment(ref _contadorClientes)}";
            
            _clientes.TryAdd(clientId, client);
            
            Console.WriteLine($"✅ {clientId} conectado ({client.Client.RemoteEndPoint})");
            Console.WriteLine($"📊 Clientes activos: {_clientes.Count}\n");

            // Crear tarea para manejar cliente de forma concurrente
            _ = Task.Run(() => ManejarClienteAsync(clientId, client));
        }
    }

    private static async Task ManejarClienteAsync(string clientId, TcpClient client)
    {
        using var stream = client.GetStream();
        using var reader = new StreamReader(stream, Encoding.UTF8);
        using var writer = new StreamWriter(stream, Encoding. UTF8) { AutoFlush = true };

        try
        {
            // Enviar mensaje de bienvenida
            await writer.WriteLineAsync($"Bienvenido!  Tu ID es: {clientId}");

            string? mensaje;
            while ((mensaje = await reader.ReadLineAsync()) != null)
            {
                Console.WriteLine($"[{DateTime.Now:HH:mm:ss}] {clientId}:  {mensaje}");

                if (mensaje.ToLower() == "salir")
                {
                    await writer.WriteLineAsync("¡Hasta luego!");
                    break;
                }

                // Broadcast a todos los clientes
                if (mensaje.StartsWith("/broadcast "))
                {
                    var contenido = mensaje.Substring(11);
                    await BroadcastMensajeAsync($"{clientId}: {contenido}", clientId);
                    await writer.WriteLineAsync("✅ Mensaje enviado a todos");
                }
                else
                {
                    // Responder solo a este cliente
                    await writer.WriteLineAsync($"Servidor: {mensaje.ToUpper()}");
                }
            }
        }
        catch (Exception ex)
        {
            Console. WriteLine($"❌ Error con {clientId}: {ex.Message}");
        }
        finally
        {
            _clientes. TryRemove(clientId, out _);
            client.Close();
            Console.WriteLine($"🔌 {clientId} desconectado");
            Console.WriteLine($"📊 Clientes activos: {_clientes.Count}\n");
        }
    }

    private static async Task BroadcastMensajeAsync(string mensaje, string remitenteId)
    {
        var tareas = _clientes
            .Where(kvp => kvp.Key != remitenteId)
            .Select(async kvp =>
            {
                try
                {
                    using var writer = new StreamWriter(kvp. Value.GetStream(), Encoding.UTF8) 
                    { 
                        AutoFlush = true 
                    };
                    await writer.WriteLineAsync($"[BROADCAST] {mensaje}");
                }
                catch
                {
                    // Cliente desconectado, se limpiará en su propio handler
                }
            });

        await Task.WhenAll(tareas);
    }
}
```

---

## Cliente asíncrono

Cliente que puede enviar y recibir mensajes simultáneamente:

```csharp
using System.Net. Sockets;
using System.Text;

public class ClienteAsincrono
{
    public static async Task Main()
    {
        using var client = new TcpClient();
        
        try
        {
            await client.ConnectAsync("localhost", 12345);
            Console.WriteLine("✅ Conectado al servidor");
            Console.WriteLine("Comandos:  /broadcast <mensaje>, salir\n");

            using var stream = client.GetStream();
            using var reader = new StreamReader(stream, Encoding.UTF8);
            using var writer = new StreamWriter(stream, Encoding.UTF8) { AutoFlush = true };

            // Tarea para recibir mensajes del servidor
            var tareaRecepcion = Task.Run(async () =>
            {
                try
                {
                    string? mensaje;
                    while ((mensaje = await reader.ReadLineAsync()) != null)
                    {
                        Console.WriteLine($"\n📥 {mensaje}");
                        Console.Write("Tú> ");
                    }
                }
                catch (Exception ex)
                {
                    Console.WriteLine($"\n❌ Error de recepción: {ex.Message}");
                }
            });

            // Enviar mensajes desde la consola
            while (true)
            {
                Console.Write("Tú> ");
                var mensaje = Console.ReadLine();

                if (string.IsNullOrWhiteSpace(mensaje))
                    continue;

                await writer.WriteLineAsync(mensaje);

                if (mensaje.ToLower() == "salir")
                    break;
            }

            await tareaRecepcion;
        }
        catch (Exception ex)
        {
            Console.WriteLine($"❌ Error: {ex.Message}");
        }
    }
}
```

---

## Protocolo de comunicación estructurado

Para aplicaciones más complejas, es mejor usar un protocolo estructurado:

```csharp
using System.Text. Json;

// Mensajes del protocolo
public record Mensaje(
    string Tipo,
    string Contenido,
    DateTime Timestamp,
    string?  Remitente = null
);

public enum TipoMensaje
{
    Texto,
    Comando,
    Sistema,
    Error
}

public class ServidorConProtocolo
{
    public static async Task Main()
    {
        var listener = new TcpListener(IPAddress.Any, 12345);
        listener.Start();
        Console.WriteLine("🚀 Servidor con protocolo JSON iniciado\n");

        while (true)
        {
            var client = await listener.AcceptTcpClientAsync();
            _ = Task.Run(() => ManejarClienteAsync(client));
        }
    }

    private static async Task ManejarClienteAsync(TcpClient client)
    {
        using var stream = client.GetStream();
        using var reader = new StreamReader(stream, Encoding.UTF8);
        using var writer = new StreamWriter(stream, Encoding.UTF8) { AutoFlush = true };

        try
        {
            // Enviar mensaje de bienvenida
            await EnviarMensajeAsync(writer, new Mensaje(
                Tipo: "Sistema",
                Contenido: "Bienvenido al servidor",
                Timestamp: DateTime. UtcNow
            ));

            string? linea;
            while ((linea = await reader.ReadLineAsync()) != null)
            {
                var mensajeRecibido = JsonSerializer.Deserialize<Mensaje>(linea);
                
                if (mensajeRecibido == null)
                    continue;

                Console.WriteLine($"📩 [{mensajeRecibido. Tipo}] {mensajeRecibido.Contenido}");

                // Procesar según el tipo
                var respuesta = mensajeRecibido.Tipo switch
                {
                    "Comando" => ProcesarComando(mensajeRecibido. Contenido),
                    "Texto" => new Mensaje(
                        Tipo: "Respuesta",
                        Contenido: mensajeRecibido.Contenido. ToUpper(),
                        Timestamp: DateTime.UtcNow,
                        Remitente:  "Servidor"
                    ),
                    _ => new Mensaje(
                        Tipo: "Error",
                        Contenido: "Tipo de mensaje no reconocido",
                        Timestamp: DateTime. UtcNow
                    )
                };

                await EnviarMensajeAsync(writer, respuesta);

                if (mensajeRecibido. Contenido.ToLower() == "salir")
                    break;
            }
        }
        catch (Exception ex)
        {
            Console.WriteLine($"❌ Error: {ex.Message}");
        }
        finally
        {
            client.Close();
        }
    }

    private static async Task EnviarMensajeAsync(StreamWriter writer, Mensaje mensaje)
    {
        var json = JsonSerializer.Serialize(mensaje);
        await writer.WriteLineAsync(json);
    }

    private static Mensaje ProcesarComando(string comando)
    {
        return comando. ToLower() switch
        {
            "hora" => new Mensaje(
                Tipo: "Respuesta",
                Contenido:  $"Hora del servidor: {DateTime.Now:HH:mm:ss}",
                Timestamp: DateTime. UtcNow
            ),
            "info" => new Mensaje(
                Tipo: "Respuesta",
                Contenido: "Servidor v1.0 - . NET 8",
                Timestamp: DateTime. UtcNow
            ),
            _ => new Mensaje(
                Tipo: "Error",
                Contenido: "Comando no reconocido",
                Timestamp: DateTime.UtcNow
            )
        };
    }
}

// Cliente con protocolo
public class ClienteConProtocolo
{
    public static async Task Main()
    {
        using var client = new TcpClient();
        await client.ConnectAsync("localhost", 12345);

        using var stream = client.GetStream();
        using var reader = new StreamReader(stream, Encoding.UTF8);
        using var writer = new StreamWriter(stream, Encoding.UTF8) { AutoFlush = true };

        // Recibir bienvenida
        var bienvenida = await RecibirMensajeAsync(reader);
        Console.WriteLine($"📥 {bienvenida?. Contenido}\n");

        // Enviar mensajes
        await EnviarMensajeAsync(writer, new Mensaje("Texto", "Hola servidor", DateTime.UtcNow));
        var respuesta = await RecibirMensajeAsync(reader);
        Console.WriteLine($"📥 {respuesta?.Contenido}");

        await EnviarMensajeAsync(writer, new Mensaje("Comando", "hora", DateTime.UtcNow));
        respuesta = await RecibirMensajeAsync(reader);
        Console.WriteLine($"📥 {respuesta?. Contenido}");

        await EnviarMensajeAsync(writer, new Mensaje("Texto", "salir", DateTime.UtcNow));
    }

    private static async Task EnviarMensajeAsync(StreamWriter writer, Mensaje mensaje)
    {
        var json = JsonSerializer.Serialize(mensaje);
        Console.WriteLine($"📤 Enviando: {mensaje.Contenido}");
        await writer.WriteLineAsync(json);
    }

    private static async Task<Mensaje?> RecibirMensajeAsync(StreamReader reader)
    {
        var json = await reader.ReadLineAsync();
        return json != null ? JsonSerializer.Deserialize<Mensaje>(json) : null;
    }
}
```

---

## Sockets UDP

UDP es más rápido pero no garantiza entrega:

```csharp
using System.Net;
using System.Net.Sockets;
using System.Text;

// Servidor UDP
public class ServidorUdp
{
    public static async Task Main()
    {
        using var udpClient = new UdpClient(12345);
        Console.WriteLine("🚀 Servidor UDP iniciado en puerto 12345\n");

        while (true)
        {
            var resultado = await udpClient.ReceiveAsync();
            var mensaje = Encoding.UTF8.GetString(resultado.Buffer);
            var clientEndpoint = resultado.RemoteEndPoint;

            Console.WriteLine($"📩 Mensaje de {clientEndpoint}: {mensaje}");

            // Responder al cliente
            var respuesta = Encoding.UTF8.GetBytes($"Servidor: {mensaje.ToUpper()}");
            await udpClient.SendAsync(respuesta, clientEndpoint);
        }
    }
}

// Cliente UDP
public class ClienteUdp
{
    public static async Task Main()
    {
        using var udpClient = new UdpClient();
        var serverEndpoint = new IPEndPoint(IPAddress. Loopback, 12345);

        // Enviar mensaje
        var mensaje = "Hola, servidor UDP! ";
        var datos = Encoding.UTF8.GetBytes(mensaje);
        await udpClient.SendAsync(datos, serverEndpoint);
        Console.WriteLine($"📤 Enviado:  {mensaje}");

        // Recibir respuesta
        var resultado = await udpClient.ReceiveAsync();
        var respuesta = Encoding.UTF8.GetString(resultado.Buffer);
        Console.WriteLine($"📥 Respuesta: {respuesta}");
    }
}
```

---

## WebSockets en .NET

Para comunicación bidireccional en tiempo real en aplicaciones web:

```csharp
using System.Net. WebSockets;
using System. Text;

// Servidor WebSocket (en ASP.NET Core)
public class Startup
{
    public void Configure(IApplicationBuilder app)
    {
        app.UseWebSockets();

        app.Use(async (context, next) =>
        {
            if (context.Request.Path == "/ws")
            {
                if (context.WebSockets.IsWebSocketRequest)
                {
                    using var webSocket = await context.WebSockets.AcceptWebSocketAsync();
                    await ManejarWebSocketAsync(webSocket);
                }
                else
                {
                    context.Response.StatusCode = 400;
                }
            }
            else
            {
                await next();
            }
        });
    }

    private async Task ManejarWebSocketAsync(WebSocket webSocket)
    {
        var buffer = new byte[1024 * 4];

        while (webSocket.State == WebSocketState.Open)
        {
            var resultado = await webSocket.ReceiveAsync(
                new ArraySegment<byte>(buffer),
                CancellationToken. None
            );

            if (resultado.MessageType == WebSocketMessageType.Close)
            {
                await webSocket.CloseAsync(
                    WebSocketCloseStatus. NormalClosure,
                    "Cerrando",
                    CancellationToken.None
                );
            }
            else
            {
                var mensaje = Encoding.UTF8.GetString(buffer, 0, resultado.Count);
                Console. WriteLine($"📩 Mensaje recibido: {mensaje}");

                // Echo de vuelta al cliente
                await webSocket.SendAsync(
                    new ArraySegment<byte>(buffer, 0, resultado.Count),
                    resultado.MessageType,
                    resultado.EndOfMessage,
                    CancellationToken.None
                );
            }
        }
    }
}
```

---

