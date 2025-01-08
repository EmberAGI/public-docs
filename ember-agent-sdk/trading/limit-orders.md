# Limit Orders

Learn how to create and manage limit orders using the Ember SDK.

## Creating a Limit Order

Here's how to create a basic limit order:

```rust
use ember_sdk::{CreateLimitOrderParams, TokenIdentifier};

async fn create_limit_order(client: &EmberClient) -> Result<(), EmberError> {
    let order_params = CreateLimitOrderParams {
        from_address: "0xYourAddress...".to_string(),
        sell_token: TokenIdentifier {
            chain_id: 1,
            address: "0xTokenToSell...".to_string(),
        },
        buy_token: TokenIdentifier {
            chain_id: 1,
            address: "0xTokenToBuy...".to_string(),
        },
        sell_amount: "1000".to_string(),
        limit_price: "1.5".to_string(), // Price in terms of buy token
        expiration: "2024-12-31T23:59:59Z".to_string(),
        max_fee: "5".to_string(),
    };

    let order_plan = client.create_limit_order(order_params).await?;
    println!("Limit order created: {:?}", order_plan);
    
    Ok(())
}
```

## Managing Limit Orders

Retrieve all your limit orders:

```rust
async fn get_orders(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetLimitOrdersParams {
        address: "0xYourAddress...".to_string(),
        // Optional filters
        chain_ids: Some(vec![1]),
        status: Some("OPEN".to_string()),
    };

    let orders = client.get_limit_orders(params).await?;
    
    for order in orders.orders {
        println!("Order ID: {}", order.order_id);
        println!("Status: {}", order.status);
        println!("Filled Amount: {}", order.filled_amount);
        println!("Remaining Amount: {}", order.remaining_amount);
    }
    
    Ok(())
}
```

## Canceling Orders

Cancel an existing limit order:

```rust
async fn cancel_order(client: &EmberClient, order_id: String) -> Result<(), EmberError> {
    let cancel_params = CancelLimitOrderParams {
        from_address: "0xYourAddress...".to_string(),
        order_id,
        max_fee: "5".to_string(),
    };

    let cancel_plan = client.cancel_limit_order(cancel_params).await?;
    println!("Order cancellation plan: {:?}", cancel_plan);
    
    Ok(())
}
```

## Order Status Monitoring

Monitor order status using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamOrdersParams};

async fn monitor_orders(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let stream_params = StreamOrdersParams {
        address: "0xYourAddress...".to_string(),
    };

    ws.on_order_update(|update| {
        match update.status.as_str() {
            "FILLED" => println!("Order {} filled!", update.order_id),
            "PARTIALLY_FILLED" => println!(
                "Order {} partially filled: {}",
                update.order_id,
                update.filled_amount
            ),
            "EXPIRED" => println!("Order {} expired", update.order_id),
            _ => println!("Order {} status: {}", update.order_id, update.status),
        }
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Set reasonable expiration times for orders
2. Monitor order status for timely updates
3. Consider gas costs when setting limit prices
4. Handle partial fills appropriately
5. Implement proper error handling for order management
6. Keep track of open orders to avoid conflicts 