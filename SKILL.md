---
name: gstable-ai-payment
description: "GStable AI Payment Protocol - 使 AI Agent 能够代表用户发现、协商并执行加密货币支付"
metadata: {"openclaw":{"emoji":"💰","homepage":"https://docs.gstable.io/zh-Hans/docs/category/ai-payment-protocol","primaryEnv":"WALLET_PRIVATE_KEY","requires":{"bins":["gstable-ai-payment"],"env":["WALLET_PRIVATE_KEY"]},"install":[{"id":"uv-install","kind":"shell","command":"uv sync","bins":["gstable-ai-payment"],"label":"Install gstable-ai-payment CLI (uv)"}]},"clawdbot":{"emoji":"💰","homepage":"https://docs.gstable.io/zh-Hans/docs/category/ai-payment-protocol","primaryEnv":"WALLET_PRIVATE_KEY","requires":{"bins":["gstable-ai-payment"],"env":["WALLET_PRIVATE_KEY"]},"install":[{"id":"uv-install","kind":"shell","command":"uv sync","bins":["gstable-ai-payment"],"label":"Install gstable-ai-payment CLI (uv)"}]}}
---

# GStable AI Payment Skill

使 AI Agent 能够代表用户发现、协商并执行加密货币支付的 OpenClaw Skill。

## Features

- 🔗 获取支付链接详情和支持的代币
- 📝 创建支付会话（EIP-712 签名）
- 🔍 查询支付会话状态
- 💳 准备支付并生成区块链交易 calldata
- ✅ 检查并自动授权 Token (approve)
- ⚡ 执行链上支付交易
- 🚀 一键支付（pay 命令，自动处理授权）
- 🔐 安全的 EIP-712 签名（私钥存储在环境变量中）
- ⛓️ 支持多链（Polygon、Ethereum、Arbitrum、Base）

## Installation

```bash
clawhub install gstable-ai-payment
cd ~/.openclaw/workspace/skills/gstable-ai-payment
uv sync
```

## Configuration

设置环境变量：

```bash
# 必需：用于签名 EIP-712 消息的钱包私钥
export WALLET_PRIVATE_KEY=0x...your_private_key_here...

# 可选：GStable API 基础 URL（默认: https://aipay.gstable.io/api/v1）
export GSTABLE_API_BASE_URL=https://aipay.gstable.io/api/v1

# 可选：默认支付者邮箱
export DEFAULT_PAYER_EMAIL=user@example.com
```

⚠️ **安全提示**: 永远不要将私钥提交到版本控制系统！

## Quick Start

### 1. 获取支付链接详情

```bash
# 支付链接格式:
# - https://pay.gstable.io/link/<link_id>
# - https://aipay.gstable.io/api/v1/payment/link/<link_id>

# 示例 1
uv run scripts/gstable-ai-payment.py get_link lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua

# 示例 2
uv run scripts/gstable-ai-payment.py get_link lnk_QTAfGfyqAZHGSm9NKLhtjNYu8dNHRpGh
```

### 2. 创建支付会话

```bash
uv run scripts/gstable-ai-payment.py create_session lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC
```

### 3. 一键支付（推荐）

```bash
uv run scripts/gstable-ai-payment.py pay lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC user@example.com
```

## All Commands

