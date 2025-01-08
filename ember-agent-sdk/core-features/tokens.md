# Token Operations

Learn how to work with tokens and retrieve token information using the Ember SDK.

## Token Information

Retrieve detailed information about a specific token:

```rust
use ember_sdk::{GetTokenDataParams};

async fn get_token_info(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetTokenDataParams {
        chain_id: 1,
        address: "0xTokenAddress...".to_string(),
    };

    let token_data = client.get_token_data(params).await?;
    
    println!("Token name: {}", token_data.token.name);
    println!("Symbol: {}", token_data.token.symbol);
    println!("Decimals: {}", token_data.token.decimals);
    println!("Market cap: {}", token_data.token.market_data.market_cap);
    println!("24h volume: {}", token_data.token.market_data.volume_24h);
    
    Ok(())
}
```

## Supported Tokens

Get a list of all supported tokens:

```rust
use ember_sdk::{GetAllSupportedTokensParams};

async fn list_supported_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let params = GetAllSupportedTokensParams {
        chain_id: Some(1),
        include_metadata: Some(true),
    };

    let tokens = client.get_all_supported_tokens(params).await?;
    
    for token in tokens.tokens {
        println!("Token: {} ({})", token.name, token.symbol);
        println!("Address: {}", token.address);
        println!("Chain ID: {}", token.chain_id);
        
        if let Some(metadata) = token.metadata {
            println!("Website: {}", metadata.website);
            println!("Twitter: {}", metadata.twitter);
        }
    }
    
    Ok(())
}
```

## Chain Information

Get information about supported blockchain networks:

```rust
async fn get_chain_info(client: &EmberClient) -> Result<(), EmberError> {
    let chains = client.get_all_supported_chains().await?;
    
    for chain in chains.chains {
        println!("Chain: {} (ID: {})", chain.name, chain.chain_id);
        println!("Native currency: {} ({} decimals)", 
            chain.native_currency.symbol,
            chain.native_currency.decimals
        );
        println!("Block explorer: {}", chain.block_explorer);
        println!("Features: {:?}", chain.features);
        println!("Status: {}", chain.status);
    }
    
    Ok(())
}
```

## Token Transfers

Create a token transfer transaction:

```rust
use ember_sdk::{CreateTransferParams};

async fn transfer_tokens(client: &EmberClient) -> Result<(), EmberError> {
    let params = CreateTransferParams {
        chain_id: 1,
        from_address: "0xSenderAddress...".to_string(),
        to_address: "0xRecipientAddress...".to_string(),
        token: "0xTokenAddress...".to_string(),
        amount: "100".to_string(),
        max_fee: "5".to_string(),
    };

    let transfer_plan = client.create_transfer(params).await?;
    println!("Transfer plan created: {:?}", transfer_plan);
    println!("Fee breakdown: {:?}", transfer_plan.fee_breakdown);
    
    Ok(())
}
```

## Token Validation

Validate token addresses and amounts:

```rust
async fn validate_token(client: &EmberClient, token: &str) -> Result<(), EmberError> {
    // Check if token is supported
    let params = GetTokenDataParams {
        chain_id: 1,
        address: token.to_string(),
    };

    match client.get_token_data(params).await {
        Ok(data) => {
            println!("Token is valid:");
            println!("Name: {}", data.token.name);
            println!("Decimals: {}", data.token.decimals);
        },
        Err(EmberError::UnsupportedToken(_)) => {
            println!("Token is not supported");
        },
        Err(e) => {
            println!("Error validating token: {:?}", e);
        }
    }
    
    Ok(())
}
```

## Best Practices

1. Cache token information for frequently used tokens
2. Validate token addresses before creating transactions
3. Handle token decimals correctly when displaying amounts
4. Check token support on target chains before operations
5. Monitor token allowances for contract interactions
6. Keep track of token metadata updates 