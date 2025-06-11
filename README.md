 autoRBF.js that handles both Ethereum and Bitcoin Replace-By-Fee in one script, plus a 🔥 README.md. Enjoy your Early Bird Treat from The ExDeX! 🦅

> **Double your delight**—rescue stuck Ethereum **and** Bitcoin transactions from a single script!  
> Fortune favors the bold… and the well-fee’d. 🦅

---

## 🌅 Prerequisites

1. **Node.js & NPM**  
2. A **.env** file in project root with:
   ```dotenv
   # Ethereum (Infura or any JSON-RPC)
   INFURA_URL=https://mainnet.infura.io/v3/YOUR_INFURA_KEY
   PRIVATE_KEY=0xYOUR_ETH_PRIVATE_KEY

   # Bitcoin Core RPC
   BTC_RPC_USER=rpcuser
   BTC_RPC_PASS=rpcpass
   BTC_RPC_HOST=127.0.0.1
   BTC_RPC_PORT=8332
   BTC_NETWORK=mainnet

    A running Bitcoin Core node with RPC enabled.

    An Infura (or equivalent) API key for Ethereum.

🚀 Installation

git clone https://github.com/chuckyLeeVIII/ExDeX-Auto-RBF.git
cd ExDeX-Auto-RBF
npm install web3 dotenv bitcoin-core

✨ Usage

# Ethereum: double the gas price and rebroadcast
node autoRBF.js eth <YourStuckEthTxHash>

# Bitcoin: bump fee via `bumpfee` RPC
node autoRBF.js btc <YourStuckBtcTxId>

What happens under the hood?

    Fetch your original tx

    Bump the fee (ETH: ×2 gasPrice; BTC: default increment)

    Sign & send (ETH) or RPC-call (BTC)

    Watch the magic! 🎩✨

⚙️ Configuration & Tweaks

    ETH bump factor: change autoRBFeth’s currentGasPrice.mul(web3.utils.toBN(…)).

    BTC bump options: pass { conf_target, totalFee } to bumpfee.

    Networks & IDs: override via your .env.

🛡️ Safety Tips

    Never commit your PRIVATE_KEY or RPC credentials.

    Use dedicated addresses or hardware wallets for real funds.

    Monitor on Etherscan & mempool.space.

    Test on testnet before unleashing on mainnet.

    “In the mempool jungle, a little extra fee goes a long way.”_WANNA SEE MORE COOL TECH LIKE THIS ? **STAY TUNED** FOR ExdeX.cc and The Obsidion Sphere-New World Market predictions  
    — The ExDeX Early Bird Team 🦅
