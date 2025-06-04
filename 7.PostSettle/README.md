# 🕵️‍♂️ FHE Post-Settle Reveal Hook

**A privacy-preserving Uniswap v4 hook that enables encrypted swap metadata to be revealed only *after* settlement — optionally and selectively.**

## 🧠 Concept

In DeFi today, all trade information — sender, token amounts, direction, and slippage — is visible **at the moment of execution**.  
This allows front-running, strategy leakage, and MEV extraction.

This hook explores a new model:  
🔒 **Keep trade details encrypted at execution**  
🔓 **Reveal metadata only after settlement, optionally or under certain conditions**

This is enabled by **Fully Homomorphic Encryption (FHE)** and a secure off-chain coprocessor.

## ⚙️ How It Works

### 1. 🛠 Swap Happens
- Users execute swaps through a Uniswap v4 pool with the post-settle hook attached.
- Swap metadata (e.g. input/output amounts, direction, and slippage) is passed encrypted to the hook.
- The encrypted swap is bundled with other swaps and decrypted once a given threshold is reached.
- The bundled swap is executed in the public pool with no clear trade size or path tied back to each user.

### 2. 🔐 Metadata Stored Encrypted
- Encrypted swap metadata is stored privately on-chain in encrypted form.
- No observer can determine trade intent or details at the time of execution.

### 3. ⏳ Delayed Reveal
- After a configurable delay or trigger (e.g. time-based, proof-based, or owner-initiated), the hook/coprocessor:
  - Decrypts the trade metadata and links it to a given user
  - Optionally emits an event or stores the plaintext state
  - Can include access control (e.g. only the trader or an auditor can see it)

## 🧩 Key Use Cases

- **Anti-front-running**: Prevent MEV bots from exploiting swap intent.
- **Private strategy execution**: Traders and LPs can hide execution parameters.
- **Delayed audit trails**: Compliance or analytics can be enabled without harming live privacy.
- **Private LP receipts**: Reveal liquidity behavior only after exit or threshold conditions.

## 🔐 Access Control Options

Post-settlement reveal logic can be controlled with:

- ✅ **Self-reveal** (only the user can decrypt)
- ⌛ **Time-locks** (reveal after N blocks or timestamp)
- 🧾 **Audit triggers** (reveal upon fraud proof or external trigger)
- 🌲 **Merkle/ZK proofs** (restrict access to a pre-verified group)

## 📦 Benefits

- ✅ Improves on-chain privacy without compromising execution flow
- 🔁 Works within the Uniswap v4 swap lifecycle
- 🧱 Composable with other FHE hook patterns (e.g. limit orders, custom AMMs)
- 🕸 Optional reveal logic offers flexible design patterns

## ⚠️ Limitations & Considerations

- Requires off-chain FHE coprocessor for decryption and reactivity
- Time delays or optional reveals may introduce UX complexity
- Metadata remains encrypted until revealed — may limit real-time observability
- Trust assumptions must be carefully handled (see notes on coprocessor liveness)

## 🔭 Future Extensions

- Combine with encrypted receipts for complete post-trade anonymity
- Integrate with DAO-controlled or threshold-based reveal logic
- Add proofs of fair execution for decentralized auditing

## 🧪 Research Status

This is an experimental concept intended for research and prototyping.  
It explores the boundary between **composable privacy** and **on-chain transparency**.
