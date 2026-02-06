# Concept‑Labs

## Build Enterprise PHP Applications with Clarity and Confidence

**Concept-Labs provides a modern PHP ecosystem that eliminates the complexity of traditional frameworks while maintaining enterprise-grade capabilities.**

[![Concept](https://img.shields.io/badge/Concept-ecosystem-violet.svg)](https://github.com/Concept-Labs)

> Stop wrestling with magic and hidden conventions. Build applications where every piece of configuration is transparent, every dependency is explicit, and every component is testable.

---

## Why Concept-Labs Exists

After decades of PHP development across enterprise applications, our founder [Victor Galitsky](https://github.com/vgalitsky) witnessed a troubling trend: **modern PHP frameworks had become opaque black boxes**. Configuration scattered across dozens of files. Dependencies resolved through hidden magic. Behavior determined by conventions you had to memorize or discover through trial and error.

**The Real Cost?** Hours debugging mysterious behaviors. Days tracking down dependency chains. Weeks battling framework limitations when scaling to production. 

**Concept-Labs is the answer:** A philosophy distilled from hard-won experience, crystallized into three principles:

1. **🔍 Transparency Over Magic** - Every configuration tells a story. Every dependency is explicit. No surprises, ever.
2. **🧩 Composability Over Monoliths** - Small, focused packages that do one thing brilliantly. Compose your perfect stack.
3. **🚀 Production-First Design** - Built for the real world: long-running processes, high concurrency, and unforgiving production environments.

This isn't just another framework—it's a **fundamental rethinking of PHP application architecture**, born from decades of battle scars and refined into an elegant, powerful ecosystem.

---

## What Makes Concept-Labs Different

### 📖 Configuration That Tells a Story
Open any config file and instantly understand your application's architecture. No hunting through dozens of files or deciphering magic conventions. Everything is explicit, readable, and version-control friendly. Support for JSON, PHP, with XML and YAML coming soon.

### 🔌 True Drop-In Integration
Add any Concept-Labs package via Composer and it automatically integrates. Every package carries its own `concept.json` manifest—self-describing dependencies and configuration. Zero manual wiring. Zero surprises.

### ✨ PSR Standards Throughout
Built on industry standards (**PSR-7, PSR-11, PSR-14, PSR-15, PSR-17**) means your code works seamlessly with the broader PHP ecosystem. Write once, integrate anywhere. Future-proof your applications.

### 🚀 Production-Grade Performance
Engineered for long-running processes like RoadRunner and Swoole. Low-overhead middleware, efficient memory management, and bulletproof error boundaries ensure your application scales when it matters most.

---

## The Ecosystem

Our ecosystem is built on carefully designed, focused packages that solve real problems:

### Foundation Layer
- **[@Concept-Labs/config](https://github.com/Concept-Labs/config)** - Transform configuration chaos into clarity with intelligent JSON configuration featuring environment variables, imports, and cross-references
- **[@Concept-Labs/singularity](https://github.com/Concept-Labs/singularity)** - Take control of dependencies with a PSR-11 container that eliminates guesswork through explicit preferences

### Communication & Events
- **[@Concept-Labs/event-dispatcher](https://github.com/Concept-Labs/event-dispatcher)** - Decouple your application with a battle-tested PSR-14 event system that keeps components loosely coupled

### HTTP Stack
- **[@Concept-Labs/http-message](https://github.com/Concept-Labs/http-message)** - Modern PSR-7/PSR-17 HTTP messages built for PHP 8.2+ with zero compromises
- **[@Concept-Labs/http](https://github.com/Concept-Labs/http)** - Enterprise-ready HTTP stack with priority-based middleware pipeline for complex applications
- **[@Concept-Labs/simple-http](https://github.com/Concept-Labs/simple-http)** - Launch HTTP applications in minutes with our quick-start template

### Data Layer
- **[@Concept-Labs/dbal](https://github.com/Concept-Labs/dbal)** - Write secure, maintainable database queries with fluent builders that prevent SQL injection
- **[@Concept-Labs/orm](https://github.com/Concept-Labs/orm)** - Map objects to databases without sacrificing control or transparency

### Utilities
- **[@Concept-Labs/arrays](https://github.com/Concept-Labs/arrays)** - Master complex data structures with advanced dot notation and recursive array utilities
- **[@Concept-Labs/cli](https://github.com/Concept-Labs/cli)** - Build professional command-line tools with elegant APIs
- **[@Concept-Labs/simple-cache](https://github.com/Concept-Labs/simple-cache)** - PSR-16 caching made simple and reliable

**70+ packages and growing** — Each independently useful, all seamlessly integrated.

---

## From Experience to Excellence

What typically takes years to mature has been accelerated through battlefield wisdom:

- **20+ Years of Enterprise Patterns** - Battle-tested solutions from real-world applications serving millions
- **Lessons from Framework Limitations** - We've hit every wall. Now you don't have to.
- **Modern PHP Superpowers** - PHP 8.2+ enables abstractions that were impossible before
- **Focused Architectural Vision** - One experienced architect's relentless pursuit of clarity

**The Result?** A production-ready ecosystem that feels familiar yet revolutionary. The productivity of modern frameworks without the mystery. The control of micro-frameworks without the boilerplate.

---

## Get Your First App Running in 5 Minutes

```bash
composer create-project concept-labs/simple-http my-app
cd my-app && cp etc/default.json.dist etc/default.json
php -S localhost:8000 -t pub
```

**Done.** No scaffolding. No code generation. No mystery. Just a working application ready for your ideas.

[→ View Quick Start Guide](../docs/quick-start.md) | [→ Explore Architecture](../docs/architecture.md)

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

## Who Should Choose Concept-Labs?

### ✅ Perfect For:
- **Senior developers** who value explicit over implicit behavior
- **Growing teams** needing readable, maintainable code
- **Production-critical apps** requiring high performance and reliability
- **Microservices architectures** benefiting from composable components
- **Long-running PHP applications** on RoadRunner, Swoole, or similar
- **API-first projects** needing robust HTTP middleware

### 💡 Consider Alternatives If:
- You prefer "convention over configuration" frameworks like Laravel
- You need a batteries-included admin panel (consider AdminLTE + your framework)
- Complex ORM relationship management is your priority (Doctrine may fit better)
- You're building a quick prototype and don't need long-term maintainability

**Honesty builds trust.** Concept-Labs solves specific problems brilliantly—make sure they're your problems.

---

## Join the Movement

- **🔥 Explore Packages**: [github.com/Concept-Labs](https://github.com/Concept-Labs) — 70+ repositories and growing
- **👨‍💻 Meet the Founder**: [Victor Galitsky](https://github.com/vgalitsky) — 20+ years of enterprise PHP expertise
- **🐛 Report Issues**: Found a bug? Open an issue in the specific package repository
- **💬 Join Discussions**: Share ideas and get help in package discussions
- **🤝 Contribute**: Every package welcomes contributions—see individual repos for guidelines

**Ready to experience PHP development without the mystery?** Start with [simple-http](https://github.com/Concept-Labs/simple-http) and feel the difference.

---

## Philosophy in Practice

> **"Your application should be a conversation between configuration and code—not an archeological expedition."**  
> — The Concept-Labs Philosophy

Every decision in Concept-Labs stems from real pain points we've experienced:

- **Debugging applications where behavior emerged from hidden conventions** → Configuration now tells the complete story
- **Tracking dependencies through framework internals** → Dependencies are explicit and traceable
- **Fighting framework constraints when scaling** → Production requirements drive every design decision
- **Onboarding developers who need weeks to be productive** → New team members understand the system in hours

Concept-Labs exists because **enterprise applications deserve better than magic tricks**. 

**Build with clarity. Deploy with confidence. Scale without surprises.**

---

## Contributing

Your expertise makes us stronger. Here's how to contribute:

1. **Choose your package** - Browse [our repositories](https://github.com/Concept-Labs) and pick your interest
2. **Fork and enhance** - Follow PSR-12 coding standards
3. **Test thoroughly** - Add comprehensive tests for new functionality
4. **Document clearly** - Update README and docs as needed
5. **Submit with context** - Create a pull request with clear description of changes

Each package has specific contribution guidelines in its repository. **Your pull request helps thousands of developers.**

---

## License

MIT License - see individual package repositories for details.

---

**Built with ❤️ and 20 years of hard-won experience.**

---

_Concept-Labs: Enterprise PHP Without the Mystery._
