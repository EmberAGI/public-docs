# Token Swaps

The Ember SDK provides comprehensive support for executing token swaps across different protocols and chains.

## Creating a Swap

Here's how to create a basic token swap:

```rust
use ember_sdk::{CreateSwapParams, SwapType, TokenIdentifier};

async fn execute_swap(client: &EmberClient) -> Result<(), EmberError> {
    let swap_params = CreateSwapParams {
        from_address: "0xYourAddress...".to_string(),
        swap_type: SwapType::MarketSell,
        sell_token: TokenIdentifier {
            chain_id: 1,
            address: "0xTokenToSell...".to_string(),
        },
        buy_token: TokenIdentifier {
            chain_id: 1,
            address: "0xTokenToBuy...".to_string(),
        },
        sell_amount: "100".to_string(),
        slippage_tolerance: 1.0,
        max_fee: "5".to_string(),
    };

    let swap_plan = client.create_swap(swap_params).await?;
    println!("Swap plan created: {:?}", swap_plan);
    
    Ok(())
}
```

## Handling Slippage

The SDK allows you to control slippage tolerance:

```rust
// Set a custom slippage tolerance (0.5%)
let swap_params = CreateSwapParams {
    slippage_tolerance: 0.5,
    // ... other parameters
};
```

## Error Handling

Common swap-related errors to handle:

```rust
match client.create_swap(swap_params).await {
    Ok(plan) => {
        // Process the swap plan
    },
    Err(EmberError::InsufficientLiquidity(msg)) => {
        println!("Not enough liquidity: {}", msg);
    },
    Err(EmberError::SlippageTooHigh(msg)) => {
        println!("Slippage exceeds tolerance: {}", msg);
    },
    Err(e) => {
        println!("Other error: {:?}", e);
    }
}
```

## Best Practices

1. Always set a reasonable slippage tolerance
2. Verify token addresses and amounts before submission
3. Handle all potential error cases
4. Monitor transaction status after submission 