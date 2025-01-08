# Error Handling

The Ember SDK provides comprehensive error handling through the `EmberError` enum.

## Error Types

```rust
pub enum EmberError {
    InsufficientBalance(String),
    SlippageTooHigh(String),
    PriceImpactTooHigh(String),
    InsufficientLiquidity(String),
    HealthFactorTooLow(String),
    PositionNotFound(String),
    UnsupportedToken(String),
    MarketClosed(String),
    NetworkError(NetworkErrorDetails),
    ValidationError(Vec<ValidationError>),
    RateLimitExceeded(RateLimitDetails),
    Unauthorized(String),
    Forbidden(String),
    ServiceUnavailable(ServiceDetails),
    Other(String),
}
```

## Basic Error Handling

Here's how to handle common errors:

```rust
match client.create_swap(params).await {
    Ok(result) => {
        // Handle successful result
        println!("Success: {:?}", result);
    },
    Err(e) => match e {
        EmberError::InsufficientBalance(msg) => {
            println!("Not enough balance: {}", msg);
        },
        EmberError::SlippageTooHigh(msg) => {
            println!("Slippage too high: {}", msg);
        },
        EmberError::NetworkError(details) => {
            if details.retryable {
                // Implement retry logic
                println!("Retrying in {} ms", details.suggested_wait_time.unwrap_or(1000));
            }
        },
        _ => println!("Other error: {:?}", e),
    }
}
```

## Validation Errors

Handle validation errors with detailed information:

```rust
async fn handle_validation_errors(result: Result<(), EmberError>) {
    if let Err(EmberError::ValidationError(errors)) = result {
        for error in errors {
            println!("Field '{}' error: {}", error.field, error.message);
            println!("Error code: {}", error.error_code);
        }
    }
}
```

## Rate Limiting

Handle rate limit errors:

```rust
match client.create_swap(params).await {
    Err(EmberError::RateLimitExceeded(details)) => {
        println!("Rate limit exceeded!");
        println!("Limit: {}", details.limit);
        println!("Remaining: {}", details.remaining);
        println!("Reset time: {}", details.reset_time);
        
        // Wait until reset time before retrying
        tokio::time::sleep(Duration::from_secs(
            details.reset_time - SystemTime::now()
                .duration_since(UNIX_EPOCH)
                .unwrap()
                .as_secs()
        )).await;
    },
    _ => { /* Handle other cases */ }
}
```

## Network Errors

Handle network-related errors:

```rust
async fn handle_network_errors(client: &EmberClient, params: SwapParams) {
    let mut retries = 0;
    let max_retries = 3;

    loop {
        match client.create_swap(params.clone()).await {
            Err(EmberError::NetworkError(details)) => {
                if details.retryable && retries < max_retries {
                    let wait_time = details.suggested_wait_time.unwrap_or(1000);
                    tokio::time::sleep(Duration::from_millis(wait_time)).await;
                    retries += 1;
                    continue;
                }
                break;
            },
            other => {
                // Handle other results
                break;
            }
        }
    }
}
```

## Best Practices

1. Always handle specific error types relevant to your operation
2. Implement retry logic for retryable errors
3. Log error details for debugging
4. Consider rate limits in your application design
5. Handle validation errors gracefully with user feedback 