# Digital Wallet Backend (Microservices)

A **Spring Boot + Spring Cloud** microservices backend for a **Digital Wallet** platform — built with **service discovery**, **API Gateway**, **secure authentication**, **event-driven messaging**, and **production-grade observability**.

---

## Repository
- GitHub: https://github.com/Reema596/DigitalWalletBackend

---

## Architecture Overview

This project is structured as **microservices**:

- **api-gateway** — entry point for all client requests (Spring Cloud Gateway)
- **eureka-server** — service discovery (Netflix Eureka Server)
- **auth-service** — authentication & authorization (JWT, security, rate limiting, email)
- **user-service** — user profile management (+ Cloudinary integration)
- **wallet-service** — wallet operations (includes Razorpay integration)
- **reward-service** — rewards/loyalty related operations
- **notification_service** — notification service (directory exists)
- **observability** — Prometheus + Grafana configuration (used by Docker Compose)
- **scripts**, **tmp_redis_inspect** — utilities / local inspection helpers

**Core patterns included**
- Service Discovery: **Eureka**
- API Gateway: **Spring Cloud Gateway (WebFlux)**
- Sync service-to-service calls: **OpenFeign**
- Async messaging: **Kafka**
- Caching: **Redis**
- Resilience: **Resilience4j circuit breaker**
- Security: **Spring Security + JWT**
- API docs: **Springdoc OpenAPI (Swagger UI)**
- Monitoring: **Actuator + Prometheus + Grafana**
- Tracing: **Zipkin**
- Quality: **JaCoCo + SonarQube**

---

## Tech Stack

**Backend**
- Java **21**
- Spring Boot **4.0.3**
- Spring Cloud **2025.1.0**
- Maven (multi-module/multi-service structure)

**Databases / Storage**
- **PostgreSQL** (runtime driver included in services)
- **Redis** (Spring Data Redis)

**Messaging**
- **Apache Kafka** + **ZooKeeper** (dockerized)

**Observability**
- Spring Boot **Actuator**
- **Micrometer Prometheus Registry**
- **Prometheus** + **Grafana**
- **Zipkin** distributed tracing

**Security**
- **Spring Security**
- **JWT (JJWT 0.13.0)**
- Rate limiting: **Bucket4j** (auth-service)

**Other Integrations**
- **Razorpay** (wallet-service)
- **Cloudinary** (user-service)

**Testing & Quality**
- JUnit (Spring Boot Starter Test)
- **JaCoCo** test coverage
- **SonarQube** via Docker Compose

---

## Services & Key Dependencies (from `pom.xml`)

### api-gateway
- Spring Cloud Gateway (WebFlux)
- Eureka Client + LoadBalancer
- Resilience4j (reactor)
- Spring Security + JWT libraries
- Actuator + Prometheus + Zipkin

### eureka-server
- Eureka Server
- Web MVC
- Actuator + Prometheus + Zipkin

### auth-service
- Spring Security + JWT (JJWT)
- Spring Data JPA + PostgreSQL driver
- Spring Data Redis
- Spring Mail
- Kafka client
- Bucket4j rate limiting
- OpenFeign + Resilience4j
- OpenAPI/Swagger UI

### user-service
- Spring Data JPA + PostgreSQL driver
- Redis
- Kafka
- OpenFeign + Resilience4j
- Cloudinary client
- OpenAPI/Swagger UI

### wallet-service
- Spring Data JPA + PostgreSQL driver
- Redis
- Kafka
- Razorpay SDK
- OpenFeign
- OpenAPI/Swagger UI
- MapStruct

### reward-service
- Spring Data JPA + PostgreSQL driver
- Redis
- Kafka
- OpenFeign
- OpenAPI/Swagger UI
- MapStruct

---

## Prerequisites

- **Java 21**
- **Maven**
- **Docker + Docker Compose** (recommended for local infrastructure)

---

## Quick Start (Local Infrastructure)

This repo includes a `docker-compose.yml` for local dev tools:

### Start dependencies
```bash
docker compose up -d
```

This will start:
- ZooKeeper: `localhost:2181`
- Kafka: `localhost:9092` (also internal `kafka:29092`)
- Redis: `localhost:6379`
- Zipkin: `http://localhost:9411`
- Prometheus: `http://localhost:9090`
- Grafana: `http://localhost:3000` (default login: `admin` / `admin`)
- SonarQube: `http://localhost:9000`
- SonarQube Postgres DB (internal)

### Stop
```bash
docker compose down
```

---

## Running the Microservices

Because each service is its own Spring Boot application, you can run them individually:

### 1) Start Eureka Server
```bash
cd eureka-server
mvn spring-boot:run
```

### 2) Start API Gateway
```bash
cd api-gateway
mvn spring-boot:run
```

### 3) Start business services
In separate terminals:
```bash
cd auth-service && mvn spring-boot:run
cd user-service && mvn spring-boot:run
cd wallet-service && mvn spring-boot:run
cd reward-service && mvn spring-boot:run
```

> Tip: Start your infrastructure with Docker Compose first (Kafka/Redis/Zipkin/etc.) to avoid connection errors.

---

## API Documentation (Swagger / OpenAPI)

Several services include:
- `org.springdoc:springdoc-openapi-starter-webmvc-ui`

So you can typically access Swagger UI at:

- `http://localhost:<service-port>/swagger-ui/index.html`

(Exact ports depend on your `application.yml/properties` per service.)

---

## Monitoring & Tracing

### Actuator + Prometheus
Services use:
- `spring-boot-starter-actuator`
- `micrometer-registry-prometheus`

Prometheus is configured from:
- `./observability/prometheus/prometheus.yml`

### Grafana
Grafana is provisioned from:
- `./observability/grafana/provisioning`
- dashboards in:
- `./observability/grafana/dashboards`

### Zipkin
Zipkin runs at:
- `http://localhost:9411`

---

## Code Quality (JaCoCo + SonarQube)

JaCoCo is configured via `jacoco-maven-plugin` in multiple services.

### Run tests + coverage
Inside a service folder:
```bash
mvn clean verify
```

### SonarQube (Docker Compose)
- SonarQube: `http://localhost:9000`
- DB is included in the Compose file (`sonarqube-db`)

> To run sonar analysis you typically add/confirm `sonar.login` and project settings. Some services already define sonar properties (example: `user-service`).

---

## Environment Variables / Configuration

This project uses:
- `me.paulschwarz:spring-dotenv` (multiple services)

That means you can store config in a `.env` file (per-service or shared, depending on your setup).

Common things you will likely configure:
- PostgreSQL connection URL/user/password
- Redis host/port
- Kafka bootstrap servers
- JWT secret / expiration
- Email SMTP credentials (auth-service)
- Cloudinary credentials (user-service)
- Razorpay key/secret (wallet-service)

> Recommendation: add a `.env.example` to the repo with safe placeholders (no secrets).

---

## Docker Compose Services Included

From `docker-compose.yml`:
- Kafka + ZooKeeper
- Redis
- Zipkin
- Prometheus + Grafana
- SonarQube + Postgres (for SonarQube)

---

## Contributing

1. Fork the repo
2. Create a feature branch: `git checkout -b feature/my-feature`
3. Commit changes
4. Open a Pull Request

---

## Author

- **Reema596** — https://github.com/Reema596

---

## License

No license file detected yet. If you want, add a `LICENSE` (MIT/Apache-2.0/etc.) to make usage terms clear.
