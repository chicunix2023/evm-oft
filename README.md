# LayerZero OFT 跨链桥部署指南

## 概述

本指南将帮助你完成 LayerZero OFT (Omnichain Fungible Token) 跨链桥的部署和配置。项目配置了 Ethereum 主网和 BSC 主网之间的跨链功能。

## 当前配置

- **Ethereum 主网**: OFTAdapter (适配现有代币)
- **BSC 主网**: OFT (新的跨链代币)

## 前置要求

1. **环境配置**
   - 确保 `.env` 文件已正确配置
   - 私钥或助记词已设置
   - RPC URL 已配置

2. **资金准备**
   - Ethereum 主网 ETH (用于部署和交易费用)
   - BSC 主网 BNB (用于部署和交易费用)

3. **代币准备**
   - 确保在 Ethereum 上有足够的代币用于测试

## 部署步骤

### 1. 编译合约

```bash
npx hardhat compile
```

### 2. 部署合约

#### 2.1 部署 OFTAdapter 到 Ethereum 主网

```bash
npx hardhat lz:deploy --tags MyOFTAdapter --networks ethereum
```

这将：

- 部署 `MyOFTAdapter` 合约到 Ethereum
- 自动连接到预配置的代币地址
- 返回部署的合约地址

#### 2.2 部署 OFT 到 BSC 主网

```bash
npx hardhat lz:deploy --tags MyOFT --networks bsc
```

这将：

- 部署 `MyOFT` 合约到 BSC 主网
- 创建新的 OFT 代币
- 返回部署的合约地址

### 3. 配置跨链连接 (Wiring)

```bash
npx hardhat lz:oapp:wire --oapp-config layerzero.config.ts
```

这一步会自动配置：

- 设置对等合约地址 (`setPeer`)
- 配置发送和接收库
- 设置 DVN (Data Verification Network) 验证节点
- 配置执行选项和确认数

**注意**: 这一步会提示你确认多个交易，请逐一确认。

### 4. 验证配置

```bash
npx hardhat lz:oapp:config:get --oapp-config layerzero.config.ts
```

这将显示当前的配置状态，包括：

- 自定义配置
- 默认配置
- 活跃配置

## 测试跨链功能

### 从 Ethereum 发送到 BSC 主网

```bash
npx hardhat lz:oft:send \
  --src-eid 30101 \
  --dst-eid 30102 \
  --amount 1 \
  --to <接收地址> \
  --network ethereum
```

### 从 BSC 主网发送到 Ethereum

```bash
npx hardhat lz:oft:send \
  --src-eid 30102 \
  --dst-eid 30101 \
  --amount 1 \
  --to <接收地址> \
  --network bsc
```

## EndpointId 参考

- **Ethereum 主网**: `30101`
- **BSC 主网**: `30102`

## 部署结果检查

部署完成后，可以在以下目录查看合约地址和 ABI：

```text
./deployments/ethereum/MyOFTAdapter.json
./deployments/bsc/MyOFT.json
```

## 常见问题

### 1. 部署失败

- 检查账户余额是否足够支付 Gas 费用
- 确认 RPC URL 是否正确
- 验证私钥或助记词配置

### 2. Wiring 失败

- 确保两个网络的合约都已成功部署
- 检查 `layerzero.config.ts` 配置是否正确
- 确认有足够的 Gas 费用执行多个交易

### 3. 跨链转账失败

- 确保 wiring 步骤已完成
- 检查发送方账户是否有足够的代币余额
- 验证接收地址格式是否正确

## 监控和验证

成功发送跨链交易后，你可以：

1. **查看 LayerZero Scan**: 交易成功后会返回 LayerZero Scan 链接
2. **检查目标链**: 确认代币已到达目标地址
3. **验证余额**: 在两个链上检查代币余额变化

## 生产环境注意事项

在部署到主网前，请确保：

1. **安全检查**
   - 不要使用测试用的 Mock 合约
   - 确认 DVN 配置符合安全要求
   - 设置合适的确认数
   - 进行充分的测试网测试

2. **Gas 优化**
   - 对 `lzReceive` 和 `lzCompose` 进行 Gas 分析
   - 调整执行选项中的 Gas 限制
   - 考虑主网 Gas 费用成本

3. **多签配置**
   - 使用多签钱包作为合约所有者
   - 配置适当的治理机制
   - 设置时间锁等安全措施

4. **主网特殊注意事项**
   - 确保有足够的主网 ETH 和 BNB 用于部署
   - Gas 费用会显著高于测试网
   - 建议先在测试网完整测试流程
   - 考虑分阶段部署和逐步开放功能

## 相关链接

- [LayerZero 文档](https://docs.layerzero.network/)
- [已部署的端点](https://docs.layerzero.network/v2/deployments/deployed-contracts)
- [故障排除指南](https://docs.layerzero.network/v2/developers/evm/troubleshooting/debugging-messages)
