# Agent Guild - Production Ready

> **Version**: 1.0.0 | **Status**: Production Ready | **License**: MIT

Agent Guild is a production-ready AI agent skill for Base Mainnet that provides 37 API tools with ERC-402 payment-gated access.

## 🚀 Quick Start

### 1. Load the Skill

```
Load the skill: agent-guild
```

### 2. Deploy Your Agent

Follow the deployment instructions in [SKILL.md](./SKILL.md)

### 3. Configure Payment

All paid tools require ERC-402 payment in USDC:
- **Free**: 1 tool (chat)
- **Lightweight**: 24 tools @ $0.01 USDC + gas
- **Heavyweight**: 10 tools @ $0.10 USDC + gas
- **Ultra-Heavy**: 2 tools @ $0.50 USDC + gas

**Payment Receiver**: `0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959`

## 📦 What's Included

### Core Files

| File | Description |
|------|-------------|
| [SKILL.md](./SKILL.md) | Main skill implementation (33KB) |
| [config.json](./config.json) | Production configuration |
| [package.json](./package.json) | Dependencies and scripts |
| [tsconfig.json](./tsconfig.json) | TypeScript configuration |
| [CHANGELOG.md](./CHANGELOG.md) | Version history |

### Features

✅ **37 API Tools** - Complete implementations for all tools  
✅ **ERC-402 Payment** - USDC-based access control  
✅ **Input Validation** - All parameters validated  
✅ **Rate Limiting** - Redis-based, 100 requests/minute  
✅ **Timeout Handling** - 30s default with retry logic  
✅ **Error Handling** - Comprehensive error management  
✅ **Logging** - Structured Winston logging  
✅ **Metrics** - Prometheus integration ready  
✅ **Testing** - Unit and integration tests  
✅ **Deployment** - Docker, Kubernetes, and server configs  

## 📚 Documentation

