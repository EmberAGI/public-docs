---
icon: robot
---

# Ember Agent TypeScript SDK

The Ember SDK gives your AI agents on-chain actions. This TypeScript SDK provides a strongly-typed client for interacting with Ember's gRPC API.

> **Beta Release**: This SDK is currently in beta. The API is unstable and may change between versions.

### Features



* Full TypeScript support with generated types
* Promise-based API
* Support for chain and token information
* Token swap functionality
* Comprehensive error handling

### Installation



Install the SDK and its peer dependencies:

```sh
# Using pnpm (recommended)
pnpm add @emberai/sdk-typescript@beta @grpc/grpc-js @grpc/proto-loader

# Using npm
npm install @emberai/sdk-typescript@beta @grpc/grpc-js @grpc/proto-loader

# Using yarn
yarn add @emberai/sdk-typescript@beta @grpc/grpc-js @grpc/proto-loader
```

> Note: During the beta period, you must explicitly use the `@beta` tag when installing. This ensures you're aware that the API may change between versions.

### Basic Usage



```typescript
import EmberClient from '@emberai/sdk-typescript';

// Create a client instance
const client = new EmberClient({
  endpoint: 'grpc.api.emberai.xyz:50051',
  apiKey: 'your-api-key',       // Optional
});

// Get supported chains
const chains = await client.getChains({
  pageSize: 10,
  filter: '',      // Optional filter string
  pageToken: '',   // Optional pagination token
});

// Get tokens for a specific chain
const tokens = await client.getTokens({
  chainId: 'ethereum',
  pageSize: 20,
  filter: '',      // Optional filter string
  pageToken: '',   // Optional pagination token
});

// Example: Perform token swap and track status
const swapResponse = await client.swapToken({
  // ... your swap parameters ...
});
// Note: requestId is received in the response from swapToken method
const requestId = swapResponse.requestId;

// Assume you submit your signed transaction to the blockchain and obtain a transaction hash
// Note: transactionId is the transaction hash received after submitting the signed transaction
const signedTx = { /* signed transaction data */ };
const txResult = await submitSignedTransaction(signedTx); // pseudo-code for submitting transaction
const transactionId = txResult.transactionHash;

// Now, call getProviderTrackingStatus endpoint to track the provider's status
const trackingStatus = await client.getProviderTrackingStatus({
  requestId,
  transactionId,
});
console.log('Provider Tracking Status:', trackingStatus);

// Don't forget to close the client when done
client.close();
```

### Examples



The SDK comes with several example implementations in the [`examples`](https://github.com/EmberAGI/ember-sdk-typescript/blob/main/examples) directory:

* [`token-swap.ts`](https://github.com/EmberAGI/ember-sdk-typescript/blob/main/examples/token-swap.ts) - A basic example demonstrating how to perform token swaps using the SDK with Viem for Ethereum interaction. It shows wallet setup, token swapping (capturing the requestId from swapToken and the transaction hash after submission), and tracking the swap status using getProviderTrackingStatus.
* [`langchain-swap.ts`](https://github.com/EmberAGI/ember-sdk-typescript/blob/main/examples/langchain-swap.ts) - An advanced example showing how to integrate the SDK with LangChain to create an AI agent that can perform token swaps through natural language commands. It demonstrates creating custom tools, setting up an OpenAI-powered agent, handling complex interactions, and tracking swap status by calling getProviderTrackingStatus with the requestId from swapToken and the transaction hash.

### Error Handling



The SDK uses standard gRPC error codes:

* `INVALID_ARGUMENT`: The request parameters are malformed or invalid
* `NOT_FOUND`: The requested resource(s) could not be found
* `INTERNAL`: An unexpected server-side error occurred
* `UNAUTHENTICATED`: Authentication failed
* `PERMISSION_DENIED`: Authorization failed
