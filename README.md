## LayerZero官方文档
https://docs.layerzero.network/v2

## Prerequisite Knowledge

- [What is an OApp (Omnichain Application)?](https://docs.layerzero.network/v2/concepts/applications/oapp-standard)
- [How does LayerZero work?](https://docs.layerzero.network/v2/concepts/protocol/core-concepts)

## Requirements

- `Node.js` - `>=18.16.0`
- `pnpm` (recommended) - or another package manager of your choice (npm, yarn)
- `forge` (optional) - `>=0.2.0` for testing, and if not using Hardhat for compilation



## 安装步骤
```sh
使用layerZero
这是将消息从某链传递到某链去的dapp
我们先使用arb sepolia与base sepolia将eth桥接到arb sepolia与base sepolia网络去
通过sepolia桥接到arb sepolia
https://portal.arbitrum.io/bridge?destinationChain=arbitrum-sepolia&sanitized=true&sourceChain=sepolia
通过sepolia桥接到base sepolia
https://superbridge.app/base-sepolia
打开powershell，安装pnpm,命令如下:
# 启用 Corepack（Node.js 16.13+ 自带）
corepack enable
# 安装最新版 pnpm
corepack prepare pnpm@latest --activate


npx create-lz-oapp@latest --example oapp
pnpm install
然后执行pnpm approve-builds
将 bufferutil@4.1.0, es5-ext@0.10.64, keccak@3.0.4, secp256k1@4.0.4, unrs-resolver@1.11.1,
utf-8-validate@5.0.10, web3-bzz@1.10.4, web3-shh@1.10.4, web3@1.10.4
等依赖按空格选中,然后选择y，即可
使用 LayerZero CLI 工具，可以一次性选择多个链进行部署
首先修改package.json,将scripts中的 "compile": "pnpm run compile:forge && pnpm run compile:hardhat",换成这样
修改oapp目录下.env.example文件，
PRIVATE_KEY=你的私钥
保存文件,修改.env.example为.env,并保存
在oapp目录下,右键 → Git Bash Here, 输入命令如下:
pnpm compile
npx hardhat lz:deploy或者npx hardhat lz:deploy --network arbitrum-sepolia && npx hardhat lz:deploy --network base-sepolia
控制台打印数据如下
Network: arbitrum-sepolia
Deployer: 0x5159eA8501d3746bB07c20B5D0406bD12844D7ec
Network: base-sepolia
Deployer: 0x5159eA8501d3746bB07c20B5D0406bD12844D7ec
Deployed contract: MyOApp, network: base-sepolia, address: 0x860bF843e9e10C8F93C37C37c64bD260b3d47487
Deployed contract: MyOApp, network: arbitrum-sepolia, address: 0x860bF843e9e10C8F93C37C37c64bD260b3d47487
info:    ✓ Your contracts are now deployed

下一步骤,配置layerzero.config.ts
打开layerzero.config.ts文件,修改如下内容
const baseContract: OmniPointHardhat = {
    eid: EndpointId.BASESEP_V2_TESTNET,
    contractName: 'MyOApp',
}

const arbitrumContract: OmniPointHardhat = {
    eid: EndpointId.ARBSEP_V2_TESTNET,
    contractName: 'MyOApp',
}

ps: （请将 contractName 和 EID 替换为你实际部署的合约名和 EID）
由于我们部署的合约名是MyOApp,所以contractName是MyOApp,而EID是EndpointId.BASESEP_V2_TESTNET和EndpointId.ARBSEP_V2_TESTNET是已经定义好的,所以这两个EID是我们需要的，我们不需要修改


下一步骤，执行 wiring 命令
运行以下命令，自动为你配置 trusted peers、消息库、DVN/Executor，该命令会根据 config 文件内容，自动完成跨链通信的所有必要配置
直接在git bash终端执行如下命令:
npx hardhat lz:oapp:wire --oapp-config layerzero.config.ts
选择Y即可,终端打印数据如下
info:    Successfully sent 12 transactions
info:    ✓ Your OApp is now configured


下一步骤,验证 wiring 是否成功
npx hardhat lz:oapp:peers:get --oapp-config layerzero.config.ts
终端打印数据如下
$ npx hardhat lz:oapp:peers:get --oapp-config layerzero.config.ts
PRIVATE_KEY: xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

    ╭─────────────────────────────────────────╮
    │       ▓▓▓ LayerZero DevTools ▓▓▓        │
    │  ═══════════════════════════════════    │
    │          /*\                            │
    │         /* *\     BUILD ANYTHING        │
    │         ('v')                           │
    │        //-=-\\    ▶ OMNICHAIN           │
    │        (\_=_/)                          │
    │         ^^ ^^                           │
    │  ▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓▓  │
    ╰─────────────────────────────────────────╯

┌──────────────────┬──────────────┬──────────────────┐
│ from → to        │ base-sepolia │ arbitrum-sepolia │
├──────────────────┼──────────────┼──────────────────┤
│ base-sepolia     │      ∅       │        ✓         │
├──────────────────┼──────────────┼──────────────────┤
│ arbitrum-sepolia │      ✓       │        ∅         │
└──────────────────┴──────────────┴──────────────────┘

 ✓ - Connected
 ⤫ - Not Connected
 ∅ - Ignored

下一步步骤,验证跨链通信是否成功
npx hardhat lz:oapp:send --network arbitrum-sepolia --dst-eid 40245 --string "Hello from Arbitrum Sepolia"
终端打印数据如下
info:    Initiating string send from arbitrum-sepolia to basesep-testnet
info:    String to send: "Hello from Arbitrum Sepolia"
info:    Destination EID: 40245
info:    Using signer: 0x5159eA8501d3746bB07c20B5D0406bD12844D7ec
info:    MyOApp contract found at: 0x860bF843e9e10C8F93C37C37c64bD260b3d47487
info:    Execution options: 0x
info:    Quoting gas cost for the send transaction...
info:      Native fee: 0.000009454810865075 ETH
info:      LZ token fee: 0 LZ
info:    Sending the string transaction...
info:      Transaction hash: 0x7e85d7b647224d9257314adf047c4986bacd95dd54b50e3afa576ada6c9fdc84
info:    Waiting for transaction confirmation...
info:      Gas used: 225604
info:      Block number: 233379354
```
info:    ✅ SENT_VIA_OAPP: Successfully sent "Hello from Arbitrum Sepolia" from arbitrum-sepolia to basesep-testnet
info:    ✅ TX_HASH: Block explorer link for source chain arbitrum-sepolia: https://sepolia.arbiscan.io/tx/0x7e85d7b647224d9257314adf047c4986bacd95dd54b50e3afa576ada6c9fdc84
info:    ✅ EXPLORER_LINK: LayerZero Scan link for tracking cross-chain delivery: https://testnet.layerzeroscan.com/tx/0x7e85d7b647224d9257314adf047c4986bacd95dd54b50e3afa576ada6c9fdc84

