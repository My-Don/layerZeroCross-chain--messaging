# LayerZero OApp 跨链消息示例

<p align="center">
  <a href="https://layerzero.network">
    <img alt="LayerZero" src="https://docs.layerzero.network/img/LayerZero_Logo_Black.svg" width="360" />
  </a>
</p>

<p align="center">
  <a href="https://docs.layerzero.network/v2">📘 LayerZero 官方文档（v2）</a>
</p>

---

## 📌 项目简介

本示例基于 **LayerZero v2 OApp 标准**，演示如何在 **Arbitrum Sepolia** 与 **Base Sepolia** 之间完成 **跨链消息通信**。

示例内容包括：

* 使用 `create-lz-oapp` 初始化 OApp 项目
* 在多条测试链上部署同一 OApp 合约
* 配置 OApp Wiring（trusted peers / DVN / Executor）
* 执行跨链消息发送并验证结果

---

## 📚 前置知识（Prerequisite Knowledge）

* [What is an OApp (Omnichain Application)?](https://docs.layerzero.network/v2/concepts/applications/oapp-standard)
* [How does LayerZero work?](https://docs.layerzero.network/v2/concepts/protocol/core-concepts)

---

## ✅ 环境要求（Requirements）

* **Node.js** `>= 18.16.0`
* **pnpm**（推荐，也可使用 npm / yarn）
* **Hardhat**
* **forge**（可选，用于测试） `>= 0.2.0`

---

## 🌉 测试网准备（重要）

在部署与测试前，请确保你的地址在以下测试网拥有 **ETH 余额**：

* **Arbitrum Sepolia**
* **Base Sepolia**

### 从 Sepolia Bridge 到目标链

* Sepolia → Arbitrum Sepolia
  [https://portal.arbitrum.io/bridge?destinationChain=arbitrum-sepolia&sanitized=true&sourceChain=sepolia](https://portal.arbitrum.io/bridge?destinationChain=arbitrum-sepolia&sanitized=true&sourceChain=sepolia)

* Sepolia → Base Sepolia
  [https://superbridge.app/base-sepolia](https://superbridge.app/base-sepolia)

---

## 🚀 安装与部署步骤

### 1️⃣ 安装 pnpm

在 **PowerShell（管理员）** 中执行：

```bash
corepack enable
corepack prepare pnpm@latest --activate
```

---

### 2️⃣ 初始化 OApp 项目

```bash
npx create-lz-oapp@latest --example oapp
cd oapp
pnpm install
```

#### approve-builds

执行：

```bash
pnpm approve-builds
```

使用 **空格键** 选中以下依赖后，按 `y` 确认：

```
bufferutil
es5-ext
keccak
secp256k1
unrs-resolver
utf-8-validate
web3-bzz
web3-shh
web3
```

---

### 3️⃣ 修改构建脚本

编辑 `package.json`：

```json
"scripts": {
  "compile": "pnpm run compile:hardhat"
}
```

---

### 4️⃣ 配置私钥

编辑 `oapp/.env.example`：

```env
PRIVATE_KEY=你的私钥
```

保存并重命名为：

```
.env
```

---

### 5️⃣ 编译并部署 OApp

```bash
pnpm compile
npx hardhat lz:deploy
```

或分别部署：

```bash
npx hardhat lz:deploy --network arbitrum-sepolia
npx hardhat lz:deploy --network base-sepolia
```

示例输出：

```text
Deployed contract: MyOApp, network: base-sepolia, address: 0x860bF843e9e10C8F93C37C37c64bD260b3d47487
Deployed contract: MyOApp, network: arbitrum-sepolia, address: 0x860bF843e9e10C8F93C37C37c64bD260b3d47487
✓ Your contracts are now deployed
```

---

## 🔧 OApp Wiring 配置

### 6️⃣ 修改 `layerzero.config.ts`

```ts
const baseContract: OmniPointHardhat = {
  eid: EndpointId.BASESEP_V2_TESTNET,
  contractName: 'MyOApp',
}

const arbitrumContract: OmniPointHardhat = {
  eid: EndpointId.ARBSEP_V2_TESTNET,
  contractName: 'MyOApp',
}
```

> ℹ️ 请确保 `contractName` 与你实际部署的合约名称一致

---

### 7️⃣ 执行 Wiring

```bash
npx hardhat lz:oapp:wire --oapp-config layerzero.config.ts
```

成功示例：

```text
Successfully sent 12 transactions
✓ Your OApp is now configured
```

---

### 8️⃣ 验证 Wiring 状态

```bash
npx hardhat lz:oapp:peers:get --oapp-config layerzero.config.ts
```

示例输出：

```
base-sepolia     → arbitrum-sepolia   ✓
arbitrum-sepolia → base-sepolia       ✓
```

---

## 🔄 跨链消息测试

### 9️⃣ 发送跨链消息

```bash
npx hardhat lz:oapp:send \
  --network arbitrum-sepolia \
  --dst-eid 40245 \
  --string "Hello from Arbitrum Sepolia"
```

示例输出：

```text
✅ SENT_VIA_OAPP: Successfully sent "Hello from Arbitrum Sepolia"
Source TX: https://sepolia.arbiscan.io/tx/0x7e85d7b6...
LayerZero Scan: https://testnet.layerzeroscan.com/tx/0x7e85d7b6...
```

---

## 🎯 最终效果

* ✅ OApp 合约在多链成功部署
* ✅ OApp Wiring 自动配置完成
* ✅ Arbitrum Sepolia ↔ Base Sepolia 跨链通信成功
* ✅ 可作为 LayerZero v2 OApp 最小可运行示例

---

## 📎 参考资料

* LayerZero Docs: [https://docs.layerzero.network/v2](https://docs.layerzero.network/v2)
* OApp Standard: [https://docs.layerzero.network/v2/concepts/applications/oapp-standard](https://docs.layerzero.network/v2/concepts/applications/oapp-standard)

---

## 📄 License

MIT
