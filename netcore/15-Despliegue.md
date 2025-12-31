# Despliegue de Aplicaciones ASP.NET Core

- [Despliegue de Aplicaciones ASP.NET Core](#despliegue-de-aplicaciones-aspnet-core)
  - [Introducción al Despliegue](#introducción-al-despliegue)
    - [Opciones de Despliegue](#opciones-de-despliegue)
  - [Preparación para Despliegue](#preparación-para-despliegue)
    - [Configurar Entorno de Producción](#configurar-entorno-de-producción)
    - [Ajustar Tests](#ajustar-tests)
  - [Publicación de la Aplicación](#publicación-de-la-aplicación)
    - [Publicar para Windows/Linux](#publicar-para-windowslinux)
    - [Publicar con Dependencias Incluidas](#publicar-con-dependencias-incluidas)
  - [Despliegue con Docker](#despliegue-con-docker)
    - [Dockerfile Básico](#dockerfile-básico)
    - [Dockerfile Multi-Stage (Recomendado)](#dockerfile-multi-stage-recomendado)
    - [Construir y Ejecutar Imagen](#construir-y-ejecutar-imagen)
  - [Docker Compose](#docker-compose)
    - [Con PostgreSQL](#con-postgresql)
    - [Con SQL Server](#con-sql-server)
    - [Con MongoDB](#con-mongodb)
    - [Completo (PostgreSQL + MongoDB)](#completo-postgresql--mongodb)
  - [Variables de Entorno en Docker](#variables-de-entorno-en-docker)
    - [Archivo .env](#archivo-env)
    - [Uso en docker-compose.yml](#uso-en-docker-composeyml)
  - [Optimización de Imágenes Docker](#optimización-de-imágenes-docker)
    - [Alpine Linux](#alpine-linux)
    - [. dockerignore](#-dockerignore)
  - [Health Checks](#health-checks)
    - [Endpoint de Salud](#endpoint-de-salud)
    - [Health Check en Docker](#health-check-en-docker)
  - [Despliegue en Azure](#despliegue-en-azure)
    - [Azure App Service](#azure-app-service)
    - [Azure Container Instances](#azure-container-instances)
  - [Despliegue en AWS](#despliegue-en-aws)
    - [AWS Elastic Beanstalk](#aws-elastic-beanstalk)
  - [CI/CD con GitHub Actions](#cicd-con-github-actions)
    - [Workflow Básico](#workflow-básico)
    - [Workflow con Docker](#workflow-con-docker)
  - [Monitoreo y Logging](#monitoreo-y-logging)
    - [Application Insights (Azure)](#application-insights-azure)
    - [Serilog](#serilog)
  - [Buenas Prácticas](#buenas-prácticas)
  - [Práctica de Clase](#práctica-de-clase)
  - [Proyecto del Curso](#proyecto-del-curso)

![Deployment Banner](../images/banner16.jpg)

---

## Introducción al Despliegue

### Opciones de Despliegue

| Opción | Descripción | Casos de Uso |
|: -------|:------------|:-------------|
| **IIS** | Internet Information Services (Windows) | Aplicaciones Windows, intranet corporativa |
| **Kestrel** | Servidor web multiplataforma de . NET | Desarrollo, detrás de reverse proxy |
| **Docker** | Contenedores | Microservicios, portabilidad, cloud |
| **Azure App Service** | PaaS de Microsoft | Despliegue rápido, escalado automático |
| **AWS** | Elastic Beanstalk, ECS | Infraestructura en AWS |
| **Kubernetes** | Orquestación de contenedores | Aplicaciones a gran escala |

---

## Preparación para Despliegue

### Configurar Entorno de Producción

**appsettings.Production.json:**

```json
{
  "Logging": {
    "LogLevel": {
      "Default": "Warning",
      "Microsoft.AspNetCore": "Error"
    }
  },
  "ConnectionStrings": {
    "DefaultConnection": "Host=${DB_HOST};Port=${DB_PORT};Database=${DB_NAME};Username=${DB_USER};Password=${DB_PASSWORD}",
    "MongoConnection": "mongodb://${MONGO_USER}:${MONGO_PASSWORD}@${MONGO_HOST}:${MONGO_PORT}/${MONGO_DB}"
  },
  "Jwt": {
    "Secret": "${JWT_SECRET}",
    "Issuer": "https://api.miaplicacion.com",
    "Audience": "https://api.miaplicacion.com"
  },
  "AllowedHosts": "api.miaplicacion.com",
  "EnableSwagger": false,
  "Cors": {
    "AllowedOrigins": ["https://miaplicacion.com"]
  }
}
```

---

### Ajustar Tests

**FunkosApi.Tests.csproj:**

```xml
<PropertyGroup>
  <TargetFramework>net8.0</TargetFramework>
  <IsPackable>false</IsPackable>
  <IsTestProject>true</IsTestProject>
  <!-- Configurar entorno de test -->
  <ASPNETCORE_ENVIRONMENT>Test</ASPNETCORE_ENVIRONMENT>
</PropertyGroup>
```

**Ejecutar tests con entorno específico:**

```bash
# Linux/Mac
ASPNETCORE_ENVIRONMENT=Test dotnet test

# Windows (PowerShell)
$env:ASPNETCORE_ENVIRONMENT="Test"; dotnet test

# Windows (CMD)
set ASPNETCORE_ENVIRONMENT=Test && dotnet test
```

---

## Publicación de la Aplicación

### Publicar para Windows/Linux

```bash
# Publicar para producción (runtime del sistema actual)
dotnet publish -c Release -o ./publish

# Publicar para Linux (desde Windows)
dotnet publish -c Release -r linux-x64 -o ./publish-linux

# Publicar para Windows (desde Linux/Mac)
dotnet publish -c Release -r win-x64 -o ./publish-windows
```

---

### Publicar con Dependencias Incluidas

```bash
# Self-contained (incluye . NET runtime)
dotnet publish -c Release -r linux-x64 --self-contained true -o ./publish-selfcontained

# Framework-dependent (requiere .NET instalado)
dotnet publish -c Release -r linux-x64 --self-contained false -o ./publish-fdd
```

**Ejecutar aplicación publicada:**

```bash
# Framework-dependent
dotnet FunkosApi.dll

# Self-contained
./FunkosApi
```

---

## Despliegue con Docker

### Dockerfile Básico

```dockerfile
FROM mcr.microsoft.com/dotnet/aspnet:8.0
WORKDIR /app
COPY ./publish . 
EXPOSE 80
EXPOSE 443
ENTRYPOINT ["dotnet", "FunkosApi.dll"]
```

---

### Dockerfile Multi-Stage (Recomendado)

```dockerfile
# ==================================================
# Etapa 1: Build
# ==================================================
FROM mcr.microsoft.com/dotnet/sdk:8.0 AS build
WORKDIR /src

# Copiar archivos de proyecto y restaurar dependencias
COPY ["FunkosApi/FunkosApi.csproj", "FunkosApi/"]
RUN dotnet restore "FunkosApi/FunkosApi.csproj"

# Copiar todo el código fuente
COPY .  . 
WORKDIR "/src/FunkosApi"

# Construir la aplicación
RUN dotnet build "FunkosApi.csproj" -c Release -o /app/build

# ==================================================
# Etapa 2: Publish
# ==================================================
FROM build AS publish
RUN dotnet publish "FunkosApi.csproj" -c Release -o /app/publish /p:UseAppHost=false

# ==================================================
# Etapa 3: Runtime (Imagen final)
# ==================================================
FROM mcr.microsoft.com/dotnet/aspnet:8.0 AS final
WORKDIR /app

# Crear usuario no-root para seguridad
RUN addgroup --system --gid 1000 appgroup \
    && adduser --system --uid 1000 --ingroup appgroup --shell /bin/sh appuser

# Copiar archivos publicados desde la etapa anterior
COPY --from=publish /app/publish .

# Cambiar ownership
RUN chown -R appuser:appgroup /app

# Cambiar a usuario no-root
USER appuser

# Exponer puertos
EXPOSE 8080
EXPOSE 8081

# Variables de entorno
ENV ASPNETCORE_URLS=http://+:8080
ENV ASPNETCORE_ENVIRONMENT=Production

# Configurar health check
HEALTHCHECK --interval=30s --timeout=3s --start-period=5s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1

# Punto de entrada
ENTRYPOINT ["dotnet", "FunkosApi.dll"]
```

---

### Construir y Ejecutar Imagen

```bash
# Construir imagen
docker build -t funkosapi: latest .

# Ejecutar contenedor
docker run -d \
  -p 8080:8080 \
  -e ASPNETCORE_ENVIRONMENT=Production \
  -e ConnectionStrings__DefaultConnection="Host=db;Port=5432;Database=funkos;Username=postgres;Password=postgres123" \
  -e Jwt__Secret="mi-clave-secreta-produccion-muy-larga" \
  --name funkosapi \
  funkosapi:latest

# Ver logs
docker logs -f funkosapi

# Detener y eliminar
docker stop funkosapi
docker rm funkosapi
```

---

## Docker Compose

### Con PostgreSQL

```yaml
version: '3.8'

services:
  # API
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: funkosapi
    ports:
      - "8080:8080"
    environment: 
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Host=db;Port=5432;Database=funkosdb;Username=postgres;Password=${DB_PASSWORD}
      - Jwt__Secret=${JWT_SECRET}
    depends_on:
      db:
        condition: service_healthy
    restart: unless-stopped
    networks:
      - funkos-network

  # PostgreSQL
  db:
    image: postgres:15-alpine
    container_name: funkos-postgres
    environment:
      POSTGRES_DB: funkosdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes: 
      - postgres_data:/var/lib/postgresql/data
      - ./init.sql:/docker-entrypoint-initdb. d/init.sql
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval:  10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks:
      - funkos-network

volumes:
  postgres_data: 

networks:
  funkos-network:
    driver: bridge
```

---

### Con SQL Server

```yaml
version: '3.8'

services:
  # API
  api:
    build:
      context: .
      dockerfile: Dockerfile
    container_name: funkosapi
    ports:
      - "8080:8080"
    environment: 
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Server=sqlserver;Database=FunkosDb;User Id=sa;Password=${DB_PASSWORD};TrustServerCertificate=True;
      - Jwt__Secret=${JWT_SECRET}
    depends_on:
      sqlserver: 
        condition: service_healthy
    restart: unless-stopped
    networks:
      - funkos-network

  # SQL Server
  sqlserver:
    image: mcr.microsoft.com/mssql/server:2022-latest
    container_name:  funkos-sqlserver
    environment:
      ACCEPT_EULA: "Y"
      SA_PASSWORD: ${DB_PASSWORD}
      MSSQL_PID: "Express"
    ports:
      - "1433:1433"
    volumes:
      - sqlserver_data:/var/opt/mssql
    healthcheck: 
      test: ["CMD-SHELL", "/opt/mssql-tools/bin/sqlcmd -S localhost -U sa -P ${DB_PASSWORD} -Q 'SELECT 1'"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks: 
      - funkos-network

volumes:
  sqlserver_data:

networks:
  funkos-network:
    driver: bridge
```

---

### Con MongoDB

```yaml
version: '3.8'

services:
  # API
  api:
    build:
      context: . 
      dockerfile: Dockerfile
    container_name: funkosapi
    ports:
      - "8080:8080"
    environment: 
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=funkosdb;Username=postgres;Password=${DB_PASSWORD}
      - ConnectionStrings__MongoConnection=mongodb://mongo:${MONGO_PASSWORD}@mongodb:27017/funkosdb
      - Jwt__Secret=${JWT_SECRET}
    depends_on: 
      postgres:
        condition:  service_healthy
      mongodb:
        condition: service_healthy
    restart: unless-stopped
    networks:
      - funkos-network

  # PostgreSQL
  postgres:
    image: postgres:15-alpine
    container_name: funkos-postgres
    environment:
      POSTGRES_DB: funkosdb
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: ${DB_PASSWORD}
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U postgres"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks:
      - funkos-network

  # MongoDB
  mongodb:
    image: mongo:6.0
    container_name: funkos-mongodb
    environment:
      MONGO_INITDB_ROOT_USERNAME: mongo
      MONGO_INITDB_ROOT_PASSWORD:  ${MONGO_PASSWORD}
      MONGO_INITDB_DATABASE: funkosdb
    ports: 
      - "27017:27017"
    volumes:
      - mongo_data:/data/db
    healthcheck:
      test: ["CMD", "mongosh", "--eval", "db.adminCommand('ping')"]
      interval: 10s
      timeout: 5s
      retries: 5
    restart: unless-stopped
    networks:
      - funkos-network

volumes:
  postgres_data:
  mongo_data: 

networks:
  funkos-network:
    driver: bridge
```

---

### Completo (PostgreSQL + MongoDB)

**(Ya mostrado arriba)**

---

## Variables de Entorno en Docker

### Archivo .env

```bash
# . env
DB_PASSWORD=Postgres123! 
MONGO_PASSWORD=Mongo123!
JWT_SECRET=mi-clave-secreta-produccion-muy-larga-y-segura-de-al-menos-32-caracteres
```

**⚠️ Agregar . env al . gitignore:**

```gitignore
. env
.env.local
. env.*. local
```

---

### Uso en docker-compose.yml

```yaml
services:
  api:
    build:  .
    env_file:
      - .env
    environment:
      - ConnectionStrings__DefaultConnection=Host=postgres;Database=funkos;Username=postgres;Password=${DB_PASSWORD}
      - Jwt__Secret=${JWT_SECRET}
```

---

## Optimización de Imágenes Docker

### Alpine Linux

```dockerfile
# Usar imágenes Alpine (más ligeras)
FROM mcr.microsoft.com/dotnet/sdk:8.0-alpine AS build
# ... 
FROM mcr.microsoft.com/dotnet/aspnet:8.0-alpine AS final
```

---

### . dockerignore

```
# . dockerignore
**/bin/
**/obj/
**/out/
**/.vs/
**/.vscode/
**/.git/
**/.gitignore
**/docker-compose*
**/Dockerfile*
**/.dockerignore
**/*. md
**/charts/
**/.env
**/secrets. json
```

---

## Health Checks

### Endpoint de Salud

```csharp
// Program.cs
builder.Services.AddHealthChecks()
    .AddDbContextCheck<ApplicationDbContext>("database")
    .AddMongoDb(
        builder.Configuration. GetConnectionString("MongoConnection")! ,
        "mongodb"
    );

var app = builder.Build();

app.MapHealthChecks("/health");
app.MapHealthChecks("/health/ready", new HealthCheckOptions
{
    Predicate = check => check.Tags.Contains("ready")
});
app.MapHealthChecks("/health/live", new HealthCheckOptions
{
    Predicate = _ => false
});
```

---

### Health Check en Docker

```dockerfile
HEALTHCHECK --interval=30s --timeout=3s --start-period=10s --retries=3 \
  CMD curl --fail http://localhost:8080/health || exit 1
```

```yaml
# docker-compose.yml
services:
  api:
    healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/health"]
      interval:  30s
      timeout: 3s
      retries: 3
      start_period: 10s
```

---

## Despliegue en Azure

### Azure App Service

```bash
# Login
az login

# Crear Resource Group
az group create --name funkos-rg --location eastus

# Crear App Service Plan
az appservice plan create \
  --name funkos-plan \
  --resource-group funkos-rg \
  --sku B1 \
  --is-linux

# Crear Web App
az webapp create \
  --name funkosapi \
  --resource-group funkos-rg \
  --plan funkos-plan \
  --runtime "DOTNET|8.0"

# Desplegar desde ZIP
dotnet publish -c Release -o ./publish
cd publish
zip -r ../publish.zip .
az webapp deployment source config-zip \
  --resource-group funkos-rg \
  --name funkosapi \
  --src ../publish.zip
```

---

### Azure Container Instances

```bash
# Login a Azure Container Registry
az acr login --name miacr

# Build y push imagen
docker build -t miacr.azurecr.io/funkosapi:latest .
docker push miacr.azurecr. io/funkosapi:latest

# Crear Container Instance
az container create \
  --resource-group funkos-rg \
  --name funkosapi \
  --image miacr.azurecr.io/funkosapi:latest \
  --cpu 1 \
  --memory 1 \
  --registry-login-server miacr.azurecr.io \
  --registry-username <username> \
  --registry-password <password> \
  --dns-name-label funkosapi \
  --ports 80 443
```

---

## Despliegue en AWS

### AWS Elastic Beanstalk

```bash
# Instalar EB CLI
pip install awsebcli

# Inicializar
eb init -p docker funkosapi --region us-east-1

# Crear entorno
eb create funkosapi-env

# Desplegar
eb deploy

# Abrir en navegador
eb open
```

---

## CI/CD con GitHub Actions

### Workflow Básico

```yaml
# .github/workflows/dotnet. yml
name: .NET CI/CD

on:
  push:
    branches: [ main ]
  pull_request:
    branches: [ main ]

jobs: 
  build-and-test:
    runs-on: ubuntu-latest

    steps:
    - uses:  actions/checkout@v3

    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with: 
        dotnet-version: 8.0.x

    - name: Restore dependencies
      run: dotnet restore

    - name: Build
      run: dotnet build --no-restore -c Release

    - name: Test
      run: dotnet test --no-build --verbosity normal -c Release

    - name:  Publish
      run: dotnet publish -c Release -o ./publish

    - name: Upload artifact
      uses: actions/upload-artifact@v3
      with:
        name: funkosapi
        path: ./publish
```

---

### Workflow con Docker

```yaml
# .github/workflows/docker.yml
name: Docker CI/CD

on:
  push:
    branches: [ main ]
    tags: [ 'v*' ]

env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}

jobs:
  build-and-push:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      packages: write

    steps:
    - uses: actions/checkout@v3

    - name: Log in to Container Registry
      uses: docker/login-action@v2
      with:
        registry:  ${{ env.REGISTRY }}
        username: ${{ github.actor }}
        password: ${{ secrets.GITHUB_TOKEN }}

    - name: Extract metadata
      id: meta
      uses: docker/metadata-action@v4
      with:
        images: ${{ env.REGISTRY }}/${{ env.IMAGE_NAME }}

    - name: Build and push Docker image
      uses: docker/build-push-action@v4
      with:
        context: .
        push: true
        tags: ${{ steps.meta.outputs.tags }}
        labels: ${{ steps.meta.outputs.labels }}
```

---

## Monitoreo y Logging

### Application Insights (Azure)

```bash
dotnet add package Microsoft.ApplicationInsights.AspNetCore
```

```csharp
// Program.cs
builder.Services.AddApplicationInsightsTelemetry(
    builder.Configuration["ApplicationInsights:ConnectionString"]
);
```

---

### Serilog

```bash
dotnet add package Serilog. AspNetCore
dotnet add package Serilog.Sinks. Console
dotnet add package Serilog. Sinks.File
```

```csharp
// Program.cs
using Serilog;

Log.Logger = new LoggerConfiguration()
    .WriteTo.Console()
    .WriteTo.File("logs/funkosapi-. txt", rollingInterval: RollingInterval.Day)
    .CreateLogger();

builder.Host.UseSerilog();
```

---

## Buenas Prácticas

✅ **Multi-stage builds**: Reducir tamaño de imagen final

✅ **Health checks**: Implementar endpoints de salud

✅ **Variables de entorno**: No hardcodear secretos

✅ **Usuario no-root**: Ejecutar contenedor con usuario no privilegiado

✅ **.  dockerignore**: Excluir archivos innecesarios

✅ **Versionado de imágenes**: Usar tags semánticos

✅ **Logging centralizado**: Usar Serilog o Application Insights

✅ **Monitoreo**: Implementar métricas y alertas

---

## Práctica de Clase

**Objetivo**: Desplegar la API de Funkos completamente con Docker Compose. 

**Tareas:**

1. ✅ Crear Dockerfile multi-stage optimizado
2. ✅ Crear docker-compose.yml con:
   - API
   - PostgreSQL
   - MongoDB
3. ✅ Configurar variables de entorno en .  env
4. ✅ Implementar health checks
5. ✅ Crear scripts de inicialización de BD
6. ✅ Configurar volúmenes para persistencia
7. ✅ Documentar comandos de despliegue en README
8. ✅ (Opcional) Configurar CI/CD con GitHub Actions

**Criterios de evaluación:**

- ✅ Dockerfile optimizado con multi-stage
- ✅ docker-compose.yml funcional
- ✅ Variables de entorno configuradas
- ✅ Health checks implementados
- ✅ Persistencia de datos configurada
- ✅ Documentación completa
- ✅ Aplicación funciona correctamente en Docker

---

## Proyecto del Curso

Puedes encontrar el proyecto con despliegue completo en el repositorio de GitHub.