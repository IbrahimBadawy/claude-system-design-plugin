# /backend libs - Backend Libraries & Tools Guide

Show and recommend the best backend libraries, tools, and helper packages for each framework.

## Usage
```
/backend libs <framework>           # Show best libs for a framework
/backend libs nestjs                # NestJS ecosystem
/backend libs fastapi               # FastAPI ecosystem
/backend libs express               # Express ecosystem
/backend libs spring                # Spring Boot ecosystem
/backend libs django                # Django ecosystem
```

## Best Libraries by Framework

### Node.js + NestJS

| Category | Library | Purpose |
|----------|---------|---------|
| **ORM** | Prisma | Type-safe DB client, migrations, studio |
| **ORM Alt** | Drizzle ORM | Lightweight, SQL-like, fast |
| **Validation** | class-validator + class-transformer | DTO validation decorators |
| **Validation Alt** | Zod | Schema-based, works with pipes |
| **Auth** | @nestjs/passport + @nestjs/jwt | JWT + strategies (local, OAuth) |
| **Auth Alt** | @nestjs/auth (v2+) | Newer built-in auth module |
| **API Docs** | @nestjs/swagger | Auto-generate OpenAPI/Swagger |
| **Config** | @nestjs/config | Env-based configuration |
| **Caching** | @nestjs/cache-manager + cache-manager-redis-yet | Redis caching |
| **Queue** | @nestjs/bull + bull | Background jobs with Redis |
| **Queue Alt** | @nestjs/bullmq | Modern fork, better API |
| **Events** | @nestjs/event-emitter | In-process events |
| **Scheduling** | @nestjs/schedule | Cron jobs |
| **Rate Limit** | @nestjs/throttler | Request rate limiting |
| **File Upload** | @nestjs/platform-express + multer | Multipart uploads |
| **Storage** | @aws-sdk/client-s3 | S3/MinIO file storage |
| **Email** | @nestjs-modules/mailer + nodemailer | Transactional email |
| **Logging** | nestjs-pino + pino | High-performance structured logging |
| **Monitoring** | @willsoto/nestjs-prometheus | Prometheus metrics |
| **Testing** | @nestjs/testing + jest | Built-in testing utilities |
| **WebSocket** | @nestjs/websockets + socket.io | Real-time communication |
| **GraphQL** | @nestjs/graphql + @nestjs/apollo | GraphQL support |
| **Health** | @nestjs/terminus | Health check endpoints |
| **Compression** | compression | Gzip response compression |
| **Helmet** | helmet | Security HTTP headers |
| **CORS** | built-in | Cross-origin configuration |
| **Pagination** | nestjs-paginate | Cursor/offset pagination |

### Node.js + Express

| Category | Library | Purpose |
|----------|---------|---------|
| **ORM** | Prisma or Drizzle | Database access |
| **Validation** | Zod + zod-express-middleware | Request validation |
| **Auth** | passport + jsonwebtoken | Authentication |
| **API Docs** | swagger-jsdoc + swagger-ui-express | OpenAPI docs |
| **Logging** | pino + pino-http | Structured logging |
| **Rate Limit** | express-rate-limit | Rate limiting |
| **Security** | helmet + cors | Security headers + CORS |
| **Error** | express-async-errors | Async error catching |
| **Upload** | multer | File uploads |
| **Session** | express-session + connect-redis | Session management |

### Python + FastAPI

| Category | Library | Purpose |
|----------|---------|---------|
| **ORM** | SQLAlchemy 2.0 + alembic | DB ORM + migrations |
| **ORM Alt** | Tortoise ORM | Async-first ORM |
| **ORM Alt** | SQLModel | SQLAlchemy + Pydantic (by FastAPI creator) |
| **Validation** | Pydantic v2 (built-in) | Data validation (built into FastAPI) |
| **Auth** | python-jose + passlib + python-multipart | JWT + password hashing |
| **Auth Alt** | fastapi-users | Full auth system (register, login, OAuth) |
| **API Docs** | Built-in (Swagger + ReDoc) | Auto-generated from Pydantic models |
| **Config** | pydantic-settings | Environment-based settings |
| **Caching** | fastapi-cache2 + redis | Redis caching with decorators |
| **Queue** | Celery + redis | Background task processing |
| **Queue Alt** | arq | Lightweight async job queue |
| **Rate Limit** | slowapi | Rate limiting (based on limits) |
| **File Upload** | python-multipart + boto3 | Upload to S3 |
| **Email** | fastapi-mail | Transactional email |
| **Logging** | loguru or structlog | Structured logging |
| **Monitoring** | prometheus-fastapi-instrumentator | Auto metrics |
| **Testing** | pytest + httpx + pytest-asyncio | Async testing |
| **WebSocket** | Built-in (Starlette) | Real-time communication |
| **CORS** | Built-in (CORSMiddleware) | CORS configuration |
| **Admin** | sqladmin | Auto admin panel from models |
| **Pagination** | fastapi-pagination | Pagination utilities |
| **Linting** | ruff | Fast Python linter + formatter |

### Python + Django

| Category | Library | Purpose |
|----------|---------|---------|
| **API** | Django REST Framework (DRF) | Full REST API toolkit |
| **Auth** | djangorestframework-simplejwt | JWT authentication |
| **Auth Alt** | dj-rest-auth + django-allauth | Full auth with social login |
| **Validation** | DRF Serializers (built-in) | Request/response validation |
| **Filtering** | django-filter | Advanced queryset filtering |
| **Pagination** | Built-in (DRF) | Cursor/limit-offset/page |
| **API Docs** | drf-spectacular | OpenAPI 3.0 schema generation |
| **Caching** | django-redis | Redis cache backend |
| **Queue** | Celery + django-celery-beat | Background tasks + scheduling |
| **CORS** | django-cors-headers | CORS configuration |
| **File Storage** | django-storages + boto3 | S3/GCS file storage |
| **Admin** | Built-in (Django Admin) | Auto admin panel |
| **Admin Alt** | django-unfold | Modern admin theme |
| **Testing** | pytest-django | Django-aware pytest |
| **Logging** | structlog | Structured logging |
| **Security** | django.middleware.security | Built-in security middleware |
| **Monitoring** | django-prometheus | Prometheus metrics |

### Java + Spring Boot

| Category | Library | Purpose |
|----------|---------|---------|
| **ORM** | Spring Data JPA + Hibernate | Database access |
| **Validation** | Jakarta Validation (built-in) | Bean validation |
| **Auth** | Spring Security + jjwt | Authentication + JWT |
| **API Docs** | springdoc-openapi | Swagger/OpenAPI auto-generation |
| **Config** | Built-in (application.yml) | Configuration management |
| **Caching** | Spring Cache + Caffeine/Redis | Multi-level caching |
| **Queue** | Spring AMQP (RabbitMQ) or Spring Kafka | Message queues |
| **Rate Limit** | Bucket4j | Token bucket rate limiting |
| **Logging** | SLF4J + Logback (built-in) | Structured logging |
| **Monitoring** | Micrometer + Spring Actuator | Metrics + health |
| **Testing** | JUnit 5 + Mockito + Testcontainers | Testing ecosystem |
| **Migration** | Flyway or Liquibase | Database migrations |
| **Mapping** | MapStruct | DTO <-> Entity mapping |
| **Lombok** | Lombok | Reduce boilerplate |
| **HTTP Client** | WebClient or RestClient | External API calls |

### Go + Gin

| Category | Library | Purpose |
|----------|---------|---------|
| **ORM** | GORM | Full-featured ORM |
| **ORM Alt** | sqlx + squirrel | Lightweight SQL builder |
| **Validation** | go-playground/validator | Struct validation |
| **Auth** | golang-jwt/jwt | JWT handling |
| **Config** | viper | Configuration management |
| **Logging** | zerolog or zap | Structured logging |
| **Rate Limit** | ulule/limiter | Rate limiting middleware |
| **Migration** | golang-migrate | Database migrations |
| **Testing** | testify + gomock | Assertions + mocking |
| **HTTP Client** | resty | HTTP client with retry |
| **CORS** | gin-contrib/cors | CORS middleware |
| **Swagger** | swaggo/swag | Auto-generate Swagger |
| **Cache** | go-redis/redis | Redis client |
| **Queue** | asynq | Redis-based task queue |

## ALWAYS Research Before Using

Before installing any library:
1. WebSearch "<library> latest version docs <year>"
2. Check if it's still actively maintained
3. Read the quickstart/installation guide
4. Verify compatibility with your framework version

## Examples
```
/backend libs nestjs
/backend libs fastapi
/backend libs express
/backend libs spring
```
