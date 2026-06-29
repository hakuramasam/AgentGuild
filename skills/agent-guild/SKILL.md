---
name: agent-guild
description: Use this skill when creating, deploying, or managing AI agents on Base Mainnet using Agent Guild's MCP server and API tools. Load for AI agent setup, Base chain configuration, or Agent Guild MCP integration.
---

# Agent Guild on Base Mainnet

This skill guides you through creating, configuring, and deploying AI agents on Base Mainnet using Agent Guild's MCP server and API tools with ERC-402 payment in USDC.

## Prerequisites

Before creating an AI agent, ensure you have:
1. **Thirdweb Account**: Sign up at [thirdweb.com](https://thirdweb.com)
2. **MCP Server Configured**: Your MCP server endpoint
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

import { ERC402 } from "thirdweb/erc402";

const paymentGateway = new ERC402({
  client,
  chain,
  tokenAddress: USDC_ADDRESS,
  recipient: X402_RECEIVER_ADDRESS,
});
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
const chain = base;
```

### Step 2: Define AI Agent Configuration

```typescript
const agentConfig = {
  name: "AgentGuild",
  description: "Agent Guild deployed on Base Mainnet",
  chainId: 8453,
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

### Step 3: Deploy Agent

```typescript
import { deployAgent } from "thirdweb/agents";

const deployment = await deployAgent({
  client,
  chain,
  config: agentConfig,
  contractAddress: "0x...",
});

console.log("Agent deployed at:", deployment.address);
```

## Stage 2: Configure MCP Server Connection

### Step 1: Establish MCP Connection

```typescript
import { connectMCPServer } from "thirdweb/mcp";

const mcpConnection = await connectMCPServer({
  endpoint: agentConfig.mcpServerEndpoint,
  client,
  agentAddress: deployment.address,
});
```

### Step 2: Define Tool Costs

```typescript
const TOOL_COSTS = {
  chat: { costUSDC: "0", category: 'free' },
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
  deployContract: { costUSDC: "500000", category: 'ultra-heavy' },
};
```

### Step 3: Register Tools with Payment

```typescript
import { ethers } from "ethers";

async function verifyPayment(caller, amountUSDC) {
  if (amountUSDC === "0") return true;
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

await mcpConnection.registerTools(TOOL_COSTS);
```

## API Tools Reference

| Tool Name | Category | Service Fee | Est. Total Cost |
|-----------|----------|-------------|------------------|
| chat | Free | $0.00 | $0.00 |
| initiateAuthentication, completeAuthentication, linkAuthentication, unlinkAuthentication, getMyWallet, listUserWallets, createUserWallet, listServerWallets, createServerWallet, getWalletBalance, getWalletTransactions, getWalletTokens, getWalletNFTs, signMessage, signTypedData, listContracts, getContractMetadata, getContractSignatures, getTransactionById, listTransactions, listPayableServices, listTokens, getTokenOwners, getBridgeChains, getPaymentHistory | Lightweight | $0.01 | $0.018-0.05 |
| sendTokens, readContract, writeContract, getContractTransactions, getContractEvents, sendTransactions, createPayment, paymentsPurchase, fetchWithPayment, createToken, convertFiatToCrypto, bridgeSwap | Heavyweight | $0.10 | $0.14-0.18 |
| deployContract | Ultra-Heavy | $0.50 | $1.10-2.50+ |

## Stage 3: Call API Tools with Payment

```typescript
import { createAgentChat } from "thirdweb/agents";
const chat = createAgentChat({ client, agentAddress: deployment.address, mcpConnection, paymentGateway });

// Call with payment verification
const response = await chat.callTool({
  toolName: "getWalletBalance",
  parameters: { walletAddress: "0xUserAddress" },
  caller: "0xUserAddress",
});
```

## Stage 4: Access AI via MCP Server

```typescript
import { createPersistentConnection } from "thirdweb/mcp";
const persistentConn = createPersistentConnection({
  mcpEndpoint: agentConfig.mcpServerEndpoint,
  agentAddress: deployment.address,
  client,
  paymentGateway,
});
await persistentConn.start();

async function callAgentTool(toolName, params, caller) {
  const cost = TOOL_COSTS[toolName].costUSDC;
  if (cost !== "0") await verifyPayment(caller, cost);
  return await persistentConn.callTool({ toolName, parameters: params, caller });
}
```

## Common Base Mainnet Configurations

```typescript
import { base } from "thirdweb/chains";
const baseConfig = { id: 8453, name: "Base", nativeCurrency: { name: "Ether", symbol: "ETH", decimals: 18 }, rpc: ["https://mainnet.base.org"] };

const baseTokens = {
  USDC: "0x833589fcd6edb6e08f4c7c32d4f71b54bda02913",
  USDT: "0xfde4C96c8593536E31F229EA8f37b2ADa2699bb2",
  WETH: "0x4200000000000000000000000000000000000006",
  DAI: "0x50c5725949A6F0c72E6C4a641F24049A917DB0Cb",
};
```

## Summary

**Agent Guild** - AI agents on Base Mainnet with ERC-402 payments

- **Free**: 1 tool (chat)
- **Lightweight**: 23 tools ($0.01 USDC + gas)
- **Heavyweight**: 10 tools ($0.10 USDC + gas)
- **Ultra-Heavy**: 1 tool ($0.50 USDC + gas)

**Payment Receiver**: `0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959`
