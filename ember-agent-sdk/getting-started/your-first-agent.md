# Your First Agent

This guide demonstrates how to create your first agent using the Ember SDK and Rig.

## Setup

First, let's set up our dependencies:

```toml:Cargo.toml
[dependencies]
rig = "0.1.0"
ember-sdk = "0.1.0"
tokio = { version = "1.0", features = ["full"] }
serde = { version = "1.0", features = ["derive"] }
serde_json = "1.0"
```

## Creating a Swap Tool

Let's create a tool that Rig agents can use to execute swaps:

```rust:src/tools/swap_tool.rs
use ember_sdk::{EmberClient, CreateSwapParams, SwapType, TokenIdentifier};
use rig::tool::{Tool, ToolError};
use serde::{Deserialize, Serialize};

#[derive(Debug)]
pub struct SwapTool {
    client: EmberClient,
}

#[derive(Deserialize, Serialize)]
pub struct SwapParams {
    from_token: String,
    to_token: String,
    amount: String,
    slippage: f64,
}

impl SwapTool {
    pub fn new(client: EmberClient) -> Self {
        Self { client }
    }
}

impl Tool for SwapTool {
    type Input = SwapParams;
    type Output = String;
    type Error = ToolError;

    async fn call(&self, input: Self::Input) -> Result<Self::Output, Self::Error> {
        let swap_params = CreateSwapParams {
            from_address: "YOUR_SOLANA_ADDRESS".to_string(), // Replace with actual address
            swap_type: SwapType::MarketSell,
            sell_token: TokenIdentifier {
                chain_id: 101, // Solana chain ID
                address: input.from_token,
            },
            buy_token: TokenIdentifier {
                chain_id: 101,
                address: input.to_token,
            },
            sell_amount: input.amount,
            slippage_tolerance: input.slippage,
            max_fee: "5".to_string(),
        };

        // Get the swap plan
        let swap_plan = self.client.create_swap(swap_params).await
            .map_err(|e| ToolError::Custom(format!("Failed to create swap: {}", e)))?;

        // The unsigned transaction that needs to be signed
        let unsigned_tx = swap_plan.transaction_plan;
        
        // Here you would typically sign the transaction with your Solana wallet
        // For this example, we'll just return the transaction plan
        Ok(format!("Swap plan created: {:?}", unsigned_tx))
    }

    fn name(&self) -> String {
        "swap".to_string()
    }

    fn description(&self) -> String {
        "Execute a token swap on Solana".to_string()
    }
}
```

## Creating a Rig Agent with Swap Capabilities

Now let's create an agent that can use our swap tool:

```rust:src/main.rs
use rig::{providers::anthropic, Agent};
use ember_sdk::EmberClient;

#[tokio::main]
async fn main() -> Result<(), Box<dyn std::error::Error>> {
    // Initialize Ember client
    let ember_client = EmberClient::new("YOUR_EMBER_API_KEY");
    
    // Initialize Anthropic client (using Claude)
    let anthropic = anthropic::Client::from_env();
    
    // Create the swap tool
    let swap_tool = SwapTool::new(ember_client);

    // Create an agent with the swap tool
    let agent = anthropic.agent(anthropic::CLAUDE_3_SONNET)
        .preamble("You are a helpful trading assistant that can execute token swaps on Solana.")
        .tool(swap_tool)
        .build();

    // Example interaction
    let response = agent.prompt(
        "I want to swap 1 SOL for USDC on Solana. Can you help me with that?"
    ).await?;

    println!("Agent response: {}", response);

    Ok(())
}
```

## Handling Transaction Signing and Submission

Here's how to handle the transaction signing and submission process:

```rust:src/tools/swap_tool.rs
// Add this to the SwapTool implementation
async fn execute_signed_transaction(
    &self,
    signed_tx: String
) -> Result<String, ToolError> {
    let submit_params = SubmitTransactionParams {
        chain_id: 101, // Solana
        signed_transaction: signed_tx,
        transaction_type: TransactionType::Swap,
    };

    let response = self.client.submit_transaction(submit_params).await
        .map_err(|e| ToolError::Custom(format!("Failed to submit transaction: {}", e)))?;

    Ok(format!("Transaction submitted! Hash: {}", response.transaction_hash))
}
```

## Best Practices

1. **Error Handling**: Always implement proper error handling and recovery mechanisms:
```rust
match client.create_swap(swap_params).await {
    Ok(plan) => {
        // Process the swap plan
    },
    Err(EmberError::InsufficientLiquidity(msg)) => {
        return Err(ToolError::Custom(format!("Not enough liquidity: {}", msg)));
    },
    Err(EmberError::SlippageTooHigh(msg)) => {
        return Err(ToolError::Custom(format!("Slippage too high: {}", msg)));
    },
    Err(e) => {
        return Err(ToolError::Custom(format!("Unexpected error: {}", e)));
    }
}
```

2. **Transaction Monitoring**: Monitor transaction status using WebSockets:
```rust
use ember_sdk::{EmberWebSocket, StreamTransactionsParams};

async fn monitor_transaction(ws: &EmberWebSocket, tx_hash: String) {
    let params = StreamTransactionsParams {
        transaction_hashes: vec![tx_hash],
    };

    ws.on_transaction_update(|update| {
        match update.status {
            TransactionStatus::Confirmed => {
                println!("Transaction confirmed!");
            },
            TransactionStatus::Failed => {
                println!("Transaction failed: {:?}", update.details.error);
            },
            _ => println!("Transaction status: {:?}", update.status),
        }
    }).await;
}
```

3. **Token Validation**: Always validate token addresses before creating transactions:
```rust
async fn validate_token(&self, token: &str) -> Result<(), ToolError> {
    let params = GetTokenDataParams {
        chain_id: 101,
        address: token.to_string(),
    };

    self.client.get_token_data(params).await
        .map_err(|e| ToolError::Custom(format!("Invalid token: {}", e)))?;
    
    Ok(())
}
```

## Conclusion

This guide demonstrated how to:
- Create a Rig tool for Ember SDK swap functionality
- Set up a Rig agent with swap capabilities
- Handle transaction signing and submission
- Implement best practices for error handling and monitoring

Remember to always handle your private keys securely and never expose them in your code. Consider using environment variables or secure key management solutions for production deployments. 