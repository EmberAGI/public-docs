# WebSocket Streaming

Learn how to use WebSocket connections for real-time data streaming with the Ember SDK.

## Setting Up WebSocket Client

Initialize the WebSocket client:

```rust
use ember_sdk::{EmberWebSocket, EmberConfig};

async fn setup_websocket() -> Result<EmberWebSocket, EmberError> {
    let config = EmberConfig::new()
        .with_api_key("your-api-key")
        .with_environment(Environment::Production);
    
    let ws = EmberWebSocket::new(config);
    Ok(ws)
}
```

## Price Streaming

Stream real-time price updates for tokens:

```rust
use ember_sdk::{StreamPricesParams, TokenIdentifier, UpdateFrequency};

async fn stream_prices(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let params = StreamPricesParams {
        tokens: vec![
            TokenIdentifier {
                chain_id: 1,
                address: "0xToken1...".to_string(),
            },
            TokenIdentifier {
                chain_id: 1,
                address: "0xToken2...".to_string(),
            },
        ],
        update_frequency: UpdateFrequency::OneSecond,
    };

    ws.on_price_update(|update| {
        println!("Token: {}", update.token);
        println!("Price: {}", update.price);
        println!("24h Change: {}%", update.change_24h);
    }).await;
    
    Ok(())
}
```

## Position Updates

Stream updates for your DeFi positions:

```rust
use ember_sdk::StreamPositionsParams;

async fn stream_positions(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let params = StreamPositionsParams {
        address: "0xYourAddress...".to_string(),
        position_types: vec![
            "LENDING".to_string(),
            "LIQUIDITY".to_string(),
            "STAKING".to_string(),
        ],
    };

    ws.on_position_update(|update| {
        match update.type_.as_str() {
            "LENDING" => {
                println!("Lending position updated:");
                println!("Health factor: {}", update.details.health_factor);
            },
            "LIQUIDITY" => {
                println!("Liquidity position updated:");
                println!("Earned fees: {:?}", update.details.earned_fees);
            },
            "STAKING" => {
                println!("Staking position updated:");
                println!("Rewards: {}", update.details.rewards.earned);
            },
            _ => println!("Unknown position type update"),
        }
    }).await;
    
    Ok(())
}
```

## Transaction Updates

Monitor transaction status in real-time:

```rust
use ember_sdk::StreamTransactionsParams;

async fn stream_transactions(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let params = StreamTransactionsParams {
        address: "0xYourAddress...".to_string(),
    };

    ws.on_transaction_update(|update| {
        println!("Transaction {} status: {}", update.transaction_hash, update.status);
        match update.status {
            TransactionStatus::Confirmed => {
                println!("Transaction confirmed!");
                println!("Details: {:?}", update.details);
            },
            TransactionStatus::Failed => {
                println!("Transaction failed!");
                println!("Error: {:?}", update.details);
            },
            _ => println!("Transaction status: {:?}", update.status),
        }
    }).await;
    
    Ok(())
}
```

## Error Handling

Handle WebSocket connection issues:

```rust
async fn handle_websocket_errors(ws: &EmberWebSocket) {
    ws.on_error(|error| {
        match error {
            WebSocketError::ConnectionLost => {
                println!("Connection lost, attempting to reconnect...");
                // Implement reconnection logic
            },
            WebSocketError::RateLimitExceeded => {
                println!("Rate limit exceeded, backing off...");
                // Implement backoff logic
            },
            _ => println!("WebSocket error: {:?}", error),
        }
    }).await;
}
```

## Connection Management

Manage WebSocket connections efficiently:

```rust
async fn manage_connection(ws: &EmberWebSocket) {
    // Set up heartbeat monitoring
    ws.set_heartbeat_interval(Duration::from_secs(30));
    
    // Set up automatic reconnection
    ws.set_reconnect_policy(ReconnectPolicy {
        max_retries: 5,
        backoff_multiplier: 1.5,
    });
    
    // Monitor connection status
    ws.on_status_change(|status| {
        match status {
            ConnectionStatus::Connected => {
                println!("WebSocket connected");
            },
            ConnectionStatus::Disconnected => {
                println!("WebSocket disconnected");
            },
            ConnectionStatus::Reconnecting => {
                println!("Attempting to reconnect...");
            },
        }
    }).await;
}
```

## Best Practices

1. Implement proper error handling and reconnection logic
2. Monitor connection health with heartbeats
3. Handle backpressure for high-frequency updates
4. Clean up resources when closing connections
5. Use appropriate update frequencies for your needs
6. Consider connection pooling for multiple subscriptions
7. Implement circuit breakers for error conditions 