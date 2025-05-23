## Encrypted Market Order Hook

<img src="../assets/FHEMarketOrderHook.png" />

### 🔄 Encrypted Market Order Flow

This system enables private (encrypted) market orders to be automatically processed during regular Uniswap activity, using a smart contract hook and an FHE (Fully Homomorphic Encryption) coprocessor.

### 1. 🔐 Users Place Private Orders
Users send **encrypted** market orders to a special contract called the **hook contract**. These orders are hidden and stored securely until it's the right time to act on them.

### 2. 💱 Other Users Make Regular Swaps
Meanwhile, regular users continue to swap tokens in the **Uniswap pool** just like normal.

### 3. ⚙️ Hook Runs on Every Swap
Every time a swap is initiated, the system automatically calls two special functions in the hook contract:
- `beforeSwap()` — runs **before** the swap happens
- `afterSwap()` — runs **after** the swap is done

### 4. Check for Executable Orders (`beforeSwap`)
The system checks if any **decrypted** market orders are ready:
- ✅ **Yes** → Those orders are executed
- ❌ **No** → Nothing happens, move on

### 5. Check for Decryption Conditions (`afterSwap`)
After the swap, the system evaluates current market conditions to decide whether any **encrypted** orders should now be decrypted:
- ✅ **Yes** → Request decryption from the **FHE coprocessor**
- ❌ **No** → Orders remain encrypted and unchanged

### 6. ✅ Swap Completes
The swap is finalized. If any orders were executed or requested for decryption, it has now been handled.

### 7. 🔁 Repeat the Process
The next time a swap occurs, the entire process runs again from step 3.

### 📦 Summary

This flow allows encrypted market orders to remain private while still reacting to live market conditions. These hidden orders:
- Are only decrypted when the time is right
- Are executed automatically without user intervention
- Do not interfere with public swaps
