# Rhai Repository Overview

## What is Rhai?

Rhai is an embedded scripting language and evaluation engine for Rust that provides a safe, fast, and easy way to add scripting capabilities to Rust applications. It combines the familiarity of JavaScript-like syntax with Rust's safety principles, making it ideal for applications that need user-defined logic or configuration.

## Key Features

### Language Characteristics
- **Simple Syntax**: JavaScript+Rust-like syntax with dynamic typing
- **Performance**: Efficient evaluation (1 million iterations in ~0.14 seconds)
- **Safety**: "Don't Panic" guarantee - any panic is considered a bug
- **Sandboxing**: Protected against malicious scripts and resource exhaustion
- **No-std Compatible**: Can run in embedded environments

### Integration Capabilities
- **Tight Rust Integration**: Direct binding with Rust functions, types, and methods
- **Custom Types**: Register Rust structs with getters, setters, and methods
- **Function Overloading**: Support for multiple function signatures
- **Closures**: Anonymous functions that can capture external variables
- **Modules**: Organize code with dynamically-loadable modules

### Advanced Features
- **Custom Syntax**: Extend the language with domain-specific constructs
- **Debugging Interface**: Built-in debugging and introspection capabilities
- **AST Compilation**: Compile-once, evaluate-many for performance
- **Optimization**: Built-in script optimizer for better performance
- **Serialization**: serde support for data interchange

## Specific Use Cases

### 1. **Configuration and Settings Management**
Replace static configuration files with dynamic scripts that can contain logic.

```rust
// Allow users to define complex configuration logic
let config_script = r#"
    const BASE_URL = "https://api.example.com";
    
    fn get_endpoint(env) {
        if env == "production" {
            BASE_URL + "/v2/prod"
        } else {
            BASE_URL + "/v1/dev"
        }
    }
    
    // Return configuration object
    #{
        timeout: 30,
        retries: 3,
        endpoint: get_endpoint(ENV)
    }
"#;
```

### 2. **Event Handler Systems with State**
Build interactive applications that respond to events while maintaining state.

```rhai
// Event handler script with persistent state
let score = 0;
let multiplier = 1;

fn on_player_action(action, points) {
    match action {
        "hit" => {
            score += points * multiplier;
            multiplier += 0.1;
        },
        "miss" => {
            multiplier = 1.0;
        }
    }
    
    print(`Score: ${score}, Multiplier: ${multiplier}`);
}
```

### 3. **Plugin and Extension Systems**
Allow users to extend application functionality through scripts.

```rust
// Host application registers API
engine.register_fn("create_widget", create_widget);
engine.register_fn("send_notification", send_notification);

// User plugin script
let user_plugin = r#"
    fn on_user_login(user) {
        let welcome_widget = create_widget("welcome", user.name);
        send_notification("admin", `${user.name} logged in`);
    }
"#;
```

### 4. **Game Scripting and AI**
Implement game logic, AI behavior, and level scripts.

```rhai
// NPC behavior script
let health = 100;
let aggression = 0.5;

fn update_ai(player_distance, player_health) {
    if player_distance < 10 && player_health < 50 {
        aggression += 0.2;
        return "attack";
    } else if health < 30 {
        return "flee";
    } else {
        return "patrol";
    }
}
```

### 5. **Data Processing and Transformation**
Process and transform data using user-defined logic.

```rhai
// Data transformation pipeline
fn process_record(record) {
    // Clean and validate data
    let email = record.email.trim().to_lower();
    
    if !email.contains("@") {
        throw "Invalid email: " + email;
    }
    
    // Transform data
    #{
        id: record.id,
        email: email,
        full_name: record.first_name + " " + record.last_name,
        created_at: timestamp()
    }
}
```

### 6. **Template Systems and Code Generation**
Generate code, documentation, or other text-based content.

```rhai
// Template for generating API documentation
fn generate_api_doc(endpoint) {
    let method = endpoint.method.to_upper();
    
    `## ${method} ${endpoint.path}
    
    **Description**: ${endpoint.description}
    
    **Parameters**:
    ${endpoint.params.map(|p| `- ${p.name}: ${p.type}`).join("\n")}
    
    **Response**: ${endpoint.response_type}
    `
}
```

