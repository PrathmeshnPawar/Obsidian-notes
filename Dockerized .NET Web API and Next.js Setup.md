
## 1. Overview

This document outlines the setup process for a Dockerized .NET Web API backend, a Next.js frontend, and a PostgreSQL database. It includes HTTPS configuration, `docker-compose.yml`, and database migration handling.

## 2. Docker Compose Configuration (`docker-compose.yml`)

```yaml
version: "3.8"

services:
  frontend:
    build: ./frontend
    container_name: nextjs_frontend
    ports:
      - "3000:3000"
    depends_on:
      - webapi
    environment:
      - NEXT_PUBLIC_API_URL=${NEXT_PUBLIC_API_URL}
    env_file:
      - .env
    restart: always

  webapi:
    build: ./backend
    container_name: dotnet_backend
    ports:
      - "5000:5000"
      - "5001:5001"
    depends_on:
      - postgres
    environment:
      - ASPNETCORE_URLS=http://+:5000;https://+:5001
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=${POSTGRES_DB};Username=${POSTGRES_USER};Password=${POSTGRES_PASSWORD}
      - ASPNETCORE_Kestrel__Certificates__Default__Path=/https/aspnetcore-https.pem
    env_file:
      - .env
    restart: always

  worker:
    build:
      context: ./backend
      dockerfile: Dockerfile.sdk # Use a separate Dockerfile with the SDK
    container_name: dotnet_worker
    depends_on:
      - postgres
    environment:
      - ConnectionStrings__DefaultConnection=Host=postgres;Port=5432;Database=${POSTGRES_DB};Username=${POSTGRES_USER};Password=${POSTGRES_PASSWORD}
    env_file:
      - .env
    command:
      [
        "sh",
        "-c",
        "dotnet ef database update --no-build --verbose && tail -f /dev/null"
      ]
    restart: always

  postgres:
    image: postgres:16
    container_name: my_postgres
    restart: always
    env_file:
      - .env
    ports:
      - "5433:5432"
    volumes:
      - pg_data:/var/lib/postgresql/data
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U ${POSTGRES_USER} -d ${POSTGRES_DB}"]
      interval: 10s
      timeout: 5s
      retries: 5

volumes:
  pg_data:
```

## 3. .NET Web API (`Program.cs`)

```csharp
using Microsoft.EntityFrameworkCore;
using backend.Data;
using Npgsql.EntityFrameworkCore.PostgreSQL;

var builder = WebApplication.CreateBuilder(args);

// Configure Kestrel for HTTPS
builder.WebHost.ConfigureKestrel(options =>
{
    options.ListenAnyIP(5000); // HTTP
    options.ListenAnyIP(5001, listenOptions =>
    {
        listenOptions.UseHttps("/https/aspnetcore-https.pem");
    });
});

// CORS Policy
builder.Services.AddCors(options =>
{
    options.AddPolicy("AllowAll",
        policy => policy.AllowAnyOrigin()
                        .AllowAnyMethod()
                        .AllowAnyHeader());
});

builder.Services.AddControllers();
builder.Services.AddEndpointsApiExplorer();
builder.Services.AddSwaggerGen();

// Database Context
builder.Services.AddDbContext<AppDbContext>(options =>
    options.UseNpgsql(builder.Configuration.GetConnectionString("DefaultConnection")));

var app = builder.Build();

app.UseCors("AllowAll");

if (app.Environment.IsDevelopment())
{
    app.UseSwagger();
    app.UseSwaggerUI();
}

app.UseHttpsRedirection();
app.UseAuthorization();
app.MapControllers();
app.Run();
```

## 4. HTTPS Certificate Setup

Generate a new certificate (if needed):

```sh
dotnet dev-certs https -ep aspnetcore-https.pfx -p YourPassword
```

Ensure the generated `.pfx` file is available and referenced in `docker-compose.yml`.

## 5. Database Migration Handling

**Important:** The runtime image (`mcr.microsoft.com/dotnet/aspnet`) does NOT include `dotnet ef` because it lacks the SDK. Running EF commands manually inside an SDK container ensures the correct environment.

Run database migrations manually if needed:

```sh
docker run --rm \
  --network=your_network \
  -v $(pwd)/backend:/app \
  -w /app \
  mcr.microsoft.com/dotnet/sdk:8.0 \
  sh -c "dotnet ef database update --no-build"
```

## 6. Common Commands

- Start the application:
    
    ```sh
    docker-compose up -d --build
    ```
    
- Stop the application:
    
    ```sh
    docker-compose down
    ```
    
- View logs:
    
    ```sh
    docker logs -f dotnet_backend
    ```
    
- Check running containers:
    
    ```sh
    docker ps
    ```
    

This document ensures your setup is reproducible and easy to manage. 🚀