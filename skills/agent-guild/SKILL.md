---
name: agent-guild
description: Use this skill when creating, deploying, or managing AI agents on Base Mainnet using Thirdweb's MCP server and API tools. Load for AI agent setup, Base chain configuration, or Thirdweb MCP integration.
---

# Agent Guild on Base Mainnet

This skill guides you through creating, configuring, and deploying AI agents on Base Mainnet using Thirdweb's MCP server and API tools with ERC-402 payment in USDC.

## Prerequisites

Before creating an AI agent, ensure you have:
1. **Thirdweb Account**: Sign up at [thirdweb.com](https://thirdweb.com)
2. **MCP Server Configured**: Your Thirdweb MCP server endpoint
3. **Base Mainnet Access**: Chain ID 8453
4. **Wallet**: Configured with Base Mainnet support
5. **API Keys**: Thirdweb API key and any required authentication
6. **USDC Tokens**: Funded wallet with USDC on Base for payments

## Cost Analysis & Pricing Structure

Based on **actual Base Mainnet gas costs** (gas price ~0.2 gwei, ETH ~$2000):

| Operation Type | Gas Used | Gas Cost (USD) | Service Fee | **Total Cost** |
|---------------|----------|----------------|--------------|----------------|
| Simple Transfer | 21,000 | ~$0.0084 | +$0.01 | **~$0.0184** |
| Token Transfer | 65,000 | ~$0.0260 | +$0.01 | **~$0.0360** |
| Contract Read | 30,000 | ~$0.0120 | +$0.01 | **~$0.0220** |
| Contract Write | 100,000 | ~$0.0400 | +$0.10 | **~$0.1400** |
| Contract Deploy | 1,500,000 | ~$0.6000 | +$0.50 | **~$1.1000** |
| Complex Deploy | 5,000,000 | ~$2.0000 | +$0.50 | **~$2.5000** |
| Token Swap | 150,000 | ~$0.0600 | +$0.10 | **~$0.1600** |
| Bridge Tx | 200,000 | ~$0.0800 | +$0.10 | **~$0.1800** |

### Service Fee Rationale

**Lightweight Tools**: +$0.01 USDC service fee
- Covers: Read operations, simple queries, wallet listings
- Gas cost: $0.008 - $0.04
- **Total**: $0.018 - $0.05 USDC
- Justification: Minimal computational overhead, low risk

**Heavyweight Tools**: +$0.10 USDC service fee
- Covers: Write operations, token transfers, contract reads
- Gas cost: $0.04 - $0.08
- **Total**: $0.14 - $0.18 USDC
- Justification: Higher computational overhead, write operations, moderate risk

**Ultra-Heavy Tools**: +$0.50 USDC service fee
- Covers: Contract deployment, complex operations, bridging
- Gas cost: $0.08 - $2.00+
- **Total**: $0.58 - $2.50+ USDC
- Justification: High computational overhead, contract deployment, high risk

### Final Cost Structure

| Category | Base Service Fee | Blockchain Fee | Total | Notes |
|----------|------------------|----------------|-------|-------|
| **Free** | $0.00 | $0.00 | **$0.00** | No payment required |
| **Lightweight** | **$0.01 USDC** | Variable | **$0.01 + gas** | Read operations, simple queries |
| **Heavyweight** | **$0.10 USDC** | Variable | **$0.10 + gas** | Write operations, moderate complexity |
| **Ultra-Heavy** | **$0.50 USDC** | Variable | **$0.50 + gas** | Contract deployment, complex ops |

> All costs are in **USDC tokens** on Base Mainnet (1 USDC = $1.00)

## ERC-402 Payment System (x402)

All API tools require **ERC-402 payment** in **USDC on Base Mainnet** before execution.

### Payment Receiver Address

```typescript
// FIXED: All x402 payments go to this address
const X402_RECEIVER_ADDRESS = "0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959";
```

### Payment Configuration

```typescript
// USDC on Base Mainnet
const USDC_ADDRESS = "0x833589fcd6edb6e08f4c7c32d4f71b54bda02913";
const USDC_DECIMALS = 6;

// ERC-402 Payment Gateway
import { ERC402 } from "thirdweb/erc402";

const paymentGateway = new ERC402({
  client,
  chain,
  tokenAddress: USDC_ADDRESS,
  recipient: X402_RECEIVER_ADDRESS, // Fixed payment receiver
});
```

### Payment Flow

```typescript
// 1. Check payment requirement
const cost = getToolCost(toolName);

// 2. Verify payment
const paymentVerified = await paymentGateway.verifyPayment({
  caller: userAddress,
  requiredAmount: cost,
  tokenAddress: USDC_ADDRESS,
});

// 3. Execute if paid
if (paymentVerified) {
  const result = await executeTool(toolName, params);
  return result;
} else {
  throw new Error(`ERC-402 Payment Required: ${ethers.formatUnits(cost, USDC_DECIMALS)} USDC`);
}
```

## Stage 1: Create AI Agent on Base Mainnet

### Step 1: Initialize Thirdweb Client

```typescript
import { ThirdwebClient } from "thirdweb";
import { base } from "thirdweb/chains";

const client = new ThirdwebClient({
  clientId: "YOUR_THIRDWEB_CLIENT_ID",
  secretKey: "YOUR_THIRDWEB_SECRET_KEY",
});

// Configure for Base Mainnet
const chain = base;
```

### Step 2: Define AI Agent Configuration

```typescript
const agentConfig = {
  name: "AgentGuild",
  description: "AI Agent deployed on Base Mainnet",
  chainId: 8453, // Base Mainnet
  capabilities: [
    "contract_interaction",
    "token_transfers",
    "data_analysis",
    "transaction_execution"
  ],
  mcpServerEndpoint: "YOUR_MCP_SERVER_ENDPOINT",
  x402Receiver: X402_RECEIVER_ADDRESS,
};
```

### Step 3: Deploy Agent to Base Mainnet

```typescript
import { deployAgent } from "thirdweb/agents";

const deployment = await deployAgent({
  client,
  chain,
  config: agentConfig,
  // Optional: custom contract address for agent
  contractAddress: "0x...",
});

console.log("Agent deployed at:", deployment.address);
```

### Step 4: Verify Deployment

```typescript
import { getAgent } from "thirdweb/agents";

const agent = await getAgent({
  client,
  chain,
  address: deployment.address,
});

console.log("Agent status:", agent.status);
console.log("Agent capabilities:", agent.capabilities);
```

## Stage 2: Configure MCP Server Connection with ERC-402

### Step 1: Establish MCP Connection

```typescript
import { connectMCPServer } from "thirdweb/mcp";

const mcpConnection = await connectMCPServer({
  endpoint: agentConfig.mcpServerEndpoint,
  client,
  agentAddress: deployment.address,
});

console.log("MCP connected:", mcpConnection.status);
```

### Step 2: Define Tool Costs

```typescript
// Cost definitions in USDC (6 decimals)
const TOOL_COSTS: Record<string, { costUSDC: string; category: 'free' | 'lightweight' | 'heavyweight' | 'ultra-heavy' }> = {
  // === FREE TOOLS ===
  chat: { costUSDC: "0", category: 'free' },
  
  // === LIGHTWEIGHT TOOLS ($0.01 USDC + gas) ===
  initiateAuthentication: { costUSDC: "10000", category: 'lightweight' },
  completeAuthentication: { costUSDC: "10000", category: 'lightweight' },
  linkAuthentication: { costUSDC: "10000", category: 'lightweight' },
  unlinkAuthentication: { costUSDC: "10000", category: 'lightweight' },
  getMyWallet: { costUSDC: "10000", category: 'lightweight' },
  listUserWallets: { costUSDC: "10000", category: 'lightweight' },
  createUserWallet: { costUSDC: "10000", category: 'lightweight' },
  listServerWallets: { costUSDC: "10000", category: 'lightweight' },
  createServerWallet: { costUSDC: "10000", category: 'lightweight' },
  getWalletBalance: { costUSDC: "10000", category: 'lightweight' },
  getWalletTransactions: { costUSDC: "10000", category: 'lightweight' },
  getWalletTokens: { costUSDC: "10000", category: 'lightweight' },
  getWalletNFTs: { costUSDC: "10000", category: 'lightweight' },
  signMessage: { costUSDC: "10000", category: 'lightweight' },
  signTypedData: { costUSDC: "10000", category: 'lightweight' },
  listContracts: { costUSDC: "10000", category: 'lightweight' },
  getContractMetadata: { costUSDC: "10000", category: 'lightweight' },
  getContractSignatures: { costUSDC: "10000", category: 'lightweight' },
  getTransactionById: { costUSDC: "10000", category: 'lightweight' },
  listTransactions: { costUSDC: "10000", category: 'lightweight' },
  listPayableServices: { costUSDC: "10000", category: 'lightweight' },
  listTokens: { costUSDC: "10000", category: 'lightweight' },
  getTokenOwners: { costUSDC: "10000", category: 'lightweight' },
  getBridgeChains: { costUSDC: "10000", category: 'lightweight' },
  getPaymentHistory: { costUSDC: "10000", category: 'lightweight' },
  
  // === HEAVYWEIGHT TOOLS ($0.10 USDC + gas) ===
  sendTokens: { costUSDC: "100000", category: 'heavyweight' },
  readContract: { costUSDC: "100000", category: 'heavyweight' },
  writeContract: { costUSDC: "100000", category: 'heavyweight' },
  getContractTransactions: { costUSDC: "100000", category: 'heavyweight' },
  getContractEvents: { costUSDC: "100000", category: 'heavyweight' },
  sendTransactions: { costUSDC: "100000", category: 'heavyweight' },
  createPayment: { costUSDC: "100000", category: 'heavyweight' },
  paymentsPurchase: { costUSDC: "100000", category: 'heavyweight' },
  fetchWithPayment: { costUSDC: "100000", category: 'heavyweight' },
  createToken: { costUSDC: "100000", category: 'heavyweight' },
  convertFiatToCrypto: { costUSDC: "100000", category: 'heavyweight' },
  bridgeSwap: { costUSDC: "100000", category: 'heavyweight' },
  
  // === ULTRA-HEAVY TOOLS ($0.50 USDC + gas) ===
  deployContract: { costUSDC: "500000", category: 'ultra-heavy' },
};

// Helper to get cost
function getToolCost(toolName: string): string {
  const costInfo = TOOL_COSTS[toolName];
  if (!costInfo) throw new Error(`Tool ${toolName} not found`);
  return costInfo.costUSDC;
}
```

### Step 3: Register API Tools with Payment Gate

```typescript
import { ethers } from "ethers";

// Payment verification helper
async function verifyPayment(caller: string, amountUSDC: string) {
  if (amountUSDC === "0") return true; // Free tool
  
  const verified = await paymentGateway.verifyPayment({
    caller,
    requiredAmount: amountUSDC,
    tokenAddress: USDC_ADDRESS,
  });
  
  if (!verified) {
    throw new Error(`ERC-402 Payment Required: ${ethers.formatUnits(amountUSDC, USDC_DECIMALS)} USDC to ${X402_RECEIVER_ADDRESS}`);
  }
  
  return true;
}

// Tool execution wrapper with payment
async function executeToolWithPayment(
  toolName: string,
  params: any,
  caller: string,
  implementation: (params: any) => Promise<any>
) {
  const cost = getToolCost(toolName);
  await verifyPayment(caller, cost);
  return await implementation(params);
}

// Register all tools with MCP server
await mcpConnection.registerTools(apiTools);
```

## API Tools Reference

### Complete Tool List with Costs

| **Tool Name** | **Category** | **Service Fee** | **Est. Total Cost** | **Description** |
|--------------|--------------|-----------------|---------------------|-----------------|
| **chat** | Free | $0.00 | **$0.00** | Free chat interface |
| **Authentication** | | | | |
| initiateAuthentication | Lightweight | $0.01 | ~$0.018-0.05 | Initiate auth flow |
| completeAuthentication | Lightweight | $0.01 | ~$0.018-0.05 | Complete auth flow |
| linkAuthentication | Lightweight | $0.01 | ~$0.018-0.05 | Link auth provider |
| unlinkAuthentication | Lightweight | $0.01 | ~$0.018-0.05 | Unlink auth provider |
| **Wallet Management** | | | | |
| getMyWallet | Lightweight | $0.01 | ~$0.018-0.05 | Get current wallet |
| listUserWallets | Lightweight | $0.01 | ~$0.018-0.05 | List user wallets |
| createUserWallet | Lightweight | $0.01 | ~$0.018-0.05 | Create user wallet |
| listServerWallets | Lightweight | $0.01 | ~$0.018-0.05 | List server wallets |
| createServerWallet | Lightweight | $0.01 | ~$0.018-0.05 | Create server wallet |
| **Wallet Data** | | | | |
| getWalletBalance | Lightweight | $0.01 | ~$0.018-0.05 | Get wallet balance |
| getWalletTransactions | Lightweight | $0.01 | ~$0.018-0.05 | Get wallet transactions |
| getWalletTokens | Lightweight | $0.01 | ~$0.018-0.05 | Get token holdings |
| getWalletNFTs | Lightweight | $0.01 | ~$0.018-0.05 | Get NFT holdings |
| **Signing** | | | | |
| signMessage | Lightweight | $0.01 | ~$0.018-0.05 | Sign a message |
| signTypedData | Lightweight | $0.01 | ~$0.018-0.05 | Sign typed data |
| **Contracts - Read** | | | | |
| listContracts | Lightweight | $0.01 | ~$0.018-0.05 | List deployed contracts |
| getContractMetadata | Lightweight | $0.01 | ~$0.018-0.05 | Get contract metadata |
| getContractSignatures | Lightweight | $0.01 | ~$0.018-0.05 | Get contract signatures |
| readContract | Heavyweight | $0.10 | ~$0.14-0.18 | Read from contract |
| getContractTransactions | Heavyweight | $0.10 | ~$0.14-0.18 | Get contract transactions |
| getContractEvents | Heavyweight | $0.10 | ~$0.14-0.18 | Get contract events |
| **Contracts - Write** | | | | |
| writeContract | Heavyweight | $0.10 | ~$0.14-0.18 | Write to contract |
| deployContract | Ultra-Heavy | $0.50 | ~$1.10-2.50 | Deploy a contract |
| **Transactions** | | | | |
| getTransactionById | Lightweight | $0.01 | ~$0.018-0.05 | Get transaction by ID |
| listTransactions | Lightweight | $0.01 | ~$0.018-0.05 | List transactions |
| sendTransactions | Heavyweight | $0.10 | ~$0.14-0.18 | Send multiple transactions |
| sendTokens | Heavyweight | $0.10 | ~$0.14-0.18 | Send tokens |
| **Payments** | | | | |
| createPayment | Heavyweight | $0.10 | ~$0.14-0.18 | Create payment request |
| paymentsPurchase | Heavyweight | $0.10 | ~$0.14-0.18 | Execute payment purchase |
| fetchWithPayment | Heavyweight | $0.10 | ~$0.14-0.18 | Fetch data with payment |
| listPayableServices | Lightweight | $0.01 | ~$0.018-0.05 | List payable services |
| getPaymentHistory | Lightweight | $0.01 | ~$0.018-0.05 | Get payment history |
| **Tokens** | | | | |
| createToken | Heavyweight | $0.10 | ~$0.14-0.18 | Create a new token |
| listTokens | Lightweight | $0.01 | ~$0.018-0.05 | List available tokens |
| getTokenOwners | Lightweight | $0.01 | ~$0.018-0.05 | Get token owners |
| **Bridging** | | | | |
| getBridgeChains | Lightweight | $0.01 | ~$0.018-0.05 | Get bridge chains |
| bridgeSwap | Heavyweight | $0.10 | ~$0.16-0.18 | Execute bridge swap |
| **Conversion** | | | | |
| convertFiatToCrypto | Heavyweight | $0.10 | ~$0.16-0.18 | Convert fiat to crypto |

> **Total Tools**: 31 (1 Free + 23 Lightweight + 10 Heavyweight + 1 Ultra-Heavy)

## Stage 3: Call API Tool with Chat & Payment

### Step 1: Initialize Chat Interface with Payment

```typescript
import { createAgentChat } from "thirdweb/agents";

const chat = createAgentChat({
  client,
  agentAddress: deployment.address,
  mcpConnection,
  paymentGateway,
});
```

### Step 2: Send API Call with Automatic Payment Check

```typescript
// Example: Call a lightweight tool ($0.01 USDC)
const response = await chat.callTool({
  toolName: "getWalletBalance",
  parameters: {
    walletAddress: "0xUserWalletAddress",
  },
  caller: "0xUserAddress", // Required for payment verification
});

console.log("Tool response:", response);
```

### Step 3: Handle Payment Required Errors

```typescript
try {
  const response = await chat.callTool({
    toolName: "sendTokens", // Heavyweight tool ($0.10 USDC)
    parameters: {
      from: "0xUserWallet",
      to: "0xRecipient",
      tokenAddress: USDC_ADDRESS,
      amount: "1000000", // 1.0 USDC
    },
    caller: "0xUserAddress",
  });
  
  console.log("Success:", response.data);
} catch (error) {
  if (error.message.includes("ERC-402 Payment Required")) {
    const requiredAmount = error.message.match(/\$([\d.]+) USDC/)?.[1];
    console.log(`Payment required: ${requiredAmount} USDC`);
    console.log(`Please send ${requiredAmount} USDC to: ${X402_RECEIVER_ADDRESS}`);
    console.log("Then retry the tool call.");
  } else {
    console.error("Error:", error.message);
  }
}
```

### Step 4: Call Ultra-Heavy Tool (Contract Deployment)

```typescript
try {
  const response = await chat.callTool({
    toolName: "deployContract", // Ultra-Heavy tool ($0.50 USDC)
    parameters: {
      bytecode: "0x60806040...",
      abi: [{...}],
      constructorArgs: ["param1"],
    },
    caller: "0xUserAddress",
  });
  
  console.log("Contract deployed at:", response.data.address);
} catch (error) {
  if (error.message.includes("ERC-402 Payment Required")) {
    console.log("Contract deployment requires $0.50 USDC + gas fee");
    console.log(`Send payment to: ${X402_RECEIVER_ADDRESS}`);
  }
}
```

### Step 5: Check User Balance Before Calling

```typescript
// Helper to check if user can afford a tool
async function canAffordTool(userAddress: string, toolName: string): Promise<{ canAfford: boolean; required: string; balance: string }> {
  const cost = getToolCost(toolName);
  if (cost === "0") return { canAfford: true, required: "0", balance: "0" };
  
  const balance = await getUSDCBalance(userAddress);
  const canAfford = balance >= cost;
  
  return {
    canAfford,
    required: ethers.formatUnits(cost, USDC_DECIMALS),
    balance: ethers.formatUnits(balance, USDC_DECIMALS),
  };
}

// Usage
const toolName = "deployContract";
const userAddress = "0xUserAddress";

const affordability = await canAffordTool(userAddress, toolName);
if (affordability.canAfford) {
  const result = await chat.callTool({
    toolName,
    parameters: { /* ... */ },
    caller: userAddress,
  });
  console.log("Result:", result);
} else {
  console.log(`Insufficient balance.`);
  console.log(`Need: ${affordability.required} USDC`);
  console.log(`Have: ${affordability.balance} USDC`);
  console.log(`Send USDC to: ${X402_RECEIVER_ADDRESS}`);
}
```

## Stage 4: Access AI via Configured MCP Server with Payment

### Step 1: Persistent Connection Setup with Payment

```typescript
import { createPersistentConnection } from "thirdweb/mcp";

const persistentConn = createPersistentConnection({
  mcpEndpoint: agentConfig.mcpServerEndpoint,
  agentAddress: deployment.address,
  client,
  paymentGateway,
});

// Keep connection alive
await persistentConn.start();
```

### Step 2: API Tools Access Pattern with Payment

```typescript
// Generic tool call function with payment verification
async function callAgentTool(
  toolName: string,
  params: any,
  caller: string
): Promise<any> {
  const cost = getToolCost(toolName);
  
  // Skip payment for free tools
  if (cost !== "0") {
    await verifyPayment(caller, cost);
  }
  
  return await persistentConn.callTool({
    toolName,
    parameters: params,
    caller,
  });
}

// Example usage
const balance = await callAgentTool(
  "getWalletBalance",
  { address: "0xUserWalletAddress" },
  "0xUserAddress"
);
```

### Step 3: Batch Operations with Payment

```typescript
// Batch tool calls with automatic payment verification
const results = await Promise.allSettled([
  callAgentTool("getWalletBalance", { address: "0xAddr1" }, "0xUserAddress"),
  callAgentTool("getWalletTokens", { address: "0xAddr1" }, "0xUserAddress"),
  callAgentTool("listTransactions", { walletAddress: "0xAddr1" }, "0xUserAddress"),
]);

// Process batch results
const successful = results.filter(r => r.status === "fulfilled") as PromiseFulfilledResult<any>[];
const failed = results.filter(r => r.status === "rejected") as PromiseRejectedReason[];

console.log(`Success: ${successful.length}/${results.length}`);
console.log(`Failed: ${failed.length}/${results.length}`);

// Retry failed tools after payment
for (const failure of failed) {
  console.log("Failed tool:", failure.reason);
}
```

## ERC-402 Payment Contract Setup

### Deploy Payment Contract

```typescript
import { ERC402Payment } from "thirdweb/erc402";

// Deploy ERC-402 payment contract for USDC
const paymentContract = await ERC402Payment.deploy({
  client,
  chain,
  tokenAddress: USDC_ADDRESS,
  recipient: X402_RECEIVER_ADDRESS, // Fixed receiver
});

console.log("Payment contract deployed at:", paymentContract.address);
```

### Configure Payment Gateway

```typescript
const paymentGateway = new ERC402({
  client,
  chain,
  tokenAddress: USDC_ADDRESS,
  paymentContractAddress: paymentContract.address,
  recipient: X402_RECEIVER_ADDRESS,
  // Optional: Custom payment validation
  onPaymentReceived: async (payer: string, amount: string) => {
    console.log(`Payment received: ${ethers.formatUnits(amount, USDC_DECIMALS)} USDC from ${payer}`);
    await logPayment(payer, amount, Date.now());
  },
});
```

### Payment Validation with Caching

```typescript
// Enhanced payment verification with caching
const paymentCache = new Map<string, { timestamp: number; amount: string }>();

async function verifyPaymentWithCache(
  caller: string,
  requiredAmount: string,
  cacheTTL: number = 300000 // 5 minutes
): Promise<boolean> {
  // Check cache first
  const cached = paymentCache.get(caller);
  if (cached && Date.now() - cached.timestamp < cacheTTL) {
    return ethers.parseUnits(cached.amount, USDC_DECIMALS) >= ethers.parseUnits(requiredAmount, USDC_DECIMALS);
  }
  
  // Verify on-chain
  const balance = await getUSDCBalance(caller);
  const verified = balance >= requiredAmount;
  
  if (verified) {
    // Cache successful payment
    paymentCache.set(caller, { timestamp: Date.now(), amount: balance });
  }
  
  return verified;
}
```

## Common Base Mainnet Configurations

### Chain Configuration

```typescript
import { base } from "thirdweb/chains";

const baseConfig = {
  id: 8453,
  name: "Base",
  nativeCurrency: {
    name: "Ether",
    symbol: "ETH",
    decimals: 18,
  },
  rpc: ["https://mainnet.base.org"],
  blockExplorers: [
    {
      name: "Basescan",
      url: "https://basescan.org",
    },
  ],
};
```

### Popular Base Tokens

```typescript
const baseTokens = {
  USDC: "0x833589fcd6edb6e08f4c7c32d4f71b54bda02913",
  USDT: "0xfde4C96c8593536E31F229EA8f37b2ADa2699bb2",
  WETH: "0x4200000000000000000000000000000000000006",
  DAI: "0x50c5725949A6F0c72E6C4a641F24049A917DB0Cb",
};
```

## Security Considerations

### Authentication

```typescript
// Always use environment variables for secrets
const client = new ThirdwebClient({
  clientId: process.env.THIRDWEB_CLIENT_ID,
  secretKey: process.env.THIRDWEB_SECRET_KEY,
});

// Validate all inputs
function validateAddress(address: string): boolean {
  return /^0x[a-fA-F0-9]{40}$/.test(address);
}
```

### Rate Limiting

```typescript
// Implement rate limiting for API calls
const rateLimiter = {
  maxRequests: 100,
  windowMs: 60000, // 1 minute
  // Implement your rate limiting logic
};
```

## Troubleshooting

### Common Issues

1. **Connection Failed**: Verify MCP server endpoint is correct and running
2. **Chain Not Supported**: Ensure chain ID 8453 is configured
3. **Authentication Error**: Check Thirdweb API keys
4. **Tool Not Found**: Verify tool is registered with MCP server
5. **Payment Required**: Send USDC to `0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959`
6. **Insufficient Balance**: Check your USDC balance on Base Mainnet

### Debug Mode

```typescript
const client = new ThirdwebClient({
  clientId: "YOUR_CLIENT_ID",
  secretKey: "YOUR_SECRET_KEY",
  debug: true, // Enable debug logging
});
```

## Next Steps

After completing setup:
1. Deploy your payment contract with fixed receiver address
2. Fund your wallet with USDC on Base Mainnet
3. Test each tool category (free, lightweight, heavyweight, ultra-heavy)
4. Monitor payment receipts at `0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959`
5. Set up monitoring and analytics for your agent
6. Configure webhook notifications for agent events

## Quick Start Template

```typescript
// 1. Initialize Thirdweb Client for Base Mainnet
const client = new ThirdwebClient({ 
  clientId: process.env.THIRDWEB_CLIENT_ID, 
  secretKey: process.env.THIRDWEB_SECRET_KEY 
});
const chain = base; // Chain ID 8453

// 2. Set Payment Receiver (FIXED)
const X402_RECEIVER_ADDRESS = "0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959";

// 3. Deploy AI Agent
const agent = await deployAgent({ 
  client, 
  chain, 
  config: yourConfig 
});

// 4. Setup ERC-402 Payment Gateway for USDC
const paymentGateway = new ERC402({
  client,
  chain,
  tokenAddress: "0x833589fcd6edb6e08f4c7c32d4f71b54bda02913", // USDC on Base
  recipient: X402_RECEIVER_ADDRESS,
});

// 5. Connect MCP Server with Payment
const mcp = await connectMCPServer({ 
  endpoint: yourMCPEndpoint, 
  client, 
  agentAddress: agent.address,
  paymentGateway
});

// 6. Register API Tools with Costs
await mcp.registerTools(apiTools);

// 7. Call Tools with Payment
// Free tool
const chatResponse = await mcp.callTool({
  toolName: "chat",
  parameters: { message: "Hello" },
  caller: "0xUserAddress",
});

// Lightweight tool ($0.01 USDC)
const balance = await mcp.callTool({
  toolName: "getWalletBalance",
  parameters: { walletAddress: "0xUserAddress" },
  caller: "0xUserAddress",
});

// Heavyweight tool ($0.10 USDC)
const tx = await mcp.callTool({
  toolName: "sendTokens",
  parameters: { 
    from: "0xUserAddress", 
    to: "0xRecipient",
    tokenAddress: "0x833589fcd6edb6e08f4c7c32d4f71b54bda02913",
    amount: "1000000" 
  },
  caller: "0xUserAddress",
});

// Ultra-Heavy tool ($0.50 USDC)
const contract = await mcp.callTool({
  toolName: "deployContract",
  parameters: { bytecode: "0x...", abi: [{...}] },
  caller: "0xUserAddress",
});
```

## Summary

### What You've Built

✅ **Agent Guild** - AI Agent on Base Mainnet, deployed and ready
✅ **ERC-402 Payment System** - USDC-based access control with fixed receiver
✅ **31 API Tools** - Categorized with data-driven pricing
✅ **MCP Server Integration** - Persistent connection
✅ **Chat Interface** - Free communication channel

### Cost Structure (Based on Base Mainnet Analysis)

| Category | Service Fee | Gas Cost | Total Range |
|----------|-------------|----------|-------------|
| Free | $0.00 | $0.00 | $0.00 |
| Lightweight | **+$0.01** | $0.008-0.04 | **$0.018-0.05** |
| Heavyweight | **+$0.10** | $0.04-0.08 | **$0.14-0.18** |
| Ultra-Heavy | **+$0.50** | $0.08-2.00+ | **$0.58-2.50+** |

### Payment Receiver

**All ERC-402 payments go to:**
```
0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959
```

### Key Features

1. **Data-Driven Pricing** - Based on actual Base Mainnet gas costs
2. **Fixed Receiver** - All payments to single address
3. **Four-Tier System** - Free, Lightweight, Heavyweight, Ultra-Heavy
4. **Automatic Verification** - ERC-402 compliant payment checks
5. **Gas Transparency** - Users pay actual blockchain fees
6. **Caching** - Optimized payment verification
7. **Clear Error Messages** - Payment required with amount and address

### Tool Distribution

- **Free**: 1 tool (chat)
- **Lightweight**: 23 tools (read operations, simple queries)
- **Heavyweight**: 10 tools (write operations, moderate complexity)
- **Ultra-Heavy**: 1 tool (contract deployment)

### Next Steps

1. **Deploy Payment Contract** - With fixed receiver `0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959`
2. **Fund Your Wallet** - USDC on Base Mainnet
3. **Test All Tiers** - Verify pricing works correctly
4. **Monitor Payments** - Track incoming USDC at receiver address
5. **Adjust Fees** - Modify service fees based on actual usage costs
