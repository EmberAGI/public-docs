# Staking

Learn how to stake tokens and manage staking positions using the Ember SDK.

## Staking Tokens

Here's how to stake tokens in a protocol:

```rust
use ember_sdk::StakeTokensParams;

async fn stake_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let params = StakeTokensParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "LIDO".to_string(),
        token: "0xTokenAddress...".to_string(),
        amount: "1000".to_string(),
        max_fee: "5".to_string(),
    };

    let staking_plan = client.stake_tokens(params).await?;
    println!("Staking plan created: {:?}", staking_plan);
    
    Ok(())
}
```

## Unstaking Tokens

Withdraw tokens from a staking position:

```rust
use ember_sdk::UnstakeTokensParams;

async fn unstake_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let params = UnstakeTokensParams {
        from_address: "0xYourAddress...".to_string(),
        protocol: "LIDO".to_string(),
        token: "0xTokenAddress...".to_string(),
        amount: "500".to_string(), // or "MAX" for full withdrawal
        max_fee: "5".to_string(),
        recipient: None, // Use from_address as recipient
    };

    let unstaking_plan = client.unstake_tokens(params).await?;
    println!("Unstaking plan created: {:?}", unstaking_plan);
    
    Ok(())
}
```

## Managing Staking Positions

Retrieve all staking positions:

```rust
async fn get_positions(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetStakingPositionsParams {
        address: "0xYourAddress...".to_string(),
        protocols: Some(vec!["LIDO".to_string()]),
        chain_ids: Some(vec![1]),
        active: Some(true),
    };

    let positions = client.get_staking_positions(params).await?;
    
    for position in positions.positions {
        println!("Position ID: {}", position.position_id);
        println!("Protocol: {}", position.protocol);
        println!("Staked amount: {}", position.staked_token.amount);
        println!("APR: {}", position.apr);
        println!("Rewards earned: {}", position.rewards.earned);
    }
    
    Ok(())
}
```

## Finding Staking Pools

Search for available staking opportunities:

```rust
async fn find_pools(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetStakingPoolsParams {
        protocols: Some(vec!["LIDO".to_string()]),
        chain_ids: Some(vec![1]),
        tokens: Some(vec!["0xTokenAddress...".to_string()]),
        min_tvl: Some("1000000".to_string()), // $1M minimum TVL
    };

    let pools = client.get_staking_pools(params).await?;
    
    for pool in pools.pools {
        println!("Protocol: {}", pool.protocol);
        println!("Input token: {:?}", pool.input_token);
        println!("APR: {:?}", pool.apr);
        println!("TVL: {}", pool.tvl);
        println!("Minimum stake: {}", pool.minimum_stake);
    }
    
    Ok(())
}
```

## Reward Monitoring

Monitor staking rewards using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamPositionsParams};

async fn monitor_rewards(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let stream_params = StreamPositionsParams {
        address: "0xYourAddress...".to_string(),
        position_types: vec!["STAKING".to_string()],
    };

    ws.on_position_update(|update| {
        println!("Position {} updated:", update.position_id);
        println!("Current APR: {}", update.details.apr);
        println!("Accumulated rewards: {}", update.details.rewards.earned);
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Understand lockup periods before staking
2. Monitor reward rates and APR changes
3. Consider gas costs when claiming rewards
4. Keep track of tax implications
5. Verify supported tokens and minimum amounts
6. Monitor protocol risks and insurance coverage
7. Set up alerts for significant APR changes 