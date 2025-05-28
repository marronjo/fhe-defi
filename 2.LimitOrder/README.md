## 🧊 Encrypted Limit Order Hook

**A privacy-preserving smart contract extension enabling encrypted limit orders on Uniswap v4 using Fully Homomorphic Encryption (FHE).**

### 🧭 Encrypted Limit Order Flow

This system enables **private (encrypted) limit orders** to be stored securely and automatically processed once market conditions are met.

### 1. 📝 Users Submit Encrypted Limit Orders
Users send **FHE-encrypted orders** to the hook contract, specifying:
- Order type (buy/sell)
- Token pair
- Encrypted limit price
- Encrypted quantity

These orders are stored securely and remain unreadable until decryption is explicitly triggered.

### 2. 💧 Public Swaps Proceed Normally
Other users continue to use the Uniswap pool for public swaps, without being affected by or aware of the encrypted limit orders.

### 3. ⚙️ Hook Intercepts Swaps
Every swap on the pool triggers two hook methods:
- `beforeSwap()` — checks for ready-to-execute orders
- `afterSwap()` — evaluates encrypted orders for decryption

### 4. 🔍 Executable Order Check (`beforeSwap`)
Before the swap executes:
- The hook checks **previously decrypted orders**
- If any are ready to execute
  - ✅ **Yes** → Execute the order(s)
  - ❌ **No** → Proceed with the public swap

### 5. 📉 Limit Condition Evaluation (`afterSwap`)
After the swap completes:
- The hook examines the updated pool state
- If the **current price hits any encrypted limit price**:
  - A **decryption request** is sent to the FHE coprocessor
  - Orders matching the price will be decrypted and queued for execution in the next swap cycle

### 6. 🔓 Deferred Decryption & Execution
Decryption is handled off-chain or via the Fhenix coprocessor. Once decrypted, limit orders are:
- Flagged as executable
- Executed during the **next `beforeSwap()` call**

### 7. 🔁 Ongoing Monitoring
With every new swap:
- Executable orders are attempted first
- Encrypted orders are evaluated again post-swap for potential decryption

This creates a dynamic, privacy-preserving loop that respects user intent without leaking sensitive trading information.

<img src="../assets/FHELimitOrderHook.png" width="800">

## 🧩 Key Advantages

- **Private Strategies** – Order prices and intents stay encrypted until valid  
- **MEV-Resistant** – No front-running possible on encrypted orders  
- **Composable** – Fully compatible with Uniswap v4 architecture  
- **Hands-Off Execution** – No bots or manual execution needed  

## 🧪 Ideal Use Cases

- Privacy-preserving DEX limit orders  
- DAO-managed treasury execution  
- High-volume, low-impact trades with optional visibility  

## 📦 Summary

This hook enables **secure, encrypted limit order placement and execution** within a decentralized exchange environment.

Encrypted orders:
- Are **invisible** until their price conditions are met  
- Are **decrypted only when justified** by market movement  
- Are **executed trustlessly** via Uniswap's normal swap flow  

> Bringing privacy and control to limit orders — without sacrificing composability.
