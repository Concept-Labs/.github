# Concept‑Labs

## Build Enterprise PHP Applications with Clarity and Confidence

**Concept-Labs provides a modern PHP ecosystem designed for transparency, composability, and production-grade performance.**

[![Concept](https://img.shields.io/badge/Concept-ecosystem-violet.svg)](https://github.com/Concept-Labs)

> Build applications where every piece of configuration is transparent, every dependency is explicit, and every component is testable. An approach that complements the PHP ecosystem with a focus on clarity and control.

---

## Why Concept-Labs Exists

After decades of PHP development experience, our founder [Victor Galitsky](https://github.com/vgalitsky) envisioned a different approach to building PHP applications—one that emphasizes **clarity, explicit design, and developer understanding**. While the PHP ecosystem offers many excellent frameworks, Concept-Labs explores an alternative philosophy focused on transparency and composability.

**Our Philosophy:** Built on three pillars:

1. **🔍 Transparency First** - Every configuration is readable. Every dependency is explicit. Clear and understandable.
2. **🧩 Composability by Design** - Mix and match packages. Each component is self-contained and purposeful.
3. **🚀 Production-Ready** - Built for long-running processes, high performance, and real-world scale.

Concept-Labs offers a **complementary approach** to PHP development, distilled from years of enterprise experience into a lean, powerful ecosystem that works alongside your existing tools and practices.

---

## What Makes Concept-Labs Different

### Clear Configuration You Can Actually Read
```json
{
  "@import": [
    "${BASE}/etc/app.json"
  ]
  "app":{
     "name": "myapp"
  }
  "database": {
    "host": "@env(DB_HOST|localhost)",
    "credentials": "@include(etc/redis/credetials.json)"
  },
  "cache": {
    "driver": {
      "@extends": "etc/cache/driver.json",
      "preference": "Acme\\Driver\\RedisInterface"
    },
    "prefix": "#{app.name}"
  }
}
```
Clear and explicit configuration with powerful directives you can understand at a glance.
Different formats: JSON, PHP, (XML, YAML in progress). 
Third-party libraries may be used, only need to add appropriate adapters.

### Drop-In Integration
Every package includes a `concept.json` that self-describes its dependencies and configuration. Add a package via Composer, and it just works—no manual wiring required.

### PSR Standards Throughout
Built on **PSR-7, PSR-11, PSR-14, PSR-15, PSR-17**—ensuring your code is portable, testable, and compatible with the broader PHP ecosystem.

### Built for Scale
Designed from the ground up for long-running processes like RoadRunner and Swoole. Low-overhead middleware, efficient memory management, and clear error boundaries ensure your application performs in production.

---

## Core Packages

Our ecosystem is built on carefully designed, focused packages:

- **[@Concept-Labs/config](https://github.com/Concept-Labs/config)** - Powerful JSON configuration with environment variables, imports, and references
- **[@Concept-Labs/singularity](https://github.com/Concept-Labs/singularity)** - PSR-11 dependency injection container with explicit preferences
- **[@Concept-Labs/event-dispatcher](https://github.com/Concept-Labs/event-dispatcher)** - PSR-14 event system for decoupled communication
- **[@Concept-Labs/http-message](https://github.com/Concept-Labs/http-message)** - PSR-7 HTTP message implementations
- **[@Concept-Labs/http](https://github.com/Concept-Labs/http)** - Modern HTTP stack with middleware pipeline
- **[@Concept-Labs/simple-http](https://github.com/Concept-Labs/simple-http)** - Quick-start HTTP application template
- **[@Concept-Labs/dbal](https://github.com/Concept-Labs/dbal)** - Database abstraction with fluent query builders
- More coming soon...

Each package is independently useful, yet they work seamlessly together.

---

## Quick Growth Through Experience

Concept-Labs represents rapid innovation built on a foundation of decades of PHP experience. What typically takes years to mature has been accelerated through:

- **Battle-tested patterns** from enterprise applications
- **Lessons learned** from framework limitations
- **Modern PHP capabilities** (8.2+) enabling cleaner abstractions
- **Focused vision** from experienced architecture

The result? A production-ready ecosystem that feels both familiar and refreshingly modern.

---

## Get Started in Minutes

```bash
# Install via Composer
composer create-project concept-labs/simple-http my-app

# Configure your application
cd my-app
cp etc/default.json.dist etc/default.json

# Start developing
php -S localhost:8000 -t pub
```

**That's it.** No scaffolding commands. No code generation. Just install, configure, and build.

---

## Documentation

### 📘 Getting Started
- **[Quick Start Guide](../docs/quick-start.md)** - Get up and running in 5 minutes
- **[Architecture Overview](../docs/architecture.md)** - Understand the system design

### 🛠️ Core Components
- **[Configuration System](../docs/configuration.md)** - Master the JSON DSL
- **[Dependency Injection](../docs/dependency-injection.md)** - Work with Singularity DI
- **[HTTP Stack](../docs/http-stack.md)** - Build web applications
- **[Event System](../docs/events.md)** - Coordinate with events
- **[Database Layer](../docs/database.md)** - Work with databases

### 📚 Additional Resources
- **[Testing Guide](../docs/testing.md)** - Write comprehensive tests
- **Package Documentation** - Detailed docs in each package repository

---

## Built on PSR Standards

✅ **PSR-3** - Logging  
✅ **PSR-7** - HTTP Messages  
✅ **PSR-11** - Dependency Injection Container  
✅ **PSR-14** - Event Dispatcher  
✅ **PSR-15** - HTTP Server Request Handlers  
✅ **PSR-17** - HTTP Factories  
✅ **PSR-18** - HTTP Client  

---

## Who Is Concept-Labs For?

### ✅ Concept-Labs Excels When You:
- Value **explicit over implicit** behavior
- Want **readable, diff-able configuration**
- Need **production-grade performance**
- Prefer **composition over inheritance**
- Build **long-running PHP applications**
- Work with **microservices or modular monoliths**

### 🔄 Other Great Options Exist When You:
- Prefer rapid development with "convention over configuration" 
- Need a full-featured admin panel out-of-the-box
- Want an ORM with built-in complex relationship management
- Prefer annotation/attribute-based configuration

*Concept-Labs can work alongside other tools and frameworks in your stack—it's designed to be composable and integrate well with the broader PHP ecosystem.*

---

## Community & Support

- **📦 Packages**: [github.com/Concept-Labs](https://github.com/Concept-Labs)
- **👤 Founder**: [Victor Galitsky](https://github.com/vgalitsky)
- **🐛 Issues**: Report on individual package repositories
- **💬 Discussions**: Open discussions in package repos

---

## Philosophy in Practice

> **"An application should be a conversation between its configuration and its code—clear, explicit, and understandable."**  
> — The Concept-Labs Philosophy

We believe that:
- **Configuration should tell a story** - Open any config file and understand what the app does
- **Dependencies should be explicit** - Clear resolution and visible dependencies
- **Packages should self-describe** - Each package brings its own `concept.json`
- **Production comes first** - Long-running processes, memory efficiency, clear error boundaries

This philosophy emerged from real-world experience building and maintaining large-scale applications. We've learned that clarity and explicitness can greatly enhance developer productivity and system maintainability.

Concept-Labs offers an approach focused on transparency and control. **Build with clarity. Deploy with confidence.**

---

## Contributing

We welcome contributions! Each package has its own contribution guidelines. Generally:

1. **Fork the package** you want to contribute to
2. **Make your changes** following PSR-12 coding standards
3. **Add tests** for new functionality
4. **Submit a pull request** with a clear description
5. **Update documentation** as needed

See individual package repositories for specific guidelines.

---

## License

MIT License - see individual package repositories for details.

**Built with ❤️ by developers, for developers.**

---

_Concept-Labs: Where clarity meets capability._
