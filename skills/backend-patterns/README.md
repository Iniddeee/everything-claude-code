# Backend Patterns

This skill contains common backend architecture patterns and best practices.

## Architecture Patterns

### 1. Layered Architecture
```
┌─────────────────┐
│   Presentation  │  (Controllers, APIs)
├─────────────────┤
│    Business     │  (Services, Use Cases)
├─────────────────┤
│   Persistence   │  (Repositories, DAOs)
├─────────────────┤
│    Database     │  (SQL, NoSQL)
└─────────────────┘
```

### 2. Hexagonal Architecture
- Business logic independent of frameworks
- Input/Output through ports and adapters
- Easy to test and maintain

### 3. Microservices Patterns
- Service Discovery
- Circuit Breaker
- API Gateway
- Event Sourcing
- CQRS

## Database Patterns

### 1. Repository Pattern
```typescript
interface UserRepository {
  findById(id: string): Promise<User | null>
  findByEmail(email: string): Promise<User | null>
  save(user: User): Promise<User>
  delete(id: string): Promise<void>
}
```

### 2. Unit of Work Pattern
- Manage transactions across multiple repositories
- Ensure atomic operations
- Rollback on failure

### 3. Connection Pooling
- Reuse database connections
- Configure pool size based on load
- Handle connection timeouts

## API Design Patterns

### 1. RESTful Design
- Use proper HTTP methods
- Resource-based URLs
- Status codes matter
- Version your APIs

### 2. GraphQL Patterns
- Schema-first design
- Resolvers for data fetching
- DataLoader for N+1 prevention

### 3. gRPC Patterns
- Protocol buffers for contracts
- Streaming for real-time data
- Interceptors for cross-cutting concerns

## Caching Strategies

### 1. Cache-Aside
```typescript
async function getUser(id: string): Promise<User> {
  // Try cache first
  let user = await cache.get(id)
  if (user) return user
  
  // Load from database
  user = await db.users.findById(id)
  if (user) {
    await cache.set(id, user, { ttl: 3600 })
  }
  return user
}
```

### 2. Write-Through
- Write to cache and database simultaneously
- Ensures cache consistency

### 3. Write-Behind
- Write to cache immediately
- Asynchronously write to database
- Improves write performance

## Security Patterns

### 1. Authentication
- JWT tokens for stateless auth
- Refresh tokens for long-term sessions
- Multi-factor authentication

### 2. Authorization
- RBAC (Role-Based Access Control)
- ABAC (Attribute-Based Access Control)
- Policy-based decisions

### 3. Data Protection
- Encryption at rest and in transit
- Hash passwords with bcrypt/scrypt/argon2
- Sanitize all inputs

## Performance Patterns

### 1. Async Processing
- Message queues for background jobs
- Event-driven architecture
- Non-blocking I/O

### 2. Pagination
- Cursor-based for large datasets
- Offset-based for simple cases
- Include total count when needed

### 3. Rate Limiting
- Token bucket algorithm
- Sliding window counters
- Distributed rate limiting

## Monitoring & Observability

### 1. Logging
- Structured logging (JSON)
- Correlation IDs for request tracing
- Log levels: ERROR, WARN, INFO, DEBUG

### 2. Metrics
- Request/response times
- Error rates
- Resource utilization
- Business metrics

### 3. Tracing
- Distributed tracing with OpenTelemetry
- Span context propagation
- Performance bottleneck identification

## Best Practices

1. **Design for Failure**: Assume everything can fail
2. **Graceful Degradation**: Provide fallbacks
3. **Timeout Everything**: Avoid hanging requests
4. **Circuit Breakers**: Prevent cascade failures
5. **Health Checks**: Monitor service health
6. **Configuration Externalization**: Use environment variables
7. **Immutable Infrastructure**: Replace don't modify
