# Yield Farming

Learn how to interact with yield farming protocols using the Ember SDK.

## Depositing into Yield Vaults

Create a deposit into a yield-generating vault:

```rust
use ember_sdk::{DepositYieldParams};

async fn deposit_yield(client: &EmberClient) -> Result<(), EmberError> {
    let params = DepositYieldParams {
        from_address: "0xYourAddress...".to_string(),
        vault_address: "0xVaultAddress...".to_string(),
        token: "0xTokenAddress...".to_string(),
        amount: "1000".to_string(),
        max_fee: "5".to_string(),
    };

    let deposit_plan = client.deposit_yield(params).await?;
    println!("Yield deposit plan created: {:?}", deposit_plan);
    println!("Expected APR: {}", deposit_plan.vault_details.expected_apr);
    
    Ok(())
}
```

## Withdrawing from Yield Vaults

Withdraw funds from a yield position:

```rust
use ember_sdk::{WithdrawYieldParams};

async fn withdraw_yield(client: &EmberClient) -> Result<(), EmberError> {
    let params = WithdrawYieldParams {
        from_address: "0xYourAddress...".to_string(),
        vault_address: "0xVaultAddress...".to_string(),
        share_amount: "500".to_string(),
        max_fee: "5".to_string(),
        recipient: None, // Use from_address as recipient
    };

    let withdrawal_plan = client.withdraw_yield(params).await?;
    println!("Withdrawal plan created: {:?}", withdrawal_plan);
    println!("Earned yield: {}", withdrawal_plan.withdrawal_details.earned_yield);
    
    Ok(())
}
```

## Managing Yield Positions

View all yield farming positions:

```rust
async fn get_yield_positions(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetYieldVaultPositionsParams {
        address: "0xYourAddress...".to_string(),
        protocols: Some(vec!["YEARN".to_string()]),
        chain_ids: Some(vec![1]),
        active: Some(true),
    };

    let positions = client.get_yield_vault_positions(params).await?;
    
    for position in positions.positions {
        println!("Vault: {}", position.vault_address);
        println!("Protocol: {}", position.protocol);
        println!("Deposited: {}", position.deposit_token.amount);
        println!("Current value: {}", position.value);
        println!("APR: {}", position.apr);
        println!("Earned yield: {}", position.earned_yield);
    }
    
    Ok(())
}
```

## Finding Yield Opportunities

Search for available yield vaults:

```rust
async fn find_yield_vaults(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetYieldVaultsParams {
        protocols: Some(vec!["YEARN".to_string(), "CONVEX".to_string()]),
        chain_ids: Some(vec![1]),
        tokens: Some(vec!["0xTokenAddress...".to_string()]),
        min_tvl: Some("1000000".to_string()), // $1M minimum TVL
    };

    let vaults = client.get_yield_vaults(params).await?;
    
    for vault in vaults.vaults {
        println!("Vault: {} ({})", vault.address, vault.protocol);
        println!("Deposit token: {:?}", vault.deposit_token);
        println!("TVL: {}", vault.tvl);
        println!("APR: {:?}", vault.apr);
        println!("Minimum deposit: {}", vault.minimum_deposit);
    }
    
    Ok(())
}
```

## Comparing Opportunities

Find the best yield opportunities across different protocols:

```rust
async fn compare_opportunities(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetAllYieldOpportunitiesParams {
        chain_ids: Some(vec![1]),
        tokens: Some(vec!["0xTokenAddress...".to_string()]),
        min_apr: Some("5.0".to_string()), // 5% minimum APR
        types: Some(vec![
            "STAKING".to_string(),
            "LENDING".to_string(),
            "YIELD_VAULT".to_string(),
        ]),
        min_tvl: Some("1000000".to_string()),
    };

    let opportunities = client.get_all_yield_opportunities(params).await?;
    
    for opp in opportunities.opportunities {
        println!("Type: {}", opp.type_);
        println!("Protocol: {}", opp.protocol);
        println!("APR: {}", opp.apr);
        println!("TVL: {}", opp.tvl);
        println!("Risks: {:?}", opp.risks);
    }
    
    Ok(())
}
```

## Monitoring Yield Performance

Track yield farming performance using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamPositionsParams};

async fn monitor_yield(ws: &EmberWebSocket) -> Result<(), EmberError> {
    let params = StreamPositionsParams {
        address: "0xYourAddress...".to_string(),
        position_types: vec!["YIELD_VAULT".to_string()],
    };

    ws.on_position_update(|update| {
        println!("Position {} updated:", update.position_id);
        println!("Current APR: {}", update.details.apr);
        println!("Earned yield: {}", update.details.earned_yield);
        println!("Value: {}", update.details.value);
        
        // Check for significant APR changes
        if let Some(apr_change) = update.details.apr_change {
            if apr_change.abs() > 1.0 {
                println!("Significant APR change detected!");
            }
        }
    }).await;
    
    Ok(())
}
```

## Best Practices

1. Compare APRs across different protocols and strategies
2. Consider impermanent loss for LP-based yield farming
3. Monitor gas costs relative to expected yields
4. Understand protocol risks and insurance options
5. Track historical APR performance
6. Set up alerts for significant APR changes
7. Consider auto-compounding opportunities
8. Monitor protocol TVL for security 