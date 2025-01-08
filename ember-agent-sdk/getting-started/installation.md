# Installation & Setup

## Adding the Dependency

Add the Ember Rust SDK to your `Cargo.toml`:

```toml
[dependencies]
ember-sdk = "0.2.0"
```

## Basic Usage

Here's a simple example to get started:

```rust
use ember_sdk::{EmberClient, EmberConfig, Environment};

#[tokio::main]
async fn main() -> Result<(), EmberError> {
    // Create configuration
    let config = EmberConfig::new()
        .with_api_key("your-api-key")
        .with_environment(Environment::Production);
    
    // Initialize client
    let client = EmberClient::new(config);
    
    // The client is now ready to use
    Ok(())
}
```

## Requirements

- Rust 1.56 or higher
- Tokio runtime for async operations
- Valid Ember API key

## Next Steps

Once you have installed the SDK, proceed to [Basic Configuration](configuration.md) to learn about configuring the client for your needs. 