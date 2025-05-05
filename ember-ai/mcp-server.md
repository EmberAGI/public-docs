# MCP Server

**DeFi Capabilities**

Ember's MCP server is compatible with Claude Desktop, Cursor, and other MCP clients. For information on implementation details, refer to the [MCP tools directory](https://github.com/EmberAGI/arbitrum-vibekit/tree/main/typescript/lib/mcp-tools/emberai-mcp).

Ember offers the following capabilities:

#### 1. Token Operations

* **Swap Tokens**: Exchange one token for another using supported DEXs.
* **Get Token Information**: Retrieve details about supported tokens.

#### 2. Lending Protocol Operations

* **Supply Tokens**: Deposit tokens into lending protocols.
* **Withdraw Tokens**: Withdraw previously supplied tokens.
* **Borrow Tokens**: Borrow tokens from lending protocols.
* **Repay Tokens**: Repay borrowed tokens.

#### 3. Position Management

* **Get User Positions**: Retrieve all open positions or balances a user holds within supported protocols.

#### 4. Protocol Information

* **Get Capabilities**: Fetch metadata about what the Ember can do, filtered by capability type.
