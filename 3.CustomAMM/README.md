## 🧬 Encrypted AMM Hook (FHE x Uniswap v4)

**A custom Uniswap v4 hook that intercepts swaps and redirects them into a Fully Homomorphic Encrypted (FHE) Automated Market Maker (AMM) — enabling private trading logic and encrypted liquidity management.**

This hook redefines how swaps work in Uniswap by:
- **Intercepting incoming swap calls**
- **Encrypting trade details on entry**
- **Skipping (no-op'ing) the default Uniswap swap**
- **Executing the swap privately within a custom encrypted AMM**

## 🔒 Why Encrypted AMMs?

In public AMMs like Uniswap, all price discovery, slippage, trade volume, and liquidity behavior is fully transparent. This:
- Exposes trading intent to MEV bots
- Makes front-running trivial
- Discourages large or strategic trades

This custom hook introduces an **encrypted execution layer**, allowing swaps to be handled in private using the [Fhenix Coprocessor](https://www.fhenix.io/), while maintaining the familiar interface of a Uniswap pool.

## 🧠 System Overview

Here’s how the encrypted AMM hook works under the hood:

### 1. 📨 Swap is Initiated  
A user calls `swap()` on the Uniswap pool, just like any normal trade.

### 2. 🔐 Order is Encrypted (`beforeSwap`)  
In `beforeSwap()`, the hook:
- **Extracts the swap parameters**
- **Encrypts the input amount and trade direction**
- **Stores the encrypted order**
- **Returns `amountIn = 0` to signal a no-op**  
  > This prevents the PoolManager from performing the swap — effectively hijacking the swap process.

### 3. 🧮 Encrypted Swap Executed  
Within the hook, a **custom AMM engine** built on FHE logic processes the swap:
- Encrypted price and pool state are evaluated securely
- Output amount is computed using encrypted arithmetic
- Token balances are updated accordingly (via token callbacks or internal accounting)

### 4. 🔁 Repeat as Needed  
Each future swap triggers the same logic:
- Encrypt → No-op → FHE execution

This creates a **private execution environment** that coexists with Uniswap’s public interface.

## ⚙️ Swap Flow Comparison

| Step                | Default Uniswap v4      | Encrypted AMM Hook              |
|---------------------|-------------------------|----------------------------------|
| `swap()`            | PoolManager runs swap   | Hook intercepts swap            |
| Price visibility    | Public                  | Encrypted (FHE)                 |
| Execution engine    | Constant Product (XYK)  | Custom AMM (encrypted XYK or other)          |
| MEV exposure        | High                    | None                            |
| Composability       | Native                  | Native (via hook)               |

---

## 🧩 Key Features

- ✅ **No-op Swap Logic** — Cleanly avoids default AMM path  
- 🔒 **FHE Encrypted Execution** — Trades and state remain private  
- 🧪 **Encrypted Liquidity Provisioning** — LP positions and receipts are shielded from public view  
- ⚙️ **Custom AMM Design** — Build any pricing curve inside FHE logic  
- 🔁 **Swap-Compatible** — Fully hook-based, works with Uniswap routers  
- 🛡️ **Anti-MEV & Front-running** — Orders and prices are never exposed  

## 🚀 Example Use Cases

- Private OTC or whale swaps without market signaling  
- DAO treasury rebalancing with zero strategy leakage  
- MEV-resistant cross-pool execution routes  
- Research/prototype novel AMM curves in private

## 📦 Summary

This custom hook transforms Uniswap into a **hybrid encrypted AMM**, offering full privacy while maintaining compatibility with standard interfaces.

> Trade privately. Route flexibly. Break the visibility ceiling of DeFi.

<img src="../assets/FHECustomAMMHook.png" width="800" >
