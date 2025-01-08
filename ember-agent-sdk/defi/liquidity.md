# Liquidity Provision

Learn how to provide and manage liquidity positions using the Ember SDK.

## Adding Liquidity

Here's how to add liquidity to a DEX pool:

```rust
use ember_sdk::{AddLiquidityParams, TokenAmountDetail};

async fn add_liquidity(client: &EmberClient) -> Result<(), EmberError> {
    let params = AddLiquidityParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "UNISWAP_V3".to_string(),
        pool_address: "0xPoolAddress...".to_string(),
        token0: TokenAmountDetail {
            address: "0xToken0Address...".to_string(),
            amount: "1000".to_string(),
        },
        token1: TokenAmountDetail {
            address: "0xToken1Address...".to_string(),
            amount: "1000".to_string(),
        },
        fee_tier: 3000, // 0.3%
        tick_lower: -887220,
        tick_upper: 887220,
        max_fee: "5".to_string(),
    };

    let liquidity_plan = client.add_liquidity(params).await?;
    println!("Liquidity position created: {:?}", liquidity_plan);
    
    Ok(())
}
```

## Removing Liquidity

Remove liquidity from an existing position:

```rust
use ember_sdk::RemoveLiquidityParams;

async fn remove_liquidity(client: &EmberClient) -> Result<(), EmberError> {
    let params = RemoveLiquidityParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "UNISWAP_V3".to_string(),
        position_id: "123".to_string(),
        percentage: 100, // Remove 100% of position
        max_fee: "5".to_string(),
        recipient: None, // Use from_address as recipient
    };

    let withdrawal_plan = client.remove_liquidity(params).await?;
    println!("Withdrawal plan created: {:?}", withdrawal_plan);
    
    Ok(())
}
```

## Managing Positions

Retrieve all liquidity positions:

```rust
async fn get_positions(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetLiquidityPositionsParams {
        address: "0xYourAddress...".to_string(),
        protocols: Some(vec!["UNISWAP_V3".to_string()]),
        chain_ids: Some(vec![1]),
        active: Some(true),
    };

    let positions = client.get_liquidity_positions(params).await?;
    
    for position in positions.positions {
        println!("Position ID: {}", position.position_id);
        println!("Protocol: {}", position.protocol);
        println!("APR: {}", position.apr);
        println!("Earned fees: {:?}", position.earned_fees);
    }
    
    Ok(())
}
```

## Finding Liquidity Pools

Search for available liquidity pools:

```rust
async fn find_pools(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetLiquidityPoolsParams {
        protocols: Some(vec!["UNISWAP_V3".to_string()]),
        chain_ids: Some(vec![1]),
        tokens: Some(vec![
            "0xToken0Address...".to_string(),
            "0xToken1Address...".to_string(),
        ]),
        min_tvl: Some("1000000".to_string()), // $1M minimum TVL
    };

    let pools = client.get_liquidity_pools(params).await?;
    
    for pool in pools.pools {
        println!("Pool address: {}", pool.address);
        println!("TVL: {}", pool.tvl);
        println!("24h Volume: {}", pool.volume_24h);
        println!("APR: {:?}", pool.apr);
    }
    
    Ok(())
}
```

## Position Monitoring

Monitor position performance using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamPositionsParams};

async fn monitor_positions(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let stream_params = StreamPositionsParams {
        address: "0xYourAddress...".to_string(),
        position_types: vec!["LIQUIDITY".to_string()],
    };

    ws.on_position_update(|update| {
        println!("Position {} updated:", update.position_id);
        println!("Current APR: {}", update.details.apr);
        println!("Earned fees: {:?}", update.details.earned_fees);
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Choose appropriate price ranges for concentrated liquidity
2. Monitor and adjust positions based on market conditions
3. Consider impermanent loss when providing liquidity
4. Track earned fees and APR for position management
5. Set reasonable gas limits for liquidity operations
6. Understand fee tier implications for different pools 