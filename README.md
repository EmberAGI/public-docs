# Introduction

Ember is a powerful MCP (Model Context Protocol) server that acts as a one-stop solution for managing and executing complex DeFi operations across multiple chains. By providing simple, well-documented interfaces, Ember empowers AI agents and LLMs to dynamically select, compose, and interact with DeFi protocols without the need for custom implementations.&#x20;

Ember's MCP-powered connectivity establishes a declarative DeFi layer, enabling agents to interpret and execute user intents while offering developers a unified interface to the entire DeFi ecosystem. With access to rich market data, developers can leverage Ember to create custom DeFi strategies and effortlessly automate advanced workflows.



## DeFi Capabilities

<details>

<summary>Core DeFi Operations</summary>

#### 1. Token Swaps & Management

**Swap Tokens:** Exchange one token for another across numerous supported DEXs and chains.

**Get Tokens:** Retrieve a list of supported tokens, filterable by chain ID and other criteria. This contributes to Market Data by providing information on available assets.

#### 2. Lending & Borrowing

**Supply:** Deposit tokens into lending protocols to earn yield or use as collateral.

**Borrow:** Borrow tokens from lending protocols against supplied collateral.

**Repay:** Repay borrowed tokens.

**Withdraw:** Withdraw previously supplied tokens from lending protocols.

#### 3. Liquidity Provision & Management

**Supply Liquidity:** Supply assets to liquidity pools on DEXs (e.g., providing two tokens to a pair).

**Withdraw Liquidity:** Withdraw assets from a liquidity position in a pool.

**Get Liquidity Pools:** Retrieve a list of available liquidity pools. This provides Market Data on available investment opportunities.

**Get User Liquidity Positions:** Fetch a user's current liquidity positions. This is part of Wallet Context and Position Management.

</details>

<details>

<summary>Market Data &#x26; Protocol Information</summary>

**Get Tokens:** Provides data on supported tokens.

**Get Liquidity Pools:** Provides data on available liquidity pools.

**Get Yield Markets:** Fetch information about available yield farming markets and opportunities.&#x20;

**Get Capabilities:** Discover all tools and functionalities offered by the Ember MCP server, filterable by capability type. This helps in understanding the full scope of what the server can do (Protocol Information).

</details>

<details>

<summary>Wallet Context &#x26; Position Management</summary>

**Get User Positions:** Retrieve a comprehensive overview of a user's wallet positions across various DeFi protocols, including balances, supplied assets, borrowed amounts, and staked assets. This is a core Wallet Context capability.

**Get User Liquidity Positions:** Specifically retrieves a user's positions in liquidity pools.

</details>

Ember's MCP server is compatible with Cursor, Claude Desktop and any other MCP client. For more details, refer to Ember's [MCP implementation](https://github.com/EmberAGI/arbitrum-vibekit/tree/main/typescript/lib/mcp-tools/emberai-mcp).