```bash
# 获取支付链接详情
uv run scripts/gstable-ai-payment.py get_link <link_id>

# 创建支付会话
uv run scripts/gstable-ai-payment.py create_session <link_id> <chain_id> <token> [payer]
uv run scripts/gstable-ai-payment.py create_session lnk_xxx 137 USDC
uv run scripts/gstable-ai-payment.py create_session lnk_xxx 137 0x3c499c542cef5e3811e1192ce70d8cc03d5c3359

# 获取会话状态
uv run scripts/gstable-ai-payment.py get_session <session_id>

# 准备支付（生成 calldata）
uv run scripts/gstable-ai-payment.py prepare <session_id> <chain_id> <token_address> [email]

# 执行链上交易
uv run scripts/gstable-ai-payment.py execute <chain_id> <to_address> <calldata>

# 检查 Token 授权额度
uv run scripts/gstable-ai-payment.py allowance <chain_id> <token_address> <spender>

# 授权 Token 给支付合约
uv run scripts/gstable-ai-payment.py approve <chain_id> <token_address> <spender> [amount]

# 一键支付（完整流程，自动处理授权）
uv run scripts/gstable-ai-payment.py pay <link_id> <chain_id> <token> [email]

# 查看钱包地址
uv run scripts/gstable-ai-payment.py wallet
```

## Supported Chains

| Chain | Chain ID | 代币 |
|-------|----------|------|
| Polygon | 137 | USDC, USDT |
| Ethereum | 1 | USDC, USDT |
| Arbitrum | 42161 | USDC |
| Base | 8453 | USDC |

使用 `uv run scripts/gstable-ai-payment.py get_link <link-id>` 查看具体支付链接支持的链和代币。

## Usage Examples

### 完整支付流程

```bash
# 支付链接: https://pay.gstable.io/link/lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua

# 方式一：一键支付（推荐）
uv run scripts/gstable-ai-payment.py pay lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC user@example.com

# 输出:
# Step 1/5: 获取支付链接详情...
# Step 2/5: 创建支付会话...
# Step 3/5: 准备支付...
# Step 4/5: 检查 Token 授权...
# Step 5/5: 执行链上支付交易...
# ✅ 支付完成！
# { "linkId": "lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua", "sessionId": "sess_xxx", "txHash": "0x..." }

# 方式二：分步执行
# 1. 查看支付链接详情
uv run scripts/gstable-ai-payment.py get_link lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua
# 输出 JSON 格式的支付链接详情

# 2. 创建支付会话
uv run scripts/gstable-ai-payment.py create_session lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC
# 输出: { "sessionId": "sess_abc123", ... }

# 3. 准备支付
uv run scripts/gstable-ai-payment.py prepare sess_abc123 137 0x3c499c542cef5e3811e1192ce70d8cc03d5c3359 user@example.com
# 输出: { "executionChainId": "137", "executorContract": "0x...", "calldata": "0x..." }

# 4. 检查并执行授权（如果需要）
uv run scripts/gstable-ai-payment.py allowance 137 0x3c499c542cef5e3811e1192ce70d8cc03d5c3359 0x...
uv run scripts/gstable-ai-payment.py approve 137 0x3c499c542cef5e3811e1192ce70d8cc03d5c3359 0x...

# 5. 执行链上交易
uv run scripts/gstable-ai-payment.py execute 137 0x... 0x...
# 输出: { "status": "submitted", "txHash": "0x..." }
```

### Agent 使用示例

```
User: "我想支付这个：https://pay.gstable.io/link/lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua"

Agent: [uv run scripts/gstable-ai-payment.py get_link lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua]
       "这是一个支付链接。您可以使用 Polygon 上的 USDC 支付。请问您想用哪个网络？"

User: "用 Polygon"

Agent: [uv run scripts/gstable-ai-payment.py pay lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC]
       "✅ 支付完成！交易哈希：0x..."
```

## Payment Flow

```
┌─────────────────┐
│  用户分享链接   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    get_link     │ ──► 获取商品和支付选项
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│  用户选择代币   │
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│ create_session  │ ──► 创建会话 (EIP-712 签名)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    prepare      │ ──► 获取交易 calldata (EIP-712 签名)
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   allowance     │ ──► 检查 Token 授权额度
└────────┬────────┘
         │
         ▼ (如果授权不足)
┌─────────────────┐
│    approve      │ ──► 授权 Token 给支付合约
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│    execute      │ ──► 发送链上交易
└────────┬────────┘
         │
         ▼
┌─────────────────┐
│   ✅ 支付完成   │
└─────────────────┘

或使用 pay 命令一键完成全部流程
```

