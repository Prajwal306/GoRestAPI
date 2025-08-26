
```markdown
# Go Production-Ready REST API

This project is a **sample production-ready RESTful API** built with Go.  
It demonstrates a scalable and maintainable architecture for modern web services, following best practices in **database interaction, caching, authentication, observability, and clean project structure**.

---

## ✨ Features

- **CRUD Operations** – Full Create, Read, Update, Delete functionality for a `User` resource.
- **Database Persistence** – MySQL as the primary datastore.
- **Dual-Layer Caching**
  - **In-Memory Cache** – Heap-based cache (`go-cache`) for frequently accessed data.
  - **Distributed Cache** – Redis for shared caching across service instances.
- **Authentication** – JWT-based authentication for securing endpoints.
- **Middleware**
  - Structured Logging (JSON logs with `logrus`)
  - Request ID injection
  - Panic recovery
- **Observability**
  - Prometheus metrics at `/metrics`
  - pprof profiling endpoints
- **Graceful Shutdown** – Safely handles in-flight requests during shutdown.
- **Configurable** – Easily extendable to load configs via environment variables.

---

## 📂 Project Structure

go-api-project/
├── cmd/
│   └── api/
│       └── main.go           # Application entry point
├── pkg/
│   ├── api/
│   │   └── handlers.go       # HTTP handlers & routes
│   ├── auth/
│   │   └── jwt.go            # JWT generation & validation
│   ├── cache/
│   │   ├── interface.go      # Cache interface
│   │   ├── inmemory.go       # In-memory cache implementation
│   │   └── redis.go          # Redis cache implementation
│   ├── middleware/
│   │   └── middleware.go     # Logging, auth, request ID, etc.
│   ├── models/
│   │   └── user.go           # User model
│   └── store/
│       └── mysql.go          # MySQL database queries
├── go.mod
├── go.sum
└── README.md

---

## 🛠️ Prerequisites

- Go `>= 1.18`
- MySQL server
- Redis server
- Docker & Docker Compose (optional)

---

## 🚀 Getting Started

### 1. Clone the Repository
```bash
git clone <your-repo-url>
cd go-api-project
````

### 2. Database Setup

Run the following in your MySQL instance:

```sql
CREATE DATABASE dbname;
USE dbname;

CREATE TABLE `users` (
  `id` int NOT NULL AUTO_INCREMENT,
  `first_name` varchar(255) NOT NULL,
  `last_name` varchar(255) NOT NULL,
  `email` varchar(255) NOT NULL,
  `password` varchar(255) NOT NULL,
  `created_at` timestamp NULL DEFAULT CURRENT_TIMESTAMP,
  PRIMARY KEY (`id`),
  UNIQUE KEY `email` (`email`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;
```

### 3. Configuration

Update `cmd/api/main.go` with your values:

* **MySQL DSN**

  ```go
  dsn := "user:password@tcp(127.0.0.1:3306)/dbname?parseTime=true"
  ```
* **Redis**

  ```go
  rdb := redis.NewClient(&redis.Options{
      Addr: "localhost:6379",
  })
  ```
* **JWT Secret**

  ```go
  jwtAuth := auth.NewJWTAuth("your-super-secret-key", 15*time.Minute)
  ```

### 4. Install & Run

```bash
go mod tidy
go run ./cmd/api/main.go
```

* API Server → [http://localhost:8080](http://localhost:8080)
* Metrics Server → [http://localhost:9090](http://localhost:9090)

---

## 📡 API Endpoints

**Base URL:** `http://localhost:8080`

### 🔑 Authentication

**POST /login** – Returns JWT on success

```json
{
  "email": "test@example.com",
  "password": "password123"
}
```

**Response**

```json
{ "token": "ey..." }
```

---

### 👤 Users (Requires `Authorization: Bearer <token>`)

* **POST /users** – Create a new user
* **GET /users/{id}** – Retrieve user by ID
* **PUT /users/{id}** – Update user details
* **DELETE /users/{id}** – Delete user

Example request:

```json
{
  "firstName": "John",
  "lastName": "Doe",
  "email": "john.doe@example.com",
  "password": "securepassword"
}
```

---

## 📊 Observability

* **Metrics (Prometheus)** → [http://localhost:9090/metrics](http://localhost:9090/metrics)
* **pprof Profiling** → [http://localhost:9090/debug/pprof/](http://localhost:9090/debug/pprof/)
  Example:

  ```bash
  go tool pprof http://localhost:9090/debug/pprof/profile?seconds=30
  ```

---

## 🏗️ Future Improvements

* Config via `.env` or config files
* CI/CD pipeline
* API versioning
* Swagger/OpenAPI documentation

---

## 📜 License

MIT License – feel free to use and modify.

