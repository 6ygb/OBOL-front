# OBOL Front‑End (POC)

This is a **testnet proof‑of‑concept** - **not meant for production**.

> ⚠️ **Disclaimer**
>
> - UI & contracts are **side‑project quality**; do not use in production.  
> - Performance depends on **Zama’s FHE on‑chain decryption pipeline**; operations are slower than typical non‑confidential flows.  
> - On‑screen values and rates may be **approximate** to preserve confidentiality.  
> - **Privacy note:** the app will **not** load your position until after you dismiss the disclaimer and reach the homepage.

OBOL contracts available here : https://github.com/6ygb/OBOL
</br>
Oracle relayer repo : https://github.com/6ygb/CAMM-OBOL-RELAYER

---

## What is OBOL?

**OBOL** is a confidential **lending market** built on **ERC‑7984 confidential tokens** with **Zama FHE**. Users add/remove collateral, borrow/repay, and supply/withdraw the debt asset. Risk is evaluated with public factors (A, B, price, indices), while amounts remain encrypted. Liquidations use public health checks; seized collateral is queued and claimable by the liquidator.

Front‑end features include position decryption, operator permission flows, market/oracle inspection, airdrops for test tokens, and a **Liquidation** suite for scanning/liquidating unhealthy positions.

---

## What’s inside

- **Vue 3 + TypeScript**
- **ethers v6** for wallet & contract calls
- **Zama relayer SDK** for FHE encryption & user decryption
- Contracts (via ABIs in `/contracts`):
  - **ConfidentialToken (ERC‑7984)**
  - **ConfLendMarket**
  - **ObolPriceOracle**

---

## Quick start

### Prereqs
- Node 18+ (or 20+ recommended)
- MetaMask (desktop)
- Sepolia ETH (testnet)

### Install & run
```bash
# 1) install deps
npm i

# 2) start dev server
npm run dev

# 3) build for prod
npm run build
npm run preview
```

### Network config

Network settings live in `Network.json`:
```json
{
  "public": {
    "CHAIN_NAME": "Sepolia",
    "CURRENCY_NAME": "ETH",
    "CURRENCY_SYMBOL": "ETH",
    "RPC_URL": "https://rpc.sepolia.org",
    "EXPLORER_URL": "https://sepolia.etherscan.io",
    "CHAIN_ID": 11155111
  }
}
```

Market & app settings live in `OBOL.json`:
```json
{
  "MARKET_EURtoUSD": "0xYourMarketEURtoUSD",
  "MARKET_USDtoEUR": "0xYourMarketUSDtoEUR",
  "OPERATOR_DEADLINE_SECS": 86400,
  "FRONTEND_GITHUB_URL": "https://github.com/6ygb/OBOL-front",
  "BACKEND_GITHUB_URL": "https://github.com/6ygb/OBOL"
}
```
> The UI stores **two** market addresses (EUR→USD and USD→EUR). Collateral & Debt token addresses are read from the selected market at runtime.

---

## Tabs & Features

### 1) **Market**
Select market and read public scalars:
- **Collateral / Debt token** addresses + symbols
- **Price** (collateral units per 1 debt unit, 1e6 scale), **Seize per unit** (price × bonus)
- **Borrow/Supply indices & APRs**, last accrual timestamp
- **Refresh Market Data** calls read‑only getters and updates the Oracle block

**Price direction**
- `EUR→USD`: uses inverse of `USD/EUR` from oracle (ceil)
- `USD→EUR`: uses the raw `USD/EUR`

### 2) **Operators**
Grant **operator** rights (time‑boxed) required by ERC‑7984 flows:
- Collateral token → for **add/remove collateral**
- Debt token → for **deposit / repay**
- Market (oToken) → for **redeem (withdraw underlying)**

`OPERATOR_DEADLINE_SECS` controls the default duration (e.g., 86,400s = 24h).

### 3) **Portfolio**
Decrypt and display **your** position:
- **Collateral**, **Debt**, **Max Borrow headroom** (all 1e6 scaled, decrypted client‑side)
- **Health Factor (HF)**: Raw & Normalized, live **LIQUIDATABLE** badge
- Actions: **Refresh Factors** (triggers market factor refresh via FHE callback),
  **Compute Max Borrow** (updates encrypted `maxBorrow` and decrypts), and **Reload Position**


### 4) **Supply**
Supply/withdraw the **debt asset**:
- **Deposit** mints market shares (oTokens)
- **Withdraw** redeems shares to underlying (clamped by pool liquidity above reserve)

Amounts are encrypted client‑side with Zama SDK.

### 5) **Collateral**
Manage collateral (encrypted):
- **Add collateral**
- **Remove collateral** (request is clamped on‑chain to keep HF ≥ threshold)

### 6) **Borrow / Repay**
Borrow up to your encrypted **max headroom**; repay reduces encrypted principal.
Both flows call FHE entrypoints with an encrypted amount.

### 7) **Oracle**
Inspect the **ObolPriceOracle**:
- `USD per 1 EUR`, **inverse** `EUR per 1 USD`
- **Fresh flag**, **epoch (last update)**, **stale TTL**
- **Refresh Oracle** reads oracle and recomputes the market price used by the front‑end

### 8) **Tokens**
Utilities for your confidential tokens:
- **Decrypt balances** (ERC‑7984) for the market’s collateral & debt tokens
- **Claim Airdrop** (if supported by token contract)
- **Copyable** token addresses

### 9) **Liquidation** 
Tools for liquidators and keepers:

- **Scan Liquidatable Slice**
  - Input: `offset`, `limit`
  - Calls `getLiquidatableSlice(offset,limit)` to fetch a page of **active borrowers** with a boolean **isLiquidatable**.
  - Displays a list with: truncated **address**, **HF (normalized)** when computable client‑side, and a **Copy** button.

- **Browse Active Positions**
  - Input: `offset`, `limit`
  - Calls `getActiveBorrowers(offset,limit)` to list current borrowers (may be healthy).

- **Check Health of Address**
  - Input: `address`
  - Reads public position factors (A, B, index snapshot) and computes:
    - **HF Raw**, **HF Normalized**, **Threshold**, **Liquidatable?**

- **Liquidate**
  - Input: `target address`, **repay amount**
  - Encrypts repay amount, calls `liquidate(target, encryptedAmt)`
  - Shows tx result and **claimable** seized collateral note

---

## Local storage keys

- `OBOL_DISCLAIMER_ACK_V1` - hides the disclaimer after acceptance.


---

## License

BSD 3‑Clause Clear License.
