# Concept‑Labs

## Experimental Laboratory for PHP Concepts and Patterns

**Concept-Labs is a laboratory where we experiment with PHP architecture patterns, design concepts, and modern development approaches.**

[![Concept](https://img.shields.io/badge/Concept-ecosystem-violet.svg)](https://github.com/Concept-Labs)

> This is our experimental ground where concepts are born, tested, refined, and validated before moving to production systems.

---

## What is Concept-Labs?

Concept-Labs is **a laboratory of concepts** — an experimental environment where we explore, prototype, and validate modern PHP architecture patterns and development approaches.

After concepts go through experimentation, testing, and architectural validation here, they find their way into production projects and systems, such as:

- **[juzdy](https://github.com/juzdy)** - Production implementations of validated concepts
- And other production systems

**Our Approach:** Experiment boldly, test thoroughly, validate rigorously, then deploy to production.

### Core Principles

1. **🔬 Experimentation First** - Try new ideas, validate concepts, learn from failures
2. **🧪 Test-Driven Validation** - Every concept must prove itself through rigorous testing
3. **📐 Architecture Evolution** - Patterns emerge from real problems, not theory
4. **🚀 Production Path** - Successful experiments graduate to production systems

This repository serves as the **proving ground** where architectural ideas are transformed into battle-tested solutions.

---

## Experimental Focus Areas

### Configuration System Research
Exploring transparent configuration approaches where architecture is self-documenting. Experimenting with JSON, PHP, and evaluating XML/YAML patterns. Focus on explicitness and version control friendliness.

### Dependency Management Studies
Investigating PSR-11 container patterns with explicit preference systems. Testing approaches to eliminate hidden dependencies and improve code traceability.

### Event-Driven Architecture
Researching PSR-14 event systems for loose coupling. Validating patterns for component communication and system coordination.

### HTTP Stack Experiments
Developing PSR-7/PSR-17 message implementations for PHP 8.2+. Testing middleware pipeline architectures and priority-based routing patterns. Targeting long-running process environments (RoadRunner, Swoole).

### Database Layer Prototypes
Experimenting with fluent query builders and ORM patterns. Focus on security, maintainability, and transparent object mapping.

---

## Current Experiments

Our laboratory includes multiple experimental packages exploring different architectural concepts:

### Foundation Layer
- **[@Concept-Labs/config](https://github.com/Concept-Labs/config)** - Configuration system experiments with JSON DSL, environment variables, and cross-references
- **[@Concept-Labs/singularity](https://github.com/Concept-Labs/singularity)** - PSR-11 dependency container with explicit preference patterns

### Event Communication
- **[@Concept-Labs/event-dispatcher](https://github.com/Concept-Labs/event-dispatcher)** - PSR-14 event system prototypes for component decoupling

### HTTP Stack
- **[@Concept-Labs/http-message](https://github.com/Concept-Labs/http-message)** - PSR-7/PSR-17 HTTP message implementations for PHP 8.2+
- **[@Concept-Labs/http](https://github.com/Concept-Labs/http)** - Priority-based middleware pipeline experiments
- **[@Concept-Labs/simple-http](https://github.com/Concept-Labs/simple-http)** - Quick-start template for HTTP applications

### Data Layer
- **[@Concept-Labs/dbal](https://github.com/Concept-Labs/dbal)** - Fluent query builder patterns with SQL injection prevention
- **[@Concept-Labs/orm](https://github.com/Concept-Labs/orm)** - Object-relational mapping experiments

### Utilities
- **[@Concept-Labs/arrays](https://github.com/Concept-Labs/arrays)** - Advanced array manipulation with dot notation
- **[@Concept-Labs/cli](https://github.com/Concept-Labs/cli)** - Command-line interface patterns
- **[@Concept-Labs/simple-cache](https://github.com/Concept-Labs/simple-cache)** - PSR-16 caching implementations

**70+ experimental packages** — Each exploring specific concepts and patterns.

---

## Research Background

This experimental work builds on decades of PHP development experience:

- **20+ Years of Architecture Patterns** - Foundation: [Victor Galitsky](https://github.com/vgalitsky)'s enterprise development experience
- **Framework Analysis** - Studying limitations and opportunities in existing PHP frameworks
- **Modern PHP Capabilities** - Leveraging PHP 8.2+ features for new architectural patterns
- **Production Validation** - Concepts proven in production systems before broader adoption

**The Process:** Ideas are prototyped here, tested rigorously, refined through experimentation, and validated in production environments like [juzdy](https://github.com/juzdy) and [virsh-online](https://github.com/virsh-online).

---

## Try Experimental Packages

To explore our experimental HTTP stack:

```bash
composer create-project concept-labs/simple-http my-experiment
cd my-experiment && cp etc/default.json.dist etc/default.json
php -S localhost:8000 -t pub
```

**Note:** These are experimental packages. For production use, consider validated implementations in [juzdy](https://github.com/juzdy) or [virsh-online](https://github.com/virsh-online).

[→ View Quick Start Guide](../docs/quick-start.md) | [→ Explore Architecture](../docs/architecture.md)

---

## Documentation

### 📘 Getting Started
- **[Quick Start Guide](../docs/quick-start.md)** - Setup experimental environment
- **[Architecture Overview](../docs/architecture.md)** - Understand experimental design patterns

### 🛠️ Core Experiments
- **[Configuration System](../docs/configuration.md)** - JSON DSL experiments
- **[Dependency Injection](../docs/dependency-injection.md)** - Singularity DI patterns
- **[HTTP Stack](../docs/http-stack.md)** - Middleware architecture experiments
- **[Event System](../docs/events.md)** - Event-driven patterns
- **[Database Layer](../docs/database.md)** - DBAL/ORM experiments

### 📚 Additional Resources
- **[Testing Guide](../docs/testing.md)** - Validation methodologies
- **Package Documentation** - Detailed experiments in each package repository

---

## Built on PSR Standards

All experiments follow industry standards for maximum compatibility:

✅ **PSR-3** - Logging  
✅ **PSR-7** - HTTP Messages  
✅ **PSR-11** - Dependency Injection Container  
✅ **PSR-14** - Event Dispatcher  
✅ **PSR-15** - HTTP Server Request Handlers  
✅ **PSR-17** - HTTP Factories  
✅ **PSR-18** - HTTP Client  

---

## Who Uses These Concepts?

### ✅ Experimental Usage:
- **Researchers and developers** exploring modern PHP patterns
- **Teams evaluating** architectural approaches before production adoption
- **Students and learners** studying contemporary PHP design
- **Contributors** interested in PHP ecosystem evolution

### 🚀 Production Implementations:
For production-ready implementations of these concepts, see:
- **[juzdy](https://github.com/juzdy)** - Production system using validated patterns
- **[virsh-online](https://github.com/virsh-online)** - Real-world application implementations
- Other projects adopting proven concepts

**Important:** Concept-Labs is an experimental laboratory. For production use, consider the validated implementations in downstream projects.

---

## Participate in Research

- **🔬 Explore Experiments**: [github.com/Concept-Labs](https://github.com/Concept-Labs) — 70+ experimental repositories
- **👨‍💻 Research Lead**: [Victor Galitsky](https://github.com/vgalitsky) — 20+ years PHP architecture experience
- **🐛 Report Findings**: Discovered an issue? Open an issue in the specific package repository
- **💬 Discuss Concepts**: Share insights and ideas in package discussions
- **🤝 Contribute**: Every experiment welcomes contributions—see individual repos for guidelines

**Interested in production implementations?** Check out [juzdy](https://github.com/juzdy) and [virsh-online](https://github.com/virsh-online).

---

## Laboratory Philosophy

> **"Software architecture should be an iterative laboratory process—not a marketing promise."**  
> — The Concept-Labs Approach

Our methodology:

- **Prototype in the laboratory** → Test ideas without production constraints
- **Validate through experimentation** → Rigorous testing and architectural review
- **Refine based on feedback** → Iterate on concepts based on real usage
- **Graduate to production** → Proven concepts move to projects like [juzdy](https://github.com/juzdy) and [virsh-online](https://github.com/virsh-online)

Concept-Labs exists as **a safe space for architectural experimentation** where ideas can fail fast or succeed thoroughly before production adoption.

**Experiment boldly. Validate thoroughly. Deploy confidently.**

---

## Contributing to Research

Your insights strengthen our experiments:

1. **Choose an experiment** - Browse [our repositories](https://github.com/Concept-Labs) and select your area of interest
2. **Fork and experiment** - Follow PSR-12 coding standards
3. **Validate thoroughly** - Add comprehensive tests for new concepts
4. **Document findings** - Update README and documentation with insights
5. **Share results** - Create a pull request describing your experimental findings

Each experimental package has specific contribution guidelines. **Your research helps the entire PHP community.**

---

## License

MIT License - see individual package repositories for details.

---

**Research foundation with 20+ years of architectural experience.**

---

_Concept-Labs: Experimental Laboratory for PHP Architecture._
