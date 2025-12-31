# Dockerización de Aplicaciones .  NET

---

- [Dockerización de Aplicaciones .  NET](#dockerización-de-aplicaciones---net)
  - [Introducción a Docker](#introducción-a-docker)
  - [Dockerfile para aplicaciones .NET](#dockerfile-para-aplicaciones-net)
    - [Dockerfile simple (aplicación ya compilada)](#dockerfile-simple-aplicación-ya-compilada)
    - [Dockerfile multi-etapa (compilación + ejecución)](#dockerfile-multi-etapa-compilación--ejecución)
    - [Dockerfile optimizado con cache de NuGet](#dockerfile-optimizado-con-cache-de-nuget)
  - [Docker Compose para aplicaciones . NET](#docker-compose-para-aplicaciones--net)
    - [Ejemplo:    API + PostgreSQL + Redis](#ejemplo----api--postgresql--redis)
    - [Ejemplo:  Aplicación completa con múltiples servicios](#ejemplo--aplicación-completa-con-múltiples-servicios)
  - [Mejores prácticas para Docker con . NET](#mejores-prácticas-para-docker-con--net)
  - [Docker en desarrollo vs producción](#docker-en-desarrollo-vs-producción)
  - [Comandos útiles de Docker](#comandos-útiles-de-docker)

---

## Introducción a Docker

**Docker** es una plataforma que permite empaquetar aplicaciones y sus dependencias en **contenedores** portátiles y ligeros. 

![Docker](../images/docker.jpeg)

**Conceptos clave:**

- **Imagen**: Plantilla de solo lectura que contiene todo lo necesario para ejecutar una aplicación. 
- **Contenedor**:  Instancia en ejecución de una imagen.  
- **Dockerfile**: Archivo de texto que define cómo construir una imagen.  
- **Docker Compose**: Herramienta para definir y ejecutar aplicaciones multi-contenedor.

![Docker Architecture](../images/docker.png)

---

## Dockerfile para aplicaciones .NET

### Dockerfile simple (aplicación ya compilada)

Si ya tienes tu aplicación publicada localmente: 

```dockerfile
# Usar la imagen base del runtime de ASP.NET Core
FROM mcr.microsoft.com/dotnet/aspnet:8.0

# Establecer el directorio de trabajo
WORKDIR /app

# Copiar los archivos publicados
COPY ./publish . 

# Exponer el puerto
EXPOSE 8080

# Configurar variables de entorno
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production

# Comando de inicio
ENTRYPOINT ["dotnet", "MiAplicacion.dll"]
```

**Publicar y ejecutar:**

```bash
# Publicar la aplicación localmente
dotnet publish -c Release -o ./publish

# Construir la imagen
docker build -t mi-aplicacion: 1.0 .

# Ejecutar el contenedor
docker run -d -p 8080:8080 --name mi-app mi-aplicacion:1.0
```

### Dockerfile multi-etapa (compilación + ejecución)

**Dockerfile recomendado para proyectos .  NET:**

```dockerfile
# ============================================
# Etapa 1:   BUILD - Compilación
# ============================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

# Establecer directorio de trabajo
WORKDIR /src

# Copiar archivos de proyecto y restaurar dependencias
# (primero solo los .  csproj para aprovechar la cache de Docker)
COPY ["MiAplicacion.Api/MiAplicacion.Api.csproj", "MiAplicacion.Api/"]
COPY ["MiAplicacion. Core/MiAplicacion.Core.csproj", "MiAplicacion.Core/"]
COPY ["MiAplicacion.Infrastructure/MiAplicacion.Infrastructure.csproj", "MiAplicacion.Infrastructure/"]

# Restaurar dependencias
RUN dotnet restore "MiAplicacion.Api/MiAplicacion.Api. csproj"

# Copiar el resto del código
COPY . .

# Compilar la aplicación
WORKDIR "/src/MiAplicacion. Api"
RUN dotnet build "MiAplicacion.Api.csproj" -c Release -o /app/build

# ============================================
# Etapa 2: PUBLISH
# ============================================
FROM build AS publish
RUN dotnet publish "MiAplicacion.Api.csproj" -c Release -o /app/publish /p:UseAppHost=false

# ============================================
# Etapa 3: RUNTIME - Imagen final
# ============================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final

# Crear usuario no-root para seguridad
RUN addgroup --system --gid 1001 appgroup && \
    adduser --system --uid 1001 --ingroup appgroup --shell /bin/sh appuser

WORKDIR /app

# Copiar archivos publicados desde la etapa de publish
COPY --from=publish /app/publish .

# Cambiar al usuario no-root
USER appuser

# Exponer puerto
EXPOSE 8080

# Variables de entorno
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production

# Healthcheck
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1

# Comando de inicio
ENTRYPOINT ["dotnet", "MiAplicacion.Api.dll"]
```

**Construir y ejecutar:**

```bash
# Construir la imagen
docker build -t mi-aplicacion:1.0 -f Dockerfile . 

# Ejecutar con variables de entorno
docker run -d \
  -p 8080:8080 \
  --name mi-app \
  -e ASPNETCORE_ENVIRONMENT=Production \
  -e ConnectionStrings__DefaultConnection="Host=postgres;Database=midb;Username=user;Password=pass" \
  mi-aplicacion: 1.0

# Ver logs
docker logs -f mi-app

# Detener y eliminar
docker stop mi-app
docker rm mi-app
```

### Dockerfile optimizado con cache de NuGet

Para aprovechar mejor la cache de Docker:

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build

WORKDIR /src

# 1. Copiar solo archivos de solución y proyecto
COPY ["MiAplicacion.sln", ". "]
COPY ["MiAplicacion.Api/MiAplicacion.  Api.csproj", "MiAplicacion.Api/"]
COPY ["MiAplicacion.  Core/MiAplicacion. Core.csproj", "MiAplicacion. Core/"]
COPY ["MiAplicacion.Infrastructure/MiAplicacion.Infrastructure.csproj", "MiAplicacion.Infrastructure/"]

# 2. Restaurar dependencias (esta capa se cachea si no cambian los .  csproj)
RUN dotnet restore "MiAplicacion.  sln"

# 3. Copiar el resto del código
COPY . .

# 4. Compilar
WORKDIR "/src/MiAplicacion.Api"
RUN dotnet publish "MiAplicacion.Api.csproj" \
    -c Release \
    -o /app/publish \
    --no-restore \
    /p:UseAppHost=false

# ============================================
# Runtime
# ============================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0

WORKDIR /app
COPY --from=build /app/publish .

EXPOSE 8080
ENV ASPNETCORE_URLS=http://+:8080

ENTRYPOINT ["dotnet", "MiAplicacion.Api.dll"]
```

**Archivo `.dockerignore` (importante):**

```dockerignore
# Directorios de compilación
**/bin/
**/obj/
**/out/

# Paquetes NuGet
**/packages/

# Archivos de usuario
**/.vs/
**/.vscode/
**/.idea/
**/*. user

# Archivos temporales
**/*. swp
**/*.bak
**/*~

# Datos locales
**/data/
**/logs/

# Git
.git/
.gitignore
.gitattributes

# Docker
**/Dockerfile*
**/docker-compose*
**/.dockerignore

# Documentación
**/*.md
LICENSE

# Tests
**/TestResults/
```

---

## Docker Compose para aplicaciones . NET

### Ejemplo:    API + PostgreSQL + Redis

```yaml
version: '3. 8'

services:
  # Aplicación .  NET
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: mi-api
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=miapp_db;Username=postgres;Password=mysecretpassword
      - ConnectionStrings__Redis=redis: 6379
    depends_on: 
      postgres:
        condition: service_healthy
      redis:
        condition:  service_started
    networks:
      - app-network
    restart: unless-stopped

  # PostgreSQL
  postgres:
    image: postgres:16-alpine
    container_name: mi-postgres
    environment:
      POSTGRES_DB: miapp_db
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: mysecretpassword
    ports:
      - "5432:5432"
    volumes: 
      - postgres-data:/var/lib/postgresql/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval:  10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # Redis
  redis:
    image: redis:7-alpine
    container_name: mi-redis
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - app-network
    restart:  unless-stopped

networks:
  app-network: 
    driver: bridge

volumes: 
  postgres-data:
  redis-data:
```

**Comandos:**

```bash
# Iniciar todos los servicios
docker-compose up -d

# Ver logs
docker-compose logs -f

# Ver logs de un servicio específico
docker-compose logs -f api

# Detener todos los servicios
docker-compose down

# Detener y eliminar volúmenes
docker-compose down -v

# Reconstruir imágenes
docker-compose build

# Reconstruir y reiniciar
docker-compose up -d --build
```

### Ejemplo:  Aplicación completa con múltiples servicios

```yaml
version: '3.8'

services:
  # API Principal
  api:
    build: 
      context: .
      dockerfile: src/MiAplicacion.  Api/Dockerfile
    container_name: mi-api
    ports:
      - "5000:8080"
    environment:
      - ASPNETCORE_ENVIRONMENT=Docker
      - ConnectionStrings__Postgres=Host=postgres;Database=miapp;Username=admin;Password=admin123
      - ConnectionStrings__MongoDB=mongodb://admin:admin123@mongo:27017
      - ConnectionStrings__Redis=redis:6379
      - Jwt__SecretKey=${JWT_SECRET_KEY}
    depends_on:
      postgres: 
        condition: service_healthy
      mongo:
        condition: service_healthy
      redis:
        condition:  service_started
    networks: 
      - app-network
    restart: unless-stopped

  # PostgreSQL
  postgres:
    image: postgres:16-alpine
    container_name: postgres-db
    environment:
      POSTGRES_DB: miapp
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: admin123
    ports:
      - "5432:5432"
    volumes:
      - postgres-data:/var/lib/postgresql/data
      - ./scripts/init-postgres.sql:/docker-entrypoint-initdb.d/init. sql
    networks:
      - app-network
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U admin -d miapp"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # MongoDB
  mongo:
    image: mongo:7
    container_name: mongo-db
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: admin123
      MONGO_INITDB_DATABASE: miapp
    ports:
      - "27017:27017"
    volumes: 
      - mongo-data:/data/db
      - ./scripts/init-mongo. js:/docker-entrypoint-initdb.d/init.js
    networks:
      - app-network
    healthcheck:
      test: echo 'db.runCommand("ping").ok' | mongosh localhost:27017/test --quiet
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # Redis
  redis:
    image: redis:7-alpine
    container_name: redis-cache
    command: redis-server --requirepass redis123
    ports:
      - "6379:6379"
    volumes:
      - redis-data:/data
    networks:
      - app-network
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped

  # Nginx (Reverse Proxy)
  nginx:
    image: nginx:alpine
    container_name: nginx-proxy
    ports:
      - "80:80"
      - "443:443"
    volumes:
      - ./nginx/nginx.conf:/etc/nginx/nginx.conf:ro
      - ./nginx/ssl:/etc/nginx/ssl:ro
    depends_on:
      - api
    networks:
      - app-network
    restart: unless-stopped

networks:
  app-network: 
    driver: bridge

volumes: 
  postgres-data:
  mongo-data:
  redis-data:
```

**Archivo `.env` para variables de entorno:**

```env
JWT_SECRET_KEY=tu-clave-secreta-muy-larga-y-segura-de-al-menos-32-caracteres
ASPNETCORE_ENVIRONMENT=Docker
```

---

## Mejores prácticas para Docker con . NET

✅ **1. Usar imágenes multi-etapa para reducir tamaño**

```dockerfile
# ❌ Imagen grande (SDK completo)
FROM mcr.microsoft.com/dotnet/sdk:8.0
COPY .  .
RUN dotnet publish -o /app
ENTRYPOINT ["dotnet", "/app/MiApp.dll"]

# ✅ Imagen pequeña (solo runtime)
FROM mcr.microsoft. com/dotnet/sdk:8.0 AS build
COPY .  .
RUN dotnet publish -o /app

FROM mcr.microsoft.com/dotnet/aspnet:8.0
COPY --from=build /app . 
ENTRYPOINT ["dotnet", "MiApp.dll"]
```

✅ **2. Aprovechar la cache de capas de Docker**

```dockerfile
# ✅ Primero copiar solo .  csproj para cachear restore
COPY *.csproj .
RUN dotnet restore

# Luego copiar el resto del código
COPY . . 
RUN dotnet publish
```

✅ **3. Usar usuario no-root por seguridad**

```dockerfile
RUN adduser --disabled-password --gecos '' appuser
USER appuser
```

✅ **4. Incluir healthchecks**

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s \
  CMD curl --fail http://localhost:8080/health || exit 1
```

✅ **5. Usar variables de entorno para configuración**

```dockerfile
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production
```

✅ **6. Etiquetar imágenes correctamente**

```bash
docker build -t mi-app:1.0.0 .
docker build -t mi-app:latest .
```

✅ **7. Usar `.dockerignore` para excluir archivos innecesarios**

✅ **8. Minimizar el número de capas**

```dockerfile
# ❌ Muchas capas
RUN apt-get update
RUN apt-get install -y curl
RUN apt-get clean

# ✅ Una sola capa
RUN apt-get update && \
    apt-get install -y curl && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*
```

---

## Docker en desarrollo vs producción

**Dockerfile para desarrollo (con hot reload):**

```dockerfile
FROM mcr.microsoft.com/dotnet/sdk:8.0

WORKDIR /app

# Instalar dotnet watch
RUN dotnet tool install --global dotnet-watch

COPY . . 

RUN dotnet restore

EXPOSE 8080

CMD ["dotnet", "watch", "run", "--urls", "http://+:8080"]
```

**docker-compose para desarrollo:**

```yaml
version: '3.8'

services:
  api-dev:
    build:
      context: .
      dockerfile: Dockerfile.  dev
    ports:
      - "5000:8080"
    volumes:
      - ./src:/app/src # Montar código fuente para hot reload
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
    depends_on:
      - postgres

  postgres:
    image: postgres:16-alpine
    environment: 
      POSTGRES_PASSWORD: dev123
    ports:
      - "5432:5432"
    volumes:
      - postgres-dev-data:/var/lib/postgresql/data

volumes:
  postgres-dev-data:
```

---

## Comandos útiles de Docker

```bash
# ===== IMÁGENES =====
docker images                    # Listar imágenes
docker build -t mi-app:1.0 .    # Construir imagen
docker rmi mi-app:1.0           # Eliminar imagen
docker image prune              # Eliminar imágenes no usadas

# ===== CONTENEDORES =====
docker ps                       # Listar contenedores en ejecución
docker ps -a                    # Listar todos los contenedores
docker run -d -p 8080:8080 mi-app  # Ejecutar contenedor
docker stop mi-app              # Detener contenedor
docker start mi-app             # Iniciar contenedor
docker restart mi-app           # Reiniciar contenedor
docker rm mi-app                # Eliminar contenedor
docker logs -f mi-app           # Ver logs en tiempo real
docker exec -it mi-app /bin/bash  # Acceder al contenedor

# ===== DOCKER COMPOSE =====
docker-compose up -d            # Iniciar servicios
docker-compose down             # Detener servicios
docker-compose logs -f          # Ver logs
docker-compose ps               # Listar servicios
docker-compose restart          # Reiniciar servicios
docker-compose build            # Reconstruir imágenes

# ===== LIMPIEZA =====
docker system prune             # Limpiar recursos no usados
docker system prune -a          # Limpiar todo (imágenes, contenedores, volúmenes)
docker volume prune             # Eliminar volúmenes no usados
```

---