## Environment Variables
.py get_session <session_id>

# 准备支付（生成 calldata）
uv run scripts/gstable-ai-payment.py prepare <session_id> <chain_id> <token_address> [email]

# 执行链上交易
uv run scripts/gstable-ai-payment.py execute <chain_id> <to_address> <calldata>

# 检查 Token 授权额度
uv run scripts/gstable-ai-payment.py allowance <chain_id> <token_address> <spender>

# 授权 Token 给支付合约
uv run scripts/gstable-ai-payment.py approve <chain_id> <token_address> <spender> [amount]

# 一键支付（完整流程，自动处理授权）
uv run scripts/gstable-ai-payment.py pay <link_id> <chain_id> <token> [email]

# 查看钱包地址
uv run scripts/gstable-ai-payment.py wallet
```

## Supported Chains

| Chain | Chain ID | 代币 |
|-------|----------|------|
| Polygon | 137 | USDC, USDT |
| Ethereum | 1 | USDC, USDT |
| Arbitrum | 42161 | USDC |
| Base | 8453 | USDC |

使用 `uv run scripts/gstable-ai-payment.py get_link <link-id>` 查看具体支付链接支持的链和代币。

## Usage Examples

### 完整支付流程

```bash
# 支付链接: https://pay.gstable.io/link/lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua

# 方式一：一键支付（推荐）
uv run scripts/gstable-ai-payment.py pay lnk_BUDBgiGTWejFs8v0FbdpR3iJ83CG1tua 137 USDC user@example.com

# 输出:
# Step 1/5: 获取支付链接详情...
# Step 2/5: 创建支付会话...
# Step 3/5: 准备支付...
# Step 4/5: 检查 Token 授权...
| 变量 | 必需 | 描述 |
|------|------|------|
| `WALLET_PRIVATE_KEY` | ✅ | 用于签名 EIP-712 消息和执行交易的钱包私钥（0x 开头） |
| `GSTABLE_API_BASE_URL` | ❌ | GStable API 基础 URL（默认: https://aipay.gstable.io/api/v1） |
| `DEFAULT_PAYER_EMAIL` | ❌ | 默认支付者邮箱 |
| `RPC_URL_137` | ❌ | Polygon RPC URL（默认: https://polygon-rpc.com） |
| `RPC_URL_1` | ❌ | Ethereum RPC URL（默认: https://eth.llamarpc.com） |
| `RPC_URL_42161` | ❌ | Arbitrum RPC URL（默认: https://arb1.arbitrum.io/rpc） |
| `RPC_URL_8453` | ❌ | Base RPC URL（默认: https://mainnet.base.org） |

## Troubleshooting

**"WALLET_PRIVATE_KEY not set"**
```bash
export WALLET_PRIVATE_KEY=0x...
```

**"Token not supported"**
```bash
# 先查看支持的代币
uv run scripts/gstable-ai-payment.py get_link <link_id>
```

**"Session expired"**
```bash
# 重新创建会话
uv run scripts/gstable-ai-payment.py create_session <link_id> <chain_id> <token>
```

**"No RPC URL configured for chain"**
```bash
# 设置对应链的 RPC URL
export RPC_URL_137=https://polygon-rpc.com
```

**"Gas estimation failed" 或 "Transaction failed"**
- 确保钱包有足够的原生代币（如 MATIC）支付 gas 费
- 确保钱包有足够的 token 余额完成支付
- 检查 token 是否已授权给支付合约

## Resources

- GStable AI Payment Protocol: https://docs.gstable.io/zh-Hans/docs/category/ai-payment-protocol
- GStable AI Agent Integration: https://docs.gstable.io/zh-Hans/docs/category/ai-agent-integration
- GitHub: https://github.com/gstable/gstable-ai-payment-skill

## License

MIT
