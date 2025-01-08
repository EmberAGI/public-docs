# Basic Configuration

The Ember SDK provides flexible configuration options to customize its behavior according to your needs.

## Configuration Options

```rust
use ember_sdk::{EmberConfig, Environment, ApiKeyType, RateLimitConfig, RetryPolicy};

let config = EmberConfig::new()
    .with_api_key("your-api-key")
    .with_environment(Environment::Production)
    .with_key_type(ApiKeyType::Trading)
    .with_rate_limit(RateLimitConfig {
        requests_per_minute: 100,
        burst_limit: 20,
        websocket_connections: 5,
    })
    .with_retry_policy(RetryPolicy {
        max_retries: 3,
        initial_backoff: 1000,
        max_backoff: 16000,
        backoff_multiplier: 2.0,
    });
```

## Environment Settings

The SDK supports different environments:

```rust
// For development and testing
let config = EmberConfig::new()
    .with_environment(Environment::Testnet);

// For production use
let config = EmberConfig::new()
    .with_environment(Environment::Production);
```

## API Key Types

Different API key types provide different levels of access:

```rust
// Read-only access
let config = EmberConfig::new()
    .with_key_type(ApiKeyType::ReadOnly);

// Full trading access
let config = EmberConfig::new()
    .with_key_type(ApiKeyType::Trading);

// Administrative access
let config = EmberConfig::new()
    .with_key_type(ApiKeyType::Admin);
```

## Rate Limiting

Configure rate limits to match your API tier:

```rust
let rate_limit = RateLimitConfig {
    requests_per_minute: 100,
    burst_limit: 20,
    websocket_connections: 5,
};

let config = EmberConfig::new()
    .with_rate_limit(rate_limit);
```

## Retry Policy

Customize retry behavior for failed requests:

```rust
let retry_policy = RetryPolicy {
    max_retries: 3,
    initial_backoff: 1000, // milliseconds
    max_backoff: 16000,    // milliseconds
    backoff_multiplier: 2.0,
};

let config = EmberConfig::new()
    .with_retry_policy(retry_policy);
```

## Complete Configuration Example

Here's a complete example with all configuration options:

```rust
use ember_sdk::{EmberConfig, Environment, ApiKeyType, RateLimitConfig, RetryPolicy};

async fn configure_client() -> EmberClient {
    let config = EmberConfig::new()
        .with_api_key("your-api-key")
        .with_environment(Environment::Production)
        .with_key_type(ApiKeyType::Trading)
        .with_rate_limit(RateLimitConfig {
            requests_per_minute: 100,
            burst_limit: 20,
            websocket_connections: 5,
        })
        .with_retry_policy(RetryPolicy {
            max_retries: 3,
            initial_backoff: 1000,
            max_backoff: 16000,
            backoff_multiplier: 2.0,
        });

    EmberClient::new(config)
}
``` 