# /gateway - API Gateway Design

Design the API gateway layer for multi-service systems.

## Usage
```
/gateway <system>                   # Full gateway design
/gateway bff                        # Backend-for-Frontend pattern
```

## Gateway Responsibilities
- Request routing to backend services
- Authentication and authorization (JWT validation)
- Rate limiting (per user, per tenant, per endpoint)
- Request/response transformation
- SSL termination
- Load balancing
- Circuit breaking
- Caching
- API versioning
- Request logging and tracing
- CORS handling

## Patterns

### BFF (Backend for Frontend)
Separate gateway per client type:
```
Web App    -> Web BFF Gateway    -> Microservices
Mobile App -> Mobile BFF Gateway -> Microservices
3rd Party  -> Public API Gateway -> Microservices
```
Each BFF tailors responses for its client (aggregation, field selection, format).

### Gateway Aggregation
Single call fans out to multiple services:
```
GET /api/v1/dashboard
  -> User Service (profile)
  -> Order Service (recent orders)
  -> Analytics Service (stats)
  <- Combined response
```

### Tools
| Tool | Type | Best For |
|------|------|----------|
| Kong | Full-featured | Enterprise, plugin ecosystem |
| Traefik | Cloud-native | Kubernetes, auto-discovery |
| AWS API Gateway | Managed | Serverless, AWS ecosystem |
| Envoy/Istio | Service mesh | Microservices, advanced routing |
| NGINX | Reverse proxy | Simple routing, high performance |

## Examples
```
/gateway university-system
/gateway bff
```
