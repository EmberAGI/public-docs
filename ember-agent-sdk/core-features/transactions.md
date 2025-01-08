# Transaction Management

Learn how to create, submit, and manage blockchain transactions using the Ember SDK.

## Creating Transactions

Create an unsigned transaction:

```rust
use ember_sdk::{SubmitTransactionParams, TransactionType};

async fn create_transaction(client: &EmberClient) -> Result<(), EmberError> {
    // First create a transaction plan (e.g., swap, transfer, etc.)
    let swap_plan = client.create_swap(swap_params).await?;
    
    // The transaction plan contains the unsigned transaction
    let unsigned_tx = swap_plan.transaction_plan;
    println!("Created unsigned transaction: {:?}", unsigned_tx);
    
    Ok(())
}
```

## Submitting Transactions

Submit a signed transaction:

```rust
async fn submit_transaction(
    client: &EmberClient,
    signed_tx: String
) -> Result<(), EmberError> {
    let params = SubmitTransactionParams {
        chain_id: 1,
        signed_transaction: signed_tx,
        transaction_type: TransactionType::Swap,
    };

    let response = client.submit_transaction(params).await?;
    
    println!("Transaction hash: {}", response.transaction_hash);
    println!("Status: {:?}", response.status);
    println!("Estimated confirmation time: {} seconds", 
        response.estimated_confirmation_time);
    
    Ok(())
}
```

## Transaction Status Monitoring

Monitor transaction status using WebSockets:

```rust
use ember_sdk::{EmberWebSocket, StreamTransactionsParams};

async fn monitor_transaction(
    ws: &EmberWebSocket,
    tx_hash: String
) -> Result<(), EmberError> {
    let params = StreamTransactionsParams {
        transaction_hashes: vec![tx_hash],
    };

    ws.on_transaction_update(|update| {
        println!("Transaction {} status: {:?}", update.transaction_hash, update.status);
        
        match update.status {
            TransactionStatus::Pending => {
                println!("Waiting for confirmation...");
            },
            TransactionStatus::Confirmed => {
                println!("Transaction confirmed!");
                println!("Block number: {}", update.details.block_number);
                println!("Gas used: {}", update.details.gas_used);
            },
            TransactionStatus::Failed => {
                println!("Transaction failed!");
                println!("Error: {:?}", update.details.error);
            },
        }
    }).await;
    
    Ok(())
}
```

## Gas Management

Handle gas estimation and pricing:

```rust
async fn handle_gas(client: &EmberClient) -> Result<(), EmberError> {
    // Get transaction plan
    let plan = client.create_swap(swap_params).await?;
    
    // Check gas estimates
    println!("Estimated gas: {}", plan.fee_breakdown.gas_fee);
    println!("Service fee: {}", plan.fee_breakdown.service_fee);
    println!("Total fee: {}", plan.fee_breakdown.total);
    
    // Check if gas cost is acceptable
    if plan.fee_breakdown.total > max_acceptable_fee {
        println!("Gas cost too high, waiting for better rates");
        return Ok(());
    }
    
    Ok(())
}
```

## Error Recovery

Handle common transaction errors:

```rust
async fn handle_transaction_errors(
    client: &EmberClient,
    params: SubmitTransactionParams
) -> Result<(), EmberError> {
    match client.submit_transaction(params).await {
        Ok(response) => {
            println!("Transaction submitted: {}", response.transaction_hash);
        },
        Err(EmberError::NetworkError(details)) => {
            if details.retryable {
                println!("Network error, retrying in {} ms", 
                    details.suggested_wait_time.unwrap_or(1000));
                // Implement retry logic
            }
        },
        Err(EmberError::ValidationError(errors)) => {
            for error in errors {
                println!("Validation error: {} - {}", error.field, error.message);
            }
        },
        Err(e) => println!("Other error: {:?}", e),
    }
    
    Ok(())
}
```

## Transaction History

Track transaction history:

```rust
use std::collections::HashMap;

struct TransactionTracker {
    pending_transactions: HashMap<String, TransactionDetails>,
}

impl TransactionTracker {
    async fn track_transaction(
        &mut self,
        tx_hash: String,
        details: TransactionDetails
    ) {
        self.pending_transactions.insert(tx_hash.clone(), details);
        
        // Set up monitoring
        let ws = setup_websocket().await?;
        let params = StreamTransactionsParams {
            transaction_hashes: vec![tx_hash.clone()],
        };
        
        ws.on_transaction_update(move |update| {
            if update.status == TransactionStatus::Confirmed {
                self.pending_transactions.remove(&tx_hash);
                println!("Transaction {} confirmed", tx_hash);
            }
        }).await;
    }
}
```

## Best Practices

1. Always estimate gas costs before submitting transactions
2. Implement proper error handling and recovery
3. Monitor transaction status for confirmation
4. Keep track of pending transactions
5. Consider implementing automatic retry logic
6. Set reasonable gas price limits
7. Handle nonce management for multiple transactions
8. Clean up transaction tracking resources 