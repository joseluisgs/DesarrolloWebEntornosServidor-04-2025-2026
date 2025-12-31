# Desarrollo Web en Entornos Servidor - 04 - Desarrollo de servicios web en .NET

Tema 02. Desarrollo de servicios web en .NET. 2DAW. Curso 2025-2026.

![imagen](https://github.com/joseluisgs/DesarrolloWebEntornosServidor-00-2023-2024/raw/master/images/servicios.png)
- [Desarrollo Web en Entornos Servidor - 04 - Desarrollo de servicios web en .NET](#desarrollo-web-en-entornos-servidor---04---desarrollo-de-servicios-web-en-net)
- [Contenido en YouTube](#contenido-en-youtube)
  - [C#](#c)
  - [.NET Core](#net-core)
  - [Lista de Reproducción](#lista-de-reproducción)
  - [Introducción](#introducción)
  - [Contenidos I: Lenguaje C# y .NET Core en el lado del Servidor](#contenidos-i-lenguaje-c-y-net-core-en-el-lado-del-servidor)
  - [Contenidos II: Servicios Web con C# .NET y ASP.NET Core](#contenidos-ii-servicios-web-con-c-net-y-aspnet-core)
  - [Proyecto Integrador](#proyecto-integrador)
  - [Proyectos específicos](#proyectos-específicos)
  - [Referencias](#referencias)
  - [Autor](#autor)
    - [Contacto](#contacto)
  - [Licencia de uso](#licencia-de-uso)


# Contenido en YouTube

## C#
- [Podcast C#](https://youtu.be/I3JxVttLWsQ)
- [Resumen C#](https://youtu.be/iG5MR7ulFYE)
- [Sincronía y Asincronía](https://youtu.be/noofA_KPCyU)
- [Programación Reactiva](https://youtu.be/b6xtV5wS-8c)
- [Podcast sobre Asincronía  y Reactividad](https://youtu.be/HQ6Knes3r74)
- [Railway Oriented Programming](https://youtu.be/tT7WkAOyZ08)
- [Refit y APIs REST](https://youtu.be/dFTYRAh2ujY)

## .NET Core
- [Podcast .NET/ASP Core](#)
- [Resumen .NET/ASP Core](#)
- [Construyendo Servicios Web con .NET Core](#)
- [Entity Core Framework y SQL](#)
- [WebSockets con .NET Core](#)
- [NoSQL y Mongo con .NET Core](#)
- [Seguridad: Autenticación y Autorización con .NET Core](#)
- [GraphQL con .NET Core](#) 
- [Caché avanzada con Redis en .NET Core](#)
- [Envío de Emails con .NET Core](#)

## Lista de Reproducción
- [Lista de Reproducción](https://www.youtube.com/watch?v=tlRgLmopS1g&list=PLGIH-7eZDbVzq51Vk4XHAgQ4fTHZVTLRl)

## Introducción
- C# a Java: [Ver](./intro/00-CsharpToJava.md)
- Java a C#: [Ver](./intro/00-JavaToCsharp.md)
- Java a Kotlin: [Ver](./intro/00-JavaToKotlin.md)
- Kotlin a Java: [Ver](./intro/00-KotlinToJava.md)
- Kotlin a C#: [Ver](./intro/00-KotlinToCsharp.md)
- Referencia Rápida: [Ver](./intro/00_ReferenciaRapida.md)
- Fundamentos: [Ver](./intro/00-fundamentos.md)
- Linq y Streams: [Ver](./intro/00-linq-streams.md)
  
## Contenidos I: Lenguaje C# y .NET Core en el lado del Servidor
1. [Programación en C#](./csharp/00-CSharp.md)
2. [Control de Versiones](./csharp/01-ControlVersiones.md)
3. [Gestión de Proyectos con NuGet y .NET CLI](./csharp/02-GestionProyectos.md)
4. [Patrones y Arquitectura: Inyección de Dependencias](./csharp/03-PatronesArquitecturas.md)
5. [Genéricos, Colecciones y LINQ](./csharp/04-TDAColeccionesFuncional.md)
6. [Serialización JSON](./csharp/05-FicherosIntercambio.md)
7. [Acceso a Datos: EF Core](./csharp/06-BBDD.md)
8. [Testing con NUnit](./csharp/07-Testing.md)
9. [Programación Asincrónica](./csharp/08-ConcurrenciaAsincronia.md)
10. [Programación Reactiva](./csharp/09-ProgReactiva.md)
11. [Railway Oriented Programming](./csharp/10-ROP.md)
12. [HTTP Rest con Refit](./csharp/11-HTTP_REST.md)
13. [Sockets](./csharp/12-Sockets.md)
14. [Seguridad y Hash](./csharp/13-Seguridad.md)
15. [Contenedores con Docker](./csharp/14-Docker.md)
16. [Testcontainers](./csharp/15-TestContainers.md)
  
## Contenidos II: Servicios Web con C# .NET y ASP.NET Core
1. [Servicios Web](./netcore/01-ServiciosWeb.md)
2. [APIs REST](./netcore/02-REST.md)
3. [ASP.NET Core](./netcore/03-AspCore.md)
4. [ASP.NET Core MVC](./netcore/04-AspCoreWeb.md)
5. [Servicios](./netcore/05-Servicios.md)
6. [Testing](./netcore/06-Testing.md)
7. [Entity Framework Core y SQL](./netcore/07-EntityCoreSql.md)
8. [Almacenamiento de ficheros en el servidor](./netcore/08-AlmacenamientoFicheros.md)
9.  [WebSockets](./netcore/09-WebSockets.md)
10. [Resultados avanzados](./netcore/10-ResultadosAvazados.md)
11. [Acceso a Datos NoSQL con MongoDB](./netcore/11-NoSQL.md)
12. [Seguridad: Autenticación y Autorización](./netcore/12-Seguridad.md)
13. [Documentación](./netcore/13-Documentacion.md)
14. [Entornos y Perfiles de Configuración](./netcore/14-Perfiles.md)
15. [Publicación y Despliegue](./netcore/15-Despliegue.md)
16. [GraphQL](./netcore/16-GraphQL.md)
17. [Cache con Redis](./netcore/17-Redis.md)
18. [Servicios de mensajería](./netcore/18-Mensajeria.md)
19. [Tareas en segundo plano (Background Services)](./netcore/19-TareasProgramadas.md)

## Proyecto Integrador
El proyecto realizado en clase podrás seguirlo desde el repositorio de GitHub:
- [Proyecto Integrador](https://github.com/joseluisgs/TiendaDawApi-NetCore)

## Proyectos específicos
- [Proyecto EntityFramework](https://github.com/joseluisgs/PersonasBasicRestNet)
- [Proyecto con MongoDB](https://github.com/joseluisgs/ProductosMongoRestNet)
- [Proyecto con Storage](https://github.com/joseluisgs/ProductosStorageMongoRestNet)
- [Proyecto con WebSockets](https://github.com/joseluisgs/ProductosWebsocketMongoRestNet)
- [Proyecto con Seguridad](https://github.com/joseluisgs/ProductosJwtMongoRestNet)


## Referencias
- [Documentación Oficial de .NET](https://learn.microsoft.com/dotnet/)
- [Entity Framework Core](https://learn.microsoft.com/en-us/ef/core/)
- [WebSockets](https://learn.microsoft.com/en-us/aspnet/core/fundamentals/websockets)
- [EF Core con PostgreSQL](https://www.npgsql.org/efcore/)
- [AutoMapper ](https://docs.automapper.org/)
- [FluentValidation](https://www.google.com/search?q=https://docs.fluentvalidation.net/)
- [GraphQL .NET](https://graphql-dotnet.github.io/)
- [Redis](https://stackexchange.github.io/StackExchange.Redis/)
- [MailKit](https://github.com/jstedfast/MailKit)
- [NUnit](https://nunit.org/)
- [Testcontainers](https://dotnet.testcontainers.org/)
- [BCrypt](https://github.com/BcryptNet/bcrypt.net)

## Autor

Codificado con :sparkling_heart: por [José Luis González Sánchez](https://twitter.com/JoseLuisGS_)

[![Twitter](https://img.shields.io/twitter/follow/JoseLuisGS_?style=social)](https://twitter.com/JoseLuisGS_)
[![GitHub](https://img.shields.io/github/followers/joseluisgs?style=social)](https://github.com/joseluisgs)
[![GitHub](https://img.shields.io/github/stars/joseluisgs?style=social)](https://github.com/joseluisgs)

### Contacto

<p>
  Cualquier cosa que necesites házmelo saber por si puedo ayudarte 💬.
</p>
<p>
 <a href="https://joseluisgs.dev" target="_blank">
        <img src="https://joseluisgs.github.io/img/favicon.png" 
    height="30">
    </a>  &nbsp;&nbsp;
    <a href="https://github.com/joseluisgs" target="_blank">
        <img src="https://distreau.com/github.svg" 
    height="30">
    </a> &nbsp;&nbsp;
        <a href="https://twitter.com/JoseLuisGS_" target="_blank">
        <img src="https://i.imgur.com/U4Uiaef.png" 
    height="30">
    </a> &nbsp;&nbsp;
    <a href="https://www.linkedin.com/in/joseluisgonsan" target="_blank">
        <img src="https://upload.wikimedia.org/wikipedia/commons/thumb/c/ca/LinkedIn_logo_initials.png/768px-LinkedIn_logo_initials.png" 
    height="30">
    </a>  &nbsp;&nbsp;
    <a href="https://g.dev/joseluisgs" target="_blank">
        <img loading="lazy" src="https://googlediscovery.com/wp-content/uploads/google-developers.png" 
    height="30">
    </a>  &nbsp;&nbsp;
<a href="https://www.youtube.com/@joseluisgs" target="_blank">
        <img loading="lazy" src="https://upload.wikimedia.org/wikipedia/commons/e/ef/Youtube_logo.png" 
    height="30">
    </a>  
</p>

## Licencia de uso

Este repositorio y todo su contenido está licenciado bajo licencia **Creative Commons**, si desea saber más, vea
la [LICENSE](https://joseluisgs.dev/docs/license/). Por favor si compartes, usas o modificas este proyecto cita a su
autor, y usa las mismas condiciones para su uso docente, formativo o educativo y no comercial.

<a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/"><img alt="Licencia de Creative Commons" style="border-width:0" src="https://i.creativecommons.org/l/by-nc-sa/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">
JoseLuisGS</span>
by <a xmlns:cc="http://creativecommons.org/ns#" href="https://joseluisgs.dev/" property="cc:attributionName" rel="cc:attributionURL">
José Luis González Sánchez</a> is licensed under
a <a rel="license" href="http://creativecommons.org/licenses/by-nc-sa/4.0/">Creative Commons
Reconocimiento-NoComercial-CompartirIgual 4.0 Internacional License</a>.<br />Creado a partir de la obra
en <a xmlns:dct="http://purl.org/dc/terms/" href="https://github.com/joseluisgs" rel="dct:source">https://github.com/joseluisgs</a>.