# What is Ember?

## Overview

* Ember is a powerful MCP (Model Context Protocol) server that enables AI agents to interact with DeFi protocols or on-chain services without requiring custom implementations.&#x20;
* Ember features simple, well-documented interfaces that empower LLMs to dynamically select and compose tools.&#x20;
* Ember's MCP-powered connectivity establishes a declarative DeFi layer, which not only enables AI agents to interpret and execute user intents but also offers developers a unified interface to the entire DeFi ecosystem.&#x20;
* Ember serves as a one-stop solution for managing and executing complex DeFi operations across various chains. Developers can use Ember's rich market data to create custom DeFi strategies.



## DeFi Capabilities

<details>

<summary>Core Defi Operations</summary>

#### 1. Token Swaps & Management

**swapTokens:** Exchange one token for another across numerous supported DEXs and chains.

**getTokens:** Retrieve a list of supported tokens, filterable by chain ID and other criteria. This contributes to Market Data by providing information on available assets.

#### 2. Lending & Borrowing

**supply:** Deposit tokens into lending protocols to earn yield or use as collateral.

**borrow:** Borrow tokens from lending protocols against supplied collateral.

**repay:** Repay borrowed tokens.

**withdraw:** Withdraw previously supplied tokens from lending protocols.

#### 3. Liquidity Provision & Management

**supplyLiquidity:** Supply assets to liquidity pools on DEXs (e.g., providing two tokens to a pair).

**withdrawLiquidity:** Withdraw assets from a liquidity position in a pool.

**getLiquidityPools:** Retrieve a list of available liquidity pools. This provides Market Data on available investment opportunities.

**getUserLiquidityPositions:** Fetch a user's current liquidity positions. This is part of Wallet Context and Position Management.

</details>

<details>

<summary>Market Data &#x26; Protocol Information</summary>

**getTokens:** Provides data on supported tokens.

**getLiquidityPools:** Provides data on available liquidity pools.

**getYieldMarkets:** Fetch information about available yield farming markets and opportunities.&#x20;

**getCapabilities:** Discover all tools and functionalities offered by the Ember MCP server, filterable by capability type. This helps in understanding the full scope of what the server can do (Protocol Information).

</details>

<details>

<summary>Wallet Context &#x26; Position Management</summary>

**getUserPositions:** Retrieve a comprehensive overview of a user's wallet positions across various DeFi protocols, including balances, supplied assets, borrowed amounts, and staked assets. This is a core Wallet Context capability.

**getUserLiquidityPositions:** Specifically retrieves a user's positions in liquidity pools.

</details>

Ember's MCP server is compatible with Cursor, Claude Desktop and any other MCP client. For more details, refer to Ember's [MCP implementation](https://github.com/EmberAGI/arbitrum-vibekit/tree/main/typescript/lib/mcp-tools/emberai-mcp).
