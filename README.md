# 🔐 Experimental FHE Hooks for Privacy-Preserving DeFi

**Exploring the intersection of Fully Homomorphic Encryption (FHE) and Decentralized Finance (DeFi)**

> This repository is dedicated to research and experimentation at the frontier of crypto privacy: applying FHE to DeFi protocols such as Uniswap v4 through customizable hooks.

## Table of Contents

- [Abstract](#-abstract)  
- [High-Level Overview](#-high-level-overview-how-swaps-work--uniswap-v4-features)  
  - [Basic Swap Flow](#-basic-swap-flow)  
  - [Uniswap v4 Hooks](#-enter-uniswap-v4-hooks--custom-logic)  
  - [v4 Hook Flow Example](#v4-hook-flow-example)  

- Order Types & Research Areas
  - [Encrypted Market Orders](https://github.com/marronjo/fhe-defi/tree/main/1.MarketOrder)
  - [Encrypted Limit Orders](https://github.com/marronjo/fhe-defi/tree/main/2.LimitOrder)
  - [Custom Encrypted AMM](https://github.com/marronjo/fhe-defi/tree/main/3.CustomAMM)
  - [Encrypted Trade Receipts](https://github.com/marronjo/fhe-defi/tree/main/4.TradeReceipts)
  - [TWAMM Orders](https://github.com/marronjo/fhe-defi/tree/main/5.TWAMM)
  - [Access Control](https://github.com/marronjo/fhe-defi/tree/main/6.AccessControl)

- [Summary](#-summary-of-fhe-hooks)
- [Key Learnings / Takeaways](#-learnings--takeaways)
- [Hooks vs Off-Chain Alternatives](#️-hook-based-designs-vs-off-chain-alternatives)

## 🧪 Abstract

This project explores how **Fully Homomorphic Encryption (FHE)** can be applied to 
**Decentralized Finance (DeFi)** to enable truly private on-chain interactions.

By integrating FHE with **Uniswap v4’s hook architecture**, we introduce a system 
where users can submit **encrypted market orders** without revealing intent or strategy.

Hooks trigger private computations within a secure coprocessor (e.g., Fhenix), enabling order execution only when encrypted conditions are met.

This approach preserves **composability** and **permissionless access**, while offering 
**meaningful privacy for traders** in public AMM environments.

## 🦄 High-Level Overview: How Swaps Work & Uniswap v4 Features

At its core, a **swap** is a simple trade between two tokens. In decentralized exchanges like Uniswap, this happens using **automated market makers (AMMs)**, which replace traditional order books with mathematical formulas to determine prices based on token reserves.

### 🔄 Basic Swap Flow

1. A user sends token A to a liquidity pool.
2. The pool returns token B, based on the current exchange rate.
3. The swap affects the pool’s token ratios, updating prices for the next trade.

### 🧱 Enter Uniswap v4: Hooks & Custom Logic

Uniswap v4 introduces **hooks**, a powerful new upgrade that allows developers to run custom smart contract logic at specific points in the swap lifecycle:

- `{before,after}Initialize`
- `{before,after}AddLiquidity`
- `{before,after}RemoveLiquidity`
- `{before,after}Swap`
- `{before,after}Donate`

These hooks turn Uniswap into a **programmable liquidity layer**, enabling use cases like:
- On-chain limit orders
- Dynamic fees
- KYC or access control
- Strategy execution
- 🕵️ And in our case: **encrypted, privacy-preserving order flow using FHE**

### v4 Hook Flow Example

<img src="assets/v4Hook.png" alt="Diagram showing Uniswap v4 hook flow with beforeSwap and afterSwap hooks">

This diagram illustrates how Uniswap v4 hooks (`beforeSwap`, `afterSwap`) fit into the swap lifecycle.

Please see [Table of Contents](#table-of-contents) for in-depth research.

### 🧩 Summary of FHE Hooks

| Hook Type         | Description                                                                                   | Key Features                                                                 |
|-------------------|-----------------------------------------------------------------------------------------------|------------------------------------------------------------------------------|
| 📈 **Market Order Hook** | Enables private, encrypted market orders that execute as conditions are met                 | - Encrypted order logic<br>- Auto-triggered by `beforeSwap()`<br>- FHE coprocessor integration |
| ⏳ **Limit Order Hook**  | Allows encrypted price-triggered execution of trades                                       | - Encrypted price conditions<br>- Conditional decryption via `afterSwap()`  |
| 🌊 **Custom AMM Hook**   | Overrides default swap logic to perform encrypted matching in a private AMM                | - No-op swap in pool manager<br>- Encrypted swaps executed within hook      |
| 🧾 **Trade Receipt Hook**| Issues encrypted ERC-6909 receipts (e.g. LP tokens, trade claims)                          | - FHE-wrapped receipts<br>- Token anonymity and privacy-preserving balances |
| ⌛ **TWAMM Hook**         | Enables encrypted time-weighted average market making with private execution scheduling    | - Private scheduling<br>- Delta bundling<br>- Execution triggers via coprocessor |
| 🔐 **Access Control Hook** | Enforces FHE-based rules for who can interact with a pool                                 | - Time-gated access<br>- Secret attribute filters<br> |
| 📤 **Post-Settlement Hook** | Handles encrypted actions after a swap (e.g. rewards, off-chain reporting)               | - Post-trade FHE actions<br>- Decouples logic from core execution path      |

## 📚 Learnings & Takeaways

This research demonstrates that **Fully Homomorphic Encryption (FHE)** can meaningfully expand the design space of decentralized finance by enabling **privacy-preserving logic** directly within **Uniswap v4 hooks**.

With a myriad of use cases — from market and limit orders to TWAMM, custom AMMs, and encrypted receipts —  
FHE allows for **encrypted intent**, **conditional execution**, and **selective disclosure**,  
all without sacrificing composability.

However, key trade-offs emerge:

- FHE systems rely on **off-chain coprocessors**, introducing:
  - Latency
    - trade timing is affected
    - slower UX compared to regular swaps
    - price swings can cause worse execution price for users
  - Liveness requirements
    - coprocessor uptime
    - operation is skipped / lost (never executed)
    - recover previous state after outages
  - Processing Capacity
    - can off-chain services keep up with real-time trading demand
    - FHE operations are **intensive** and performance remains a limiting factor for high-frequency or large-scale order flows.

- User Expsense 💸
  - **On-chain interactions involving FHE hooks** typically incur higher gas costs due to:
    - Additional logic for encrypted order evaluation
    - Interaction with external coprocessors or encrypted state
    - Complexity of maintaining privacy-preserving conditions inside smart contracts
    - All FHE encrypted numbers are 256 bits, so no storage slot optimization / packing is possible within smart contracts

  - Users may also bear **indirect costs** such as:
    - Fees paid to off-chain relayers or coprocessor operators
    - Delays in order execution due to FHE decryption latency

  - Depending on the chain, these costs may be **significantly amplified on Ethereum mainnet**, but more manageable on **L2s or high-throughput chains**.

  - Designing efficient UX flows (e.g., batching, permits, meta-transactions) will be critical to minimizing user burden and making FHE-enabled DeFi approachable.

- While **on-chain privacy** is improved, **metadata leakage** (e.g. timestamps) remains a challenge.

### ⚖️ Hook-Based Designs vs Off-Chain Alternatives

#### 🔗 Hook-Based Designs

Hook-based systems are implemented directly within the Uniswap v4 protocol using its native hook architecture.

**Benefits:**
- Tight **integration** with Uniswap pools, enabling privacy logic to be executed **in-line with swaps**.
- **Reactive and composable**: Hooks respond immediately to swap events (`beforeSwap`, `afterSwap`), making them ideal for fully on-chain strategies like encrypted AMMs, TWAMM, or access control.
- Preserve decentralization by keeping execution within Ethereum’s security boundary.

**Drawbacks:**
- Come with **higher gas costs** due to extra logic, state handling, and computational overhead.
- Increased **architectural complexity**—developers must reason about async decryptions, gas limits, and state syncing within Uniswap’s execution model.
- Deployment is more constrained: contracts must conform to hook interface standards and interact safely with the PoolManager.

---

#### 🌐 Off-Chain Alternatives

Off-chain models shift order handling and encryption/decryption logic to external services or relayers.

**Benefits:**
- **Cheaper execution**: Most of the computation (especially FHE operations) happens off-chain, reducing on-chain gas consumption.
- Greater **flexibility** in system design — easier to iterate, upgrade, and experiment without redeploying smart contracts.
- Can support complex logic, batching, or fallback mechanisms not easily implemented in Solidity.

**Drawbacks:**
- Sacrifice **reactivity** — orders can't respond instantly to swap events without trusted intermediaries.
- Lose **composability** with Uniswap and other DeFi protocols, as order flow becomes isolated from on-chain logic.
- Introduce **trust assumptions** and reduce decentralization — unless mitigated through  
  **cryptoeconomic incentives**, **multi-party computation (MPC)**, or **zero-knowledge proofs (ZKPs)** to enforce correctness and fairness in off-chain execution.

---

Choosing between these models depends on your design goals:

- Use **hooks** when composability, reactivity, and decentralization are priorities.
- Use **off-chain models** for experimental features, reduced costs, or where trust assumptions are acceptable and manageable.
