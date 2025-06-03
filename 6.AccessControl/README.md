# 🔐 FHE-Powered Access Controlled Pools

This project explores **privacy-preserving access control** for Uniswap v4 pools using **Fully Homomorphic Encryption (FHE)** and smart contract hooks. 

By leveraging FHE, the pool can enforce complex access rules **without revealing sensitive user data** such as token balances, roles, or usage patterns — all evaluated inside an encrypted coprocessor.

## 🛠 Key Access Control Models

### 🪪 1. Token-Gated Access (Encrypted Attribute Checks)

Allow only users holding a specific token or NFT to interact with the pool — **without revealing balances** on-chain.

- Pools can verifiy users **encrypted token balances** against a certain threshold.
- Ensure a user meets minimum encrypted trading volume requirements to interact with pool.
- Gate access to the pool by certain encrypted thresholds / loyalty metrics / no toxic orderflow.

**Benefits:**

- Privacy of token holdings is preserved.
- Supports gating by fungible tokens, NFTs, or membership tokens.
- Enables exclusive pools for premium users or DAO members.

### 📅 2. Subscription / Epoch-Based Access

Enforce limits on how often a user can access the pool (e.g., once per day, once per selected time period / epoch).

- Users last interaction time is stored in the contract.
- Time since last interaction is calculated and verified against an encrypted threshold.
- Pool will reject users breaching their rate-limit / access allowance.

**Benefits:**

- Enables pay-per-period or cooldown-based models.
- Keeps user activity time allowance private.
- Prevents abuse or excessive usage.

### 🧾 3. Encrypted Role-Based Access

Control access based on user roles or tiers (e.g., admin, trader, liquidity provider) **without revealing the user’s role on-chain**.

- Each user receives an encrypted role token.
- Along with their action request, users submit this encrypted role.
- The hook passes it to the coprocessor which evaluates whether the role permits the requested action.
- Access decisions are made privately and enforced on-chain.
- Silent rejection for users without necessary permission.

**Benefits:**

- Enables fine-grained, private permissioning.
- Protects sensitive hierarchy and role information.
- Supports dynamic and composable access policies.

## 🧩 Composability & Privacy

These access models can be **combined** within a single FHE coprocessor evaluation:

- For example, a user might need to hold a specific token **and** have an appropriate role **and** respect usage limits.

All inputs, thresholds, and policies remain encrypted throughout the process, providing strong privacy guarantees **while enabling flexible, secure access control**.

## ⚙️ How It Works (High Level)

1. User's sensitive data is encrypted (token balances, role, usage timestamp).
2. The hook evaluates the encrypted data vs encrypted access rules using FHE.
3. The hook enforces the encrypted rules (allow / silent reject / revert).

## 📚 Summary

FHE-based access control pools enable:

- **Privacy-first permissioning** without revealing user attributes.
- **Dynamic, complex policies** executed securely off-chain.
- **Non-custodial and trustless enforcement** directly via hooks.

This approach is ideal for DeFi applications requiring confidentiality, fine-grained permissions, and anti-abuse mechanisms — all seamlessly integrated with Uniswap v4’s hook architecture.

*Powered by Fully Homomorphic Encryption and Uniswap v4 hooks.*
