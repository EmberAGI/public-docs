# Lending & Borrowing

Learn how to interact with lending protocols using the Ember SDK.

## Lending Tokens

Here's how to lend tokens to a protocol:

```rust
use ember_sdk::{LendTokensParams};

async fn lend_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let lend_params = LendTokensParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "AAVE_V3".to_string(),
        token: "0xTokenAddress...".to_string(),
        amount: "1000".to_string(),
        interest_rate_mode: "VARIABLE".to_string(),
        enable_collateral: true,
        max_fee: "5".to_string(),
    };

    let lending_plan = client.lend_tokens(lend_params).await?;
    println!("Lending plan created: {:?}", lending_plan);
    
    Ok(())
}
```

## Borrowing Tokens

Example of borrowing tokens against collateral:

```rust
use ember_sdk::{BorrowTokensParams};

async fn borrow_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let borrow_params = BorrowTokensParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "AAVE_V3".to_string(),
        borrow_token: "0xTokenToBorrow...".to_string(),
        borrow_amount: "500".to_string(),
        interest_rate_mode: "VARIABLE".to_string(),
        max_fee: "5".to_string(),
    };

    let borrowing_plan = client.borrow_tokens(borrow_params).await?;
    println!("Borrowing plan created: {:?}", borrowing_plan);
    
    Ok(())
}
```

## Managing Positions

Retrieve all lending positions:

```rust
async fn get_positions(client: &EmberClient) -> Result<(), EmberError> {
    let positions_params = GetLendingPositionsParams {
        address: "0xYourAddress...".to_string(),
        protocols: Some(vec!["AAVE_V3".to_string()]),
        chain_ids: Some(vec![1]),
        active: Some(true),
    };

    let positions = client.get_lending_positions(positions_params).await?;
    
    for position in positions.positions {
        println!("Position: {:?}", position);
    }
    
    Ok(())
}
```

## Health Factor Monitoring

Monitor position health using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamPositionsParams};

async fn monitor_health_factor(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let stream_params = StreamPositionsParams {
        address: "0xYourAddress...".to_string(),
        position_types: vec!["LENDING".to_string()],
    };

    let mut position_stream = ws.stream_positions(stream_params).await?;
    
    ws.on_position_update(|update| {
        if let Some(health_factor) = update.details.health_factor {
            if health_factor < "1.1" {
                println!("Warning: Low health factor!");
            }
        }
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Always monitor health factors for borrowed positions
2. Set up alerts for significant APR changes
3. Understand the liquidation parameters of the protocol
4. Keep track of accumulated interest
5. Consider gas costs when planning small transactions 