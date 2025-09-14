# Concept-Labs

Welcome to Concept-Labs - A collection of PHP libraries focused on modern web development standards and PSR compliance.

## Overview

Concept-Labs provides a comprehensive ecosystem of PHP packages designed to build robust, PSR-compliant web applications. Our libraries focus on dependency injection, HTTP handling, configuration management, and more.

## Core Repositories

### 🔧 [Singularity](https://github.com/Concept-Labs/singularity)
**PSR-11 Container Implementation**
- **Package**: `concept-labs/singularity`
- **Description**: Dependency injection container implementing PSR-11 Container Interface
- **Keywords**: PHP, PSR-11, Dependency Injection, Container
- **License**: MIT
- **PHP Version**: ≥8.2

A powerful dependency injection container that manages your application's dependencies and provides automatic resolution.

### ⚙️ [Config](https://github.com/Concept-Labs/config)
**Configuration Manager**
- **Package**: `concept-labs/config`
- **Description**: Advanced configuration management system
- **Keywords**: PHP, Config
- **License**: MIT
- **PHP Version**: ≥8.2

Flexible configuration management with support for multiple formats and environments.

### 🌐 [HTTP](https://github.com/Concept-Labs/http)
**HTTP Application Framework**
- **Package**: `concept-labs/http`
- **Description**: Modern HTTP application framework
- **Keywords**: PHP, HTTP Application
- **License**: Apache-2.0
- **PHP Version**: ≥8.2

A robust HTTP framework built on PSR standards for building web applications and APIs.

### 🚀 [Simple HTTP](https://github.com/Concept-Labs/simple-http)
**Simple HTTP Application**
- **Package**: `concept-labs/simple-http`
- **Description**: Simplified HTTP application builder
- **Keywords**: PHP, HTTP Application
- **License**: Apache-2.0
- **PHP Version**: ≥8.2

A streamlined approach to building HTTP applications with session support.

## Supporting Libraries

### 💾 [Simple Cache](https://github.com/Concept-Labs/simple-cache)
**PSR-16 Simple Cache Implementation**
- **Package**: `concept-labs/simple-cache`
- **Description**: PSR-16 compliant caching solution
- **License**: MIT

### 📦 [Composer](https://github.com/Concept-Labs/composer)
**Composer Helper**
- **Package**: `concept-labs/composer`
- **Description**: Utilities and helpers for Composer operations
- **License**: MIT

### 🔄 [Arrays](https://github.com/Concept-Labs/arrays)
**Array Utilities**
- **Package**: `concept-labs/arrays`
- **Description**: Advanced array manipulation and utilities API
- **License**: MIT

### 📨 [HTTP Message](https://github.com/Concept-Labs/http-message)
**HTTP Message Implementation**
- **Package**: `concept-labs/http-message`
- **Description**: PSR-7 HTTP message implementation
- **License**: Apache-2.0

### 📡 [Event Dispatcher](https://github.com/Concept-Labs/event-dispatcher)
**PSR-14 Event Dispatcher**
- **Package**: `concept-labs/event-dispatcher`
- **Description**: PSR-14 compliant event dispatcher implementation
- **License**: MIT

### 🔐 [HTTP Session](https://github.com/Concept-Labs/http-session)
**HTTP Session Management**
- **Package**: `concept-labs/http-session`
- **Description**: PSR-compatible HTTP session management
- **License**: Apache-2.0

## Architecture & Dependencies

The Concept-Labs ecosystem follows a modular architecture where components can be used independently or together:

```
simple-http
    ├── http
    │   ├── singularity
    │   │   ├── config
    │   │   │   └── arrays
    │   │   ├── simple-cache
    │   │   └── composer
    │   ├── http-message
    │   └── event-dispatcher
    └── http-session
```

## Installation

All packages are available via Composer:

```bash
# Core dependency injection
composer require concept-labs/singularity

# Configuration management
composer require concept-labs/config

# HTTP framework
composer require concept-labs/http

# Simple HTTP application
composer require concept-labs/simple-http

# Individual components
composer require concept-labs/simple-cache
composer require concept-labs/arrays
composer require concept-labs/http-message
composer require concept-labs/event-dispatcher
composer require concept-labs/http-session
```

## Getting Started

### Basic HTTP Application

```php
<?php
use Concept\SimpleHttp\Application;

$app = new Application();

$app->get('/', function() {
    return 'Hello, World!';
});

$app->run();
```

### Using Dependency Injection

```php
<?php
use Concept\Singularity\Container;
use Concept\Config\ConfigManager;

$container = new Container();
$config = new ConfigManager();

// Configure your application
$container->bind('config', $config);
```

## PSR Compliance

Our libraries follow PHP Standards Recommendations (PSR):

- **PSR-11**: Container Interface (Singularity)
- **PSR-14**: Event Dispatcher (Event Dispatcher)
- **PSR-16**: Simple Cache (Simple Cache)
- **PSR-7**: HTTP Message Interface (HTTP Message)
- **PSR-15**: HTTP Server Request Handlers (HTTP)

## Requirements

- **PHP**: ≥8.2
- **Composer**: For dependency management

## Contributing

We welcome contributions to any of our repositories. Please:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Add tests if applicable
5. Submit a pull request

## License

- **MIT Licensed**: singularity, config, simple-cache, composer, arrays, event-dispatcher
- **Apache-2.0 Licensed**: http, simple-http, http-message, http-session

## Author

**Viktor Halytskyi**
- Email: concept.galitsky@gmail.com / info.concept.labs@gmail.com
- Organization: Concept-Labs

---

*Building modern PHP applications with PSR standards and clean architecture.* 
