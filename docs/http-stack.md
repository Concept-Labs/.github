# HTTP Stack & Middleware Pipeline

Building web applications with the Concept-Labs HTTP stack.

## Overview

The HTTP stack is built on PSR-7, PSR-15, and PSR-17 standards, providing a powerful and flexible middleware-based request processing pipeline.

## Middleware Configuration

Declare middleware in `etc/middleware.json`. Higher priority runs earlier.

### Priority Bands

Reserve "bands" to keep a stable mental model (recommended but not mandatory):

- **9000–8000: boot** (feature flags, event bus init)
- **7900–7000: security** (CORS, auth, CSRF, rate-limit)
- **6900–6000: infra** (session, locale, body parsers)
- **5900–5000: routing**
- **4900–4000: handler adapters**
- **3900–3000: response transformers** (templating, normalizers)
- **1100–1000: telemetry** (logging/metrics)
- **≤ 100: terminal** (404, throwable boundary, flusher)

### Example Configuration

```json
{
  "concept-labs/event-dispatcher:event-bus": {
    "preference": "Concept\\EventDispatcher\\Middleware\\EventBusMiddleware",
    "priority": 9000
  },
  "concept-labs/http:router": {
    "preference": "Concept\\Http\\Router\\RouterMiddleware",
    "priority": 5800
  },
  "concept-labs/http:flusher": {
    "preference": "Concept\\Http\\Middleware\\ResponseFlusherMiddleware",
    "priority": 100
  }
}
```

## Request Flow

```
Request → [Security] → [Session] → [Router] → [Handler] → [Views] → Response
```

Each middleware can:
1. Process the request before passing it on
2. Pass the request to the next middleware
3. Process the response before returning it
4. Short-circuit the pipeline (e.g., authentication failure)

## Building Middleware

Create custom middleware by implementing PSR-15 `MiddlewareInterface`:

```php
use Psr\Http\Message\ResponseInterface;
use Psr\Http\Message\ServerRequestInterface;
use Psr\Http\Server\MiddlewareInterface;
use Psr\Http\Server\RequestHandlerInterface;

class CustomMiddleware implements MiddlewareInterface
{
    public function process(
        ServerRequestInterface $request,
        RequestHandlerInterface $handler
    ): ResponseInterface {
        // Before: modify request or add attributes
        $request = $request->withAttribute('custom', 'value');
        
        // Call next middleware
        $response = $handler->handle($request);
        
        // After: modify response
        return $response->withHeader('X-Custom', 'Header');
    }
}
```

## Routing

Configure routes in `etc/router/route.json`:

```json
{
  "routes": {
    "home": {
      "path": "/",
      "methods": ["GET"],
      "handler": "App\\Handler\\HomeHandler"
    },
    "user.profile": {
      "path": "/users/{id}",
      "methods": ["GET"],
      "handler": "App\\Handler\\UserProfileHandler"
    }
  }
}
```

## Error Handling

Centralize errors in a single boundary middleware:

- Converts exceptions into HTML or JSON (content negotiation)
- Emits consistent payloads (consider RFC 7807 Problem Details)
- Logs via PSR-3 with request correlation IDs

Keep **404/405** and **flusher** as terminal middleware with very low priority (execute last).

## Security

### CORS & CSRF
Provide dedicated middleware in the security band (7xxx).

### Authentication
Short-circuit early when unauthenticated; keep handlers unaware of transport details.

### Headers
Set security headers:
- `X-Frame-Options`
- `X-Content-Type-Options`
- `Referrer-Policy`
- `Strict-Transport-Security`

### Sessions
Regenerate IDs on privilege change; set `SameSite`, `HttpOnly`, `Secure` appropriately.

## Performance

### Long-Running Runtimes

For RoadRunner/Swoole:
- Compile pipelines once (optional topo-sort) and cache with a `revisionId`
- Avoid capturing `$this` in request-lifetime closures
- Ensure event aggregation and container boot are idempotent
- Measure per-middleware latency (µs)

### OPcache
Enable and warm; keep files immutable between releases to maximize cache hits.

## Next Steps

- [Routing Guide](./routing.md) - Advanced routing patterns
- [Error Handling](./error-handling.md) - Comprehensive error management
- [Security Guide](./security.md) - Security best practices
- [Performance](./performance.md) - Optimization techniques

## Related Resources

- [http package](https://github.com/Concept-Labs/http) - Core HTTP functionality
- [simple-http package](https://github.com/Concept-Labs/simple-http) - Quick HTTP applications
- [http-message package](https://github.com/Concept-Labs/http-message) - PSR-7 implementations
