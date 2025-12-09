# 📘 RAZESEED — FINAL TOKEN BURN & RESERVE ARCHITECTURE  
### End-to-End Burn Logic, Reserve Math, Hedge Payouts, Dynamic Mint Support  
### **Production-Ready .MD Specification**

---

# 0. Overview

This document defines the **complete burn mechanism, reserve sizing model, and hedge-aligned token architecture** for RazeSEED.

It guarantees:

- Investor tokens are **never burned**
- Total system remains **deflationary**
- Token hedge (35% of execution loss) is **always fully fundable**
- All burns come from a **mathematically sized ProjectReserve**
- Treasury exposure stays predictable and safe
- Architecture supports **future dynamic mint rates** (not fixed to 6 RAZE/$1 forever)

Mint rate today:  
```
MintRate = 6 RAZE per $1 invested
```
Future architectures may use a dynamic mint rate model.

---

# 1. Core Token Model Summary

Whenever an investor deposits into a project escrow:

Two token pools are minted:

### 1️⃣ InvestorTokens (M)
- Goes to investor’s wallet  
- Locked 60 days  
- Unlock burn = 5%  
- **Never burned for project performance**

### 2️⃣ ProjectReserveTokens (Reserve)
- Stored in project-specific reserve  
- Used for:
  - Stage burns  
  - Final failure burn  
  - Token hedge allocation (35% of loss)  
- Ensures hedge always has backing  
- Prevents treasury shortfall

### Finalized Reserve Fraction  
```
Reserve = 55% of M
```

This is mathematically derived to ensure hedge + burn goals are always safely fundable.

---

# 2. Mint Formula & Dynamic Mint Model

### Current mint formula
```
M = MintRate × InvestmentUSD
MintRate = 6 tokens per $1
```

### Reserve allocation
```
Reserve = 0.55 × M
```

### Total project-level token creation
```
TotalMint = M + Reserve = 1.55 × M
```

### Dynamic Minting (Future)
This architecture fully supports future dynamic minting such as:

```
MintRate = f(SupplyState, TreasuryHealth, MarketCondition, Governance)
```

The only invariant:  
```
ReserveFraction r ≥ 53.85%
```
to guarantee hedge coverage.

---

# 3. Burn Architecture (Stage + Final Failure Model)

### All burns apply ONLY to ProjectReserve.

_cumulative percentages of Reserve_:

| Stage | Description | Cumulative Burn Required |
|-------|-------------|--------------------------|
| Stage 0 | No milestones achieved | **5%** |
| Stage 1 | Early progress but failed | **10%** |
| Stage 2 | Mid-stage failure | **20%** |
| Stage 3 | Late-stage failure | **25%** |
| Final Failure | All failure classifications | **35% (final)** |

### Burn Logic

Burns are cumulative.

At each stage:

```
target = Reserve × StagePercent
toBurn = target − burnAlreadyApplied
burn(toBurn)
```

At final failure:

```
FinalFailureBurnTarget = 0.35 × Reserve
ToBurnNow = FinalFailureBurnTarget − burnAlreadyApplied
burn(ToBurnNow)
```

---

# 4. Token Hedge Allocation (Guaranteed)

Execution-failure hedge requires:

```
TokenHedgeTokens = 35% × LossUSD × MintRate
```

These tokens are allocated from **ProjectReserve**, locked for 6 months.

Investor tokens are never touched.

---

# 5. Mathematical Safety Proof

We must ensure:

```
Reserve ≥ FinalFailureBurnTarget + TokenHedgeTokens
```

Substitute:

```
Reserve = r × M
FinalFailureBurn = 35% × Reserve = fb × r × M
TokenHedgeTokens = 35% × M (worst-case full-loss)
```

Inequality:

```
r × M ≥ fb × r × M + 0.35 × M
r ≥ fb × r + 0.35
r(1 − fb) ≥ 0.35
r ≥ 0.35 / (1 − fb)
```

With fb = 0.35:

```
r ≥ 0.53846
```

Thus final value:

```
ReserveFraction r = 55%
```

This gives safety buffer and predictable hedge funding.

---

# 6. Final Failure Flow (End-to-End)

### Step 1 — Compute Loss
```
LossUSD = InvestmentUSD − EscrowBalance
```

### Step 2 — Compute token hedge
```
TokenHedgeTokens = LossUSD × MintRate × 0.35
```

### Step 3 — Compute failure burn requirement
```
FinalFailureBurnTarget = 0.35 × Reserve
ToBurnNow = FinalFailureBurnTarget − BurnAlreadyApplied
```

