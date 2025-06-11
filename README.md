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
// autoRBF.js
// ──────────────────────────────────────────────────────────────────────────────
//   The ExDeX “Early Bird” Auto-RBF Suite — ETH & BTC in One Script
// ──────────────────────────────────────────────────────────────────────────────

require('dotenv').config();

// ─ ETHERNETIUM (Ethereum) SETUP ────────────────────────────────────────────────
const Web3      = require('web3');
const web3      = new Web3(new Web3.providers.HttpProvider(process.env.INFURA_URL));

async function autoRBFeth(originalTxHash) {
  const tx = await web3.eth.getTransaction(originalTxHash);
  if (!tx) throw new Error('Original ETH tx not found in mempool.');
  console.log(`✨ ETH tx ${originalTxHash}: nonce=${tx.nonce}, from=${tx.from}`);

  const currentGasPrice = web3.utils.toBN(tx.gasPrice);
  const bumpedGasPrice  = currentGasPrice.mul(web3.utils.toBN(2)); // ×2 bump
  console.log(`⚡ Bumping gas: ${web3.utils.fromWei(currentGasPrice, 'gwei')}→${web3.utils.fromWei(bumpedGasPrice, 'gwei')} Gwei`);

  const replacement = {
    from:     tx.from,
    to:       tx.to,
    value:    tx.value,
    data:     tx.input,
    nonce:    tx.nonce,
    gas:      tx.gas,
    gasPrice: bumpedGasPrice,
    chainId:  tx.chainId || await web3.eth.getChainId(),
  };
  const signed  = await web3.eth.accounts.signTransaction(replacement, process.env.PRIVATE_KEY);
  const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction);
  console.log(`✅ ETH replacement tx: ${receipt.transactionHash}`);
  return receipt.transactionHash;
}

// ─ BITCOIN SETUP ───────────────────────────────────────────────────────────────
const Client    = require('bitcoin-core');
const btcClient = new Client({
  network:  process.env.BTC_NETWORK || 'mainnet',
  username: process.env.BTC_RPC_USER,
  password: process.env.BTC_RPC_PASS,
  host:     process.env.BTC_RPC_HOST  || '127.0.0.1',
  port:     process.env.BTC_RPC_PORT  || 8332,
});

async function autoRBFbtc(txid) {
  console.log(`✨ BTC tx ${txid}: bumping fee via RPC…`);
  const bump = await btcClient.command('bumpfee', txid);
  console.log(`✅ BTC bumped txid: ${bump.txid}`);
  console.log(`   new fee: ${bump.fee} BTC (orig: ${bump.origfee} BTC)`);
  return bump.txid;
}

// ─ ENTRYPOINT ────────────────────────────────────────────────────────────────
async function main() {
  const [,, chain, hash] = process.argv;
  if (!chain || !hash || !['eth','btc'].includes(chain.toLowerCase())) {
    console.error('\nUsage: node autoRBF.js <eth|btc> <TxHash/TxId>\n');
    process.exit(1);
  }
  try {
    if (chain.toLowerCase() === 'eth') {
      await autoRBFeth(hash);
    } else {
      await autoRBFbtc(hash);
    }
    process.exit(0);
  } catch (e) {
    console.error(`❌ Auto-RBF [${chain.toUpperCase()}] failed: ${e.message}`);
    process.exit(1);
  }
}

if (require.main === module) main();

module.exports = { autoRBFeth, autoRBFbtc };

// autoRBF.js
// The ExDeX Early Bird Auto-RBF Suite — ETH & BTC

require('dotenv').config();
const Web3 = require('web3');
const Client = require('bitcoin-core');

/**
 * Bump and rebroadcast a stuck Ethereum transaction.
 * @param {string} txHash The original Ethereum tx hash.
 * @returns {Promise<string>} The new transaction hash.
 */
async function autoRBFeth(txHash) {
  const web3 = new Web3(process.env.INFURA_URL);
  const tx = await web3.eth.getTransaction(txHash);
  if (!tx) throw new Error('Original ETH tx not found.');

  console.log(`ETH tx ${txHash}: nonce=${tx.nonce}, from=${tx.from}`);

  const current = web3.utils.toBN(tx.gasPrice);
  const bumped = current.mul(web3.utils.toBN(2)); // 2× bump
  console.log(`Bumping gasPrice: ${web3.utils.fromWei(current, 'gwei')}→${web3.utils.fromWei(bumped, 'gwei')} Gwei`);

  const replacement = {
    from:     tx.from,
    to:       tx.to,
    value:    tx.value,
    data:     tx.input,
    nonce:    tx.nonce,
    gas:      tx.gas,
    gasPrice: bumped,
    chainId:  tx.chainId || await web3.eth.getChainId(),
  };

  const { rawTransaction } = await web3.eth.accounts.signTransaction(replacement, process.env.PRIVATE_KEY);
  const receipt = await web3.eth.sendSignedTransaction(rawTransaction);
  console.log(`✅ ETH replacement tx: ${receipt.transactionHash}`);
  return receipt.transactionHash;
}

/**
 * Use Bitcoin Core RPC to bump the fee of a replaceable TX.
 * @param {string} txid The original Bitcoin transaction ID.
 * @returns {Promise<string>} The new transaction ID.
 */
async function autoRBFbtc(txid) {
  const client = new Client({
    network:  process.env.BTC_NETWORK || 'mainnet',
    username: process.env.BTC_RPC_USER,
    password: process.env.BTC_RPC_PASS,
    host:     process.env.BTC_RPC_HOST || '127.0.0.1',
    port:     process.env.BTC_RPC_PORT || 8332,
  });

  console.log(`BTC tx ${txid}: bumping fee…`);
  const result = await client.command('bumpfee', txid);
  console.log(`✅ BTC bumped txid: ${result.txid}`);
  console.log(`   new fee: ${result.fee} BTC (orig: ${result.origfee} BTC)`);
  return result.txid;
}

/**
 * CLI entrypoint.
 * Usage: node autoRBF.js <eth|btc> <txHashOrId>
 */
async function main() {
  const [,, chain, id] = process.argv;
  if (!chain || !id || !['eth', 'btc'].includes(chain.toLowerCase())) {
    console.error('Usage: node autoRBF.js <eth|btc> <txHashOrId>');
    process.exit(1);
  }

  try {
    if (chain === 'eth') {
      await autoRBFeth(id);
    } else {
      await autoRBFbtc(id);
    }
    process.exit(0);
  } catch (err) {
    console.error(`❌ ${chain.toUpperCase()} Auto-RBF failed: ${err.message}`);
    process.exit(1);
  }
}

if (require.main === module) {
  main();
}

module.exports = { autoRBFeth, autoRBFbtc };


