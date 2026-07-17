# Web3 & Wallet Design Tokens Reference

> **IMPORTANT:** This token reference file contains boilerplates for Web3 frontend parameters.
> Do not use these parameters verbatim. Adapt them to fit the specific token types, target chains, and RPC configurations requested in each design task.

---

## 1. Network & Cluster Constants

```javascript
export const CLUSTERS = {
  MAINNET: {
    id: "mainnet-beta",
    name: "Mainnet Beta",
    rpcUrl: "https://api.mainnet-beta.solana.com", // Adapt to custom Node endpoints in production
    explorerUrl: "https://solscan.io",
    themeColor: "var(--color-primary)"
  },
  DEVNET: {
    id: "devnet",
    name: "Devnet Connection",
    rpcUrl: "https://api.devnet.solana.com",
    explorerUrl: "https://solscan.io?cluster=devnet",
    themeColor: "#f59e0b" // Warning amber color
  },
  LOCALNET: {
    id: "localnet",
    name: "Local Development Node",
    rpcUrl: "http://127.0.0.1:8899",
    explorerUrl: "https://explorer.solana.com?cluster=custom",
    themeColor: "#6b7280"
  }
};
```

---

## 2. Decimals & Pricing Display Parameters

Apply these precision limits when formatting token prices and amounts dynamically.

```javascript
export const PRECISION_CONFIG = {
  // USD pricing display configurations
  FIAT: {
    minDecimals: 2,
    maxDecimals: 2,
    abbreviationThreshold: 10000 // Convert to $10K+
  },
  
  // High-value token prices (e.g. BTC, SOL)
  HIGH_VALUE_TOKEN: {
    thresholdUsd: 1.00,
    minDecimals: 2,
    maxDecimals: 4
  },
  
  // Low-value/Micro-cap tokens (e.g. BONK)
  MICRO_CAP_TOKEN: {
    thresholdUsd: 0.01,
    minDecimals: 4,
    maxDecimals: 8,
    zeroSubscriptThreshold: 3 // Apply 0.0ₙ format if leading zeros after decimal >= 3
  }
};
```

---

## 3. Standard Transaction Safety Limits

Use these thresholds to trigger extra warnings or user confirmations.

```javascript
export const TRANSACTION_SAFETY = {
  // Dollar value above which high-value confirmation is enforced
  HIGH_VALUE_THRESHOLD_USD: 500.00,
  
  // Minimum local gas reserve to retain in wallet for subsequent transactions
  MIN_GAS_RESERVE_LAMPORTS: 2000000, // 0.002 SOL
  
  // Default slippage percent threshold for swaps
  DEFAULT_SLIPPAGE_PERCENT: 0.5,
  
  // Maximum slippage before returning warnings
  MAX_SLIPPAGE_WARN_PERCENT: 5.0
};
```

---

## 4. RPC Connection Limits

```javascript
export const RPC_LIMITS = {
  // Duration in milliseconds before an RPC request times out
  TIMEOUT_MS: 120000, // 12 seconds
  
  // Number of times to auto-retry a transient blockhash error before throwing
  BLOCKHASH_RETRY_COUNT: 2
};
```