### Step 4 — Burn tokens from Reserve
```
burn(ToBurnNow)
```

### Step 5 — Allocate token hedge
```
transfer(ProjectReserve → HedgeVault, TokenHedgeTokens)
lock for 6 months
```

### Step 6 — Handle shortfall (rare)
If:
```
ReserveRemaining < TokenHedgeTokens
```
→ Pull difference from **ProtocolReserve**.

---

# 7. Complete Worked Examples

---

## **Example A — Small Project, Early Failure**

Investment = $10,000

```
M = 10,000 × 6 = 60,000
Reserve = 33,000
```

### Stage 0 burn = 5%
```
1,650 burned
ReserveNow = 31,350
```

Loss = $1,000  
Token hedge:
```
2,100 RAZE
```

Failure burn target:
```
11,550
ToBurnNow = 11,550 − 1,650 = 9,900
```

Reserve needed:
```
9,900 + 2,100 = 12,000
```

Reserve available = 31,350 → SAFE.

Leftover reserve = **19,350**

---

## **Example B — Mid-Stage Failure**

Investment = $100,000

```
M = 600,000
Reserve = 330,000
```

Stage 2 cumulative burn (20%):
```
66,000 burned
ReserveNow = 264,000
```

Loss = $60,000  
Token hedge:
```
126,000 RAZE
```

Failure burn target:
```
115,500
ToBurnNow = 49,500
```

Total needed:
```
49,500 + 126,000 = 175,500
```

Reserve available = 264,000 → SAFE.

Leftover reserve = **88,500**

---

## **Example C — Worst-Case Full Loss**

Investment = $50,000

```
M = 300,000
Reserve = 165,000
Loss = 50,000
```

Token hedge:
```
105,000
```

Failure burn target:
```
57,750
```

Total needed:
```
162,750
Reserve = 165,000 → SAFE
```

Leftover = **2,250**

---

# 8. Leftover Reserve Handling

Two governance options:

### A. Burn leftover  
Maximizes deflation.

### B. Move leftover to ProtocolReserve  
Used for:
- Cross-project shortfalls  
- Long-term treasury stabilization  
- Buyback funding  

Recommended: **Move leftover to ProtocolReserve, burn annually**.

---

# 9. Dynamic Mint Rate Support (Future)

This architecture supports dynamically adjusting the mint rate:

Examples:

```
MintRate = 6 → 5 → 4 (as supply matures)
MintRate = f(CirculatingSupply)
MintRate = f(TreasuryStrength)
```

Invariant must hold:

```
ReserveFraction ≥ 0.53846
```

Thus hedge guarantees remain intact.

---

# 10. Smart Contract Components

- `RAZE`  
  - ERC20: mint, burn, snapshot, permit, lock  
- `ProjectRegistry`  
  - Tracks M, Reserve, Burns, Status  
- `ReserveManager`  
  - Executes burns, allocates hedge tokens  
- `HedgeEngine`  
  - Computes losses & hedge amounts  
- `FailureOracle`  
  - Manages failure decisions + appeals  
- `ProtocolReserve`  
  - Stores leftover reserves + fallback liquidity  
- `Escrow`  
  - Triggers mint and investment lifecycle  

---

# 11. Solidity Flow (Pseudo Snippets)

### Mint & Reserve Allocation
```solidity
M = investmentUSD * mintRate;
Reserve = M * 55 / 100;

mint(investor, M);
mint(projectReserveVault, Reserve);
```

### Stage Burn
```solidity
uint256 target = Reserve * stagePercent / 100;
uint256 toBurn = target - burnApplied;
burn(projectReserveVault, toBurn);
burnApplied = target;
```

### Final Failure
```solidity
TokenHedge = LossUSD * mintRate * 35 / 100;

FinalBurnTarget = Reserve * 35 / 100;
ToBurnNow = FinalBurnTarget - burnApplied;

burn(projectReserveVault, ToBurnNow);
transfer(projectReserveVault, hedgeVault, TokenHedge);
```

---

# 12. Guarantees

✔ Token hedge is always fully fundable  
✔ Investor tokens are never touched  
✔ Deflation built-in through stage & final burns  
✔ Treasury exposure predictable  
✔ Architecture supports dynamic future mint-rate tokenomics  
✔ Works for small, mid, and catastrophic failures  

---

# 13. Final Statement

This system is:

- Mathematically validated  
- Execution-failure hedge compatible  
- Deflationary and scalable  
- Treasury safe  
- Ready for Solidity integration and security audit  

This is the **final, production-ready burn + reserve architecture** for RazeSEED.

