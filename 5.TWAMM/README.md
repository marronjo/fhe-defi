# ⏳ FHE Encrypted TWAMM Hook

**A Fully Homomorphic Encryption (FHE)-powered hook for Uniswap v4 that enables private, long-term algorithmic trading via a Time-Weighted Average Market Maker (TWAMM) architecture.**

This hook introduces a new kind of **private long-term order execution**, where the trading schedule, amounts, and direction remain encrypted throughout the lifecycle of the order — until execution is justified.

## 🧠 What is TWAMM?

[TWAMM](https://www.paradigm.xyz/2021/07/twamm) (Time-Weighted Average Market Maker) is a design pattern that allows large orders to be split and executed gradually over time, minimizing slippage and front-running.

> Originally proposed by Paradigm, TWAMMs execute virtual orders in small steps during regular pool interactions (e.g. swaps or syncs). 

However, TWAMMs have two key limitations:
- **Orders are public** (exposed to MEV bots)
- **Execution schedule is visible** on-chain

## 🔐 Why FHE Encrypted TWAMM?

This hook integrates a **Fully Homomorphic Encryption coprocessor** to solve both problems:

- 🕵️‍♂️ **Encrypted Order Details**  
  Amounts, token pairs, and direction are encrypted at submission

- 🧮 **Private Execution Schedule**  
  The frequency, duration, and pace of order execution are processed inside the FHE coprocessor

- 🧾 **Encrypted State Updates**  
  Internal accounting (TWAMM virtual reserves, pending balances) is kept encrypted end to end

## 🔄 Flow: Encrypted Long-Term Orders

### 1. 📝 Submit Encrypted Long-Term Order
User submits a TWAMM order through the hook with encrypted:
- TokenIn / TokenOut
- Start time / duration
- Total size

This is stored privately in the hook’s encrypted state.

### 2. 🔁 Hook Triggered by Public Swaps
Regular swaps on the pool trigger:
- `beforeSwap()` → Executes any *due* encrypted TWAMM orders
- `afterSwap()` → Updates the * **virtual time** and requests new execution schedules if needed

*virtual time : In a TWAMM hook, time doesn't flow continuously — it advances in discrete steps whenever a pool interaction (like a swap) occurs. This is called virtual time. It helps determine how much of a long-term order should be executed at each individual step.

### 3. 🧠 Time Logic Delegated to Coprocessor
Only when afterSwap() determines that virtual time has progressed:

The hook sends a request to the FHE coprocessor

The coprocessor computes, homomorphically, the delta execution amounts for each encrypted long-term order

These deltas are returned as encrypted values, without identifying individual users or orders

📦 *Privacy via Delta Bundling*  
*Before execution, all encrypted deltas are bundled together by the hook.*  
*This means:*

*- No observer (on-chain or off-chain) can tell which deltas belong to which order*  
*- Execution appears as a single aggregated adjustment*  
*- Order flows are anonymized and unlinkable*

*This bundling significantly increases privacy by breaking the correlation between specific swaps and specific encrypted TWAMM orders.*


### 4. 🔓 Deferred Settlement
As TWAMM orders reach completion or withdrawal, decrypted receipts can be optionally generated for audit or settlement.


## 🧩 Key Features

- 🛡️ **Full Privacy** – Order size, price, and schedule are encrypted
- 🔄 **Reactive to Swaps** – Integrates seamlessly with Uniswap v4 flow
- 📉 **Slippage-Reduced Execution** – Ideal for large DAO or LP positions
- 🧾 **Optional Encrypted Receipts** – Compatible with encrypted ERC-6909


## 🧪 Use Cases

- **Private OTC Execution** – Institutional-size trades scheduled over hours/days
- **Algorithmic Strategies** – Programmatic time-sliced execution with total privacy  
- **Fair Launch Mechanisms** – Slow-release trading without giving away demand curves 
- **DAO Treasury Exits** – Gradually rebalance large positions without market signaling