- **[SKILL.md](./SKILL.md)** - Complete skill documentation
- **[Cost Analysis](#cost-analysis)** - Pricing structure and rationale
- **[API Tools](#api-tools)** - Full tool reference
- **[Deployment](#deployment)** - Production deployment guide

## 💰 Cost Analysis

Based on actual Base Mainnet gas costs (gas price ~0.2 gwei, ETH ~$2000):

| Operation | Gas Used | Gas Cost | Service Fee | **Total** |
|-----------|----------|----------|-------------|-----------|
| Simple Transfer | 21,000 | ~$0.0084 | +$0.01 | **~$0.0184** |
| Token Transfer | 65,000 | ~$0.0260 | +$0.01 | **~$0.0360** |
| Contract Read | 30,000 | ~$0.0120 | +$0.01 | **~$0.0220** |
| Contract Write | 100,000 | ~$0.0400 | +$0.10 | **~$0.1400** |
| Contract Deploy | 1,500,000 | ~$0.6000 | +$0.50 | **~$1.1000** |

### Final Cost Structure

| Category | Service Fee | Gas Cost | Total Range |
|----------|-------------|----------|-------------|
| **Free** | $0.00 | $0.00 | $0.00 |
| **Lightweight** | $0.01 USDC | Variable | $0.018-0.05 |
| **Heavyweight** | $0.10 USDC | Variable | $0.14-0.18 |
| **Ultra-Heavy** | $0.50 USDC | Variable | $0.58-2.50+ |

## 🛠️ API Tools

### Free Tools (1)
- `chat` - Free chat interface

### Lightweight Tools (24) - $0.01 USDC + gas
**Authentication:**
- `initiateAuthentication`, `completeAuthentication`, `linkAuthentication`, `unlinkAuthentication`

**Wallet Management:**
- `getMyWallet`, `listUserWallets`, `createUserWallet`, `listServerWallets`, `createServerWallet`

**Wallet Data:**
- `getWalletBalance`, `getWalletTransactions`, `getWalletTokens`, `getWalletNFTs`

**Signing:**
- `signMessage`, `signTypedData`

**Contracts:**
- `listContracts`, `getContractMetadata`, `getContractSignatures`

**Transactions:**
- `getTransactionById`, `listTransactions`

**Payments:**
- `listPayableServices`, `getPaymentHistory`

**Tokens:**
- `listTokens`, `getTokenOwners`

**Bridging:**
- `getBridgeChains`

### Heavyweight Tools (10) - $0.10 USDC + gas
- `readContract`, `writeContract`
- `sendTransactions`, `sendTokens`
- `createPayment`, `paymentsPurchase`, `fetchWithPayment`
- `createToken`
- `bridgeSwap`, `convertFiatToCrypto`

### Ultra-Heavy Tools (2) - $0.50 USDC + gas
- `deployContract`

## 🔒 Security Features

### Input Validation
- ✅ Ethereum address validation
- ✅ URL validation
- ✅ Number and amount validation
- ✅ Parameter schema validation

### Rate Limiting
- ✅ 100 requests per minute per user
- ✅ Redis-based for distributed deployments
- ✅ Configurable limits

### Error Handling
- ✅ Comprehensive error messages
- ✅ Payment-required errors with amount
- ✅ Validation errors with details
- ✅ Network error handling

### Timeout Handling
- ✅ 30-second default timeout
- ✅ Configurable per-tool timeouts
- ✅ Automatic retry with backoff

## 🚀 Deployment

### Local Development

```bash
# Clone repository
git clone https://github.com/hakuramasam/AgentGuild.git
cd AgentGuild

# Install dependencies
npm install

# Copy environment file
cp .env.example .env

# Edit configuration
nano .env

# Start Redis
docker run -p 6379:6379 redis

# Start development server
npm run dev
```

### Production Deployment

```bash
# Build
npm run build

# Start
npm start
```

### Docker

```bash
# Build and run
docker-compose up -d
```

### Kubernetes

```bash
# Apply Kubernetes manifests
kubectl apply -f k8s/
```

## 📊 Monitoring

### Metrics
- Tool execution counters
- Payment volume tracking
- Error rate monitoring
- Response time histograms

### Prometheus
```yaml
# prometheus.yml
scrape_configs:
  - job_name: 'agent-guild'
    static_configs:
      - targets: ['agent-guild:9090']
```

### Grafana
Create dashboards for:
- Tool usage statistics
- Payment volumes
- Error rates
- Performance metrics

## 🧪 Testing

### Run Tests

```bash
# Unit tests
npm test

# With coverage
npm run test:coverage

# Watch mode
npm run test:watch
```

### Test Coverage
- ✅ Configuration validation
- ✅ Input validation
- ✅ Payment system
- ✅ Tool registry
- ✅ Tool execution

## 📝 Configuration

### Environment Variables

```bash
# Required
THIRDWEB_CLIENT_ID=your_client_id
THIRDWEB_SECRET_KEY=your_secret_key
X402_RECEIVER_ADDRESS=0x21c2b22b7dD5634Af6fc0bc5489965bb906a9959

# Optional
USDC_ADDRESS=0x833589fcd6edb6e08f4c7c32d4f71b54bda02913
BASE_RPC_URL=https://mainnet.base.org
REDIS_URL=redis://localhost:6379
RATE_LIMIT_REQUESTS=100
RATE_LIMIT_WINDOW_MS=60000
LOG_LEVEL=info
PORT=3000
METRICS_PORT=9090
```

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📜 License

This project is licensed under the MIT License - see [LICENSE](../../LICENSE) for details.

## 📞 Support

- **GitHub**: https://github.com/hakuramasam/AgentGuild
- **Issues**: https://github.com/hakuramasam/AgentGuild/issues
- **Documentation**: https://github.com/hakuramasam/AgentGuild/blob/main/skills/agent-guild/SKILL.md

---

**Agent Guild - Production-Ready AI Agent on Base Mainnet** 🚀
