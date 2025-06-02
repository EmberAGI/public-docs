# Introduction

Ember is a powerful MCP (Model Context Protocol) server that acts as a one-stop solution for managing and executing complex DeFi operations across multiple chains. By providing simple, well-documented interfaces, Ember empowers AI agents to dynamically select, compose, and interact with DeFi protocols without the need for custom implementations.

Ember is a declarative DeFi layer that enables AI agents to interpret and execute user intents while offering developers a unified interface to the entire DeFi ecosystem. With access to rich market data, developers can leverage Ember to create custom DeFi strategies and effortlessly automate advanced workflows.



## DeFi Capabilities

<details>

<summary>Core DeFi Operations</summary>

#### 1. Token Swaps & Management

**Swap Tokens:** Exchange one token for another across numerous supported DEXs and chains.

**Get Tokens:** Retrieve a list of supported tokens, filterable by chain ID and other criteria.

#### 2. Lending & Borrowing

**Supply:** Deposit tokens into lending protocols to earn yield or use as collateral.

**Borrow:** Borrow tokens from lending protocols against supplied collateral.

**Repay:** Repay borrowed tokens.

**Withdraw:** Withdraw previously supplied tokens from lending protocols.

#### 3. Liquidity Provision & Management

**Supply Liquidity:** Supply assets to liquidity pools on DEXs.

**Withdraw Liquidity:** Withdraw assets from a liquidity position in a pool.

**Get Liquidity Pools:** Retrieve a list of available liquidity pools. This provides market data on available investment opportunities.

**Get User Liquidity Positions:** Fetch a user's current liquidity positions.&#x20;

</details>

<details>

<summary>Market Data &#x26; Protocol Information</summary>

**Get Tokens:** Provides data on supported tokens.

**Get Liquidity Pools:** Provides data on available liquidity pools.

**Get Yield Markets:** Fetch information about available yield farming markets and opportunities.&#x20;

**Get Capabilities:** Discover all tools and functionalities offered by the Ember MCP server, filterable by capability type. This helps in understanding the full scope of what the server can do.

</details>

<details>

<summary>Wallet Context &#x26; Position Management</summary>

**Get User Positions:** Retrieve a comprehensive overview of a user's wallet positions across various DeFi protocols, including balances, supplied assets, borrowed amounts, and staked assets.

**Get User Liquidity Positions:** Specifically retrieves a user's positions in liquidity pools.

</details>

Ember's MCP server is compatible with Cursor, Claude Desktop and any other MCP client. For more details, refer to Ember's [MCP implementation](https://github.com/EmberAGI/arbitrum-vibekit/tree/main/typescript/lib/mcp-tools/emberai-mcp).