### 7. **Business Rules and Workflow Engines**
Implement complex business logic that can be modified without recompiling.

```rhai
// Loan approval business rules
fn evaluate_loan(application) {
    let score = 0;
    
    // Credit score evaluation
    if application.credit_score > 750 {
        score += 40;
    } else if application.credit_score > 650 {
        score += 20;
    }
    
    // Income evaluation
    let debt_ratio = application.monthly_debt / application.monthly_income;
    if debt_ratio < 0.3 {
        score += 30;
    } else if debt_ratio < 0.4 {
        score += 10;
    }
    
    // Final decision
    if score >= 50 {
        "approved"
    } else if score >= 30 {
        "review_required"
    } else {
        "denied"
    }
}
```

### 8. **Domain-Specific Languages (DSLs)**
Create specialized languages for specific problem domains.

```rhai
// Custom DSL for database queries (simplified example)
fn query_builder() {
    select("user_id", "name", "email")
        .from("users")
        .where("active = true")
        .order_by("created_at DESC")
        .limit(100)
}
```

### 9. **Interactive Applications and REPLs**
Build interactive command-line tools and calculators.

```rhai
// Mathematical expression evaluator
fn calculate(expression) {
    // Parse and evaluate mathematical expressions
    eval(expression)
}

// Usage in REPL
// > calculate("2 + 3 * 4")
// 14
```

### 10. **Automation and Task Scripting**
Automate repetitive tasks and workflows.

```rhai
// Deployment automation script
fn deploy_service(env, version) {
    let config = load_config(env);
    
    print(`Deploying version ${version} to ${env}`);
    
    // Pre-deployment checks
    if !health_check(config.health_endpoint) {
        throw "Health check failed before deployment";
    }
    
    // Deploy
    deploy_container(config.registry, version);
    
    // Post-deployment verification
    sleep(5000); // Wait 5 seconds
    
    if !health_check(config.health_endpoint) {
        rollback();
        throw "Deployment failed health check";
    }
    
    print("Deployment successful!");
}
```

## Why Choose Rhai?

### Safety and Security
- **Memory Safe**: Leverages Rust's memory safety guarantees
- **Sandboxed Execution**: Scripts cannot access unauthorized resources
- **Resource Protection**: Built-in protections against infinite loops, stack overflow, and excessive memory usage
- **No Unsafe Operations**: Scripts cannot perform dangerous system operations

### Performance
- **Compiled AST**: Scripts are compiled to an intermediate representation for faster execution
- **Optimization**: Built-in optimizer improves script performance
- **Minimal Overhead**: Low-level integration with host Rust application
- **Efficient Memory Usage**: Optimized for minimal memory footprint

### Developer Experience
- **Familiar Syntax**: Easy to learn for developers familiar with JavaScript or Rust
- **Rich Tooling**: Language server, VS Code plugin, and debugging support
- **Comprehensive Documentation**: Extensive documentation and examples
- **Active Community**: Well-maintained with regular updates and community support

### Flexibility
- **Modular Design**: Include only the features you need
- **Custom Extensions**: Easy to add domain-specific functionality
- **Multiple Build Targets**: Supports std, no-std, and WebAssembly
- **Feature Flags**: Extensive customization through Cargo features

## When to Use Rhai

Rhai is particularly well-suited for:

- **Applications requiring user-defined logic** (game mods, business rules)
- **Configuration systems** that need more than static files
- **Plugin architectures** where users extend functionality
- **Embedded systems** needing scripting capabilities
- **Interactive applications** requiring dynamic behavior
- **Data processing pipelines** with user-defined transformations
- **Template and code generation** systems
- **Educational projects** teaching scripting concepts

## Repository Structure

- **`src/`**: Core engine implementation
  - **`engine.rs`**: Main scripting engine
  - **`parser.rs`**: Script parser and lexer
  - **`eval/`**: AST evaluation logic
  - **`api/`**: Public API surface
  - **`packages/`**: Built-in function packages
- **`examples/`**: Comprehensive examples for different use cases
- **`scripts/`**: Sample Rhai scripts demonstrating language features
- **`tests/`**: Extensive test suite
- **`benches/`**: Performance benchmarks

The repository demonstrates a mature, well-architected scripting engine with real-world applicability across numerous domains, from simple configuration to complex interactive applications.