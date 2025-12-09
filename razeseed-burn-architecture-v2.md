
# 📘 RAZESEED — FINAL TOKEN BURN, RESERVE & HEDGE ARCHITECTURE  
### *Full Mathematical Model, Stage Burns, Hedge Guarantees & Dynamic Mint-Rate Support*  
### **Updated MD File With Deeper Explanation of TotalMint, 1.55×M, and Reserve Logic**

---

# 0️⃣ Overview

This document defines the **complete, production-ready token burn, reserve, and hedge architecture** for the RazeSEED ecosystem.

It includes:

- A mathematically guaranteed **execution-failure hedge** (35% token hedge)
- A predictable & safe **reserve sizing model**
- Stage-based burn architecture
- Complete failure-flow logic
- Compatibility with **future dynamic mint rates**
- Clear explanation of why **TotalMint = 1.55 × M**
- Full numeric examples

This MD file merges all prior architecture + new clarifications.

---

# 1️⃣ Core Concepts

Whenever an investor deposits funds into a project escrow:

### The system mints *two* types of tokens:

### 1) **InvestorTokens (M)**  
- Goes to investor wallet  
- Locked 60 days  
- 5% unlock burn  
- Never burned due to project failure  
- Represents investor’s upside

### 2) **ProjectReserveTokens (Reserve)**  
- Used for:
  - Stage burns  
  - Final failure burn (35%)  
  - Token hedge (35% of loss)  
- Ensures hedge is always fully fundable  
- Shields treasury from token shortfall  

### Final architecture:
```
M = MintRate × InvestmentUSD  
Reserve = 0.55 × M  
TotalMint = M + Reserve = 1.55 × M
```

---

# 2️⃣ Why TotalMint = 1.55 × M?

This number comes from:

```
InvestorTokens = M  
ProjectReserveTokens = 0.55M  
Total = 1M + 0.55M = 1.55M
```

### Why add Reserve to M?
Because both pools are minted on-chain:

- One mint to investor  
- One mint to projectReserve  

So the **actual** new supply created is the sum of both.

### Why is Reserve 55% of M?

Because the reserve must cover:

1. **Final failure burn (35% of Reserve)**  
2. **Token hedge (up to 35% of full loss in tokens)**  

This leads to the inequality:

```
Reserve ≥ Burn + TokenHedge
```

Simplified mathematically, this gives:

```
ReserveFraction r ≥ 0.53846
```

Thus **55%** is selected to maintain buffer + safety margin.

This means:

> For every 1 token given to an investor, 0.55 tokens are minted into a reserve that fuels burns and hedge payments.

Contrary to appearing inflationary, this actually **reduces supply over time** due to high burn rates.

---

# 3️⃣ Minting Model (Supports Dynamic Mint Rate)

### Current Model  
```
MintRate = 6 RAZE per $1 invested
M = InvestmentUSD × MintRate
Reserve = 0.55 × M
TotalMint = 1.55 × M
```

### Future Dynamic Minting  
The architecture supports:
```
MintRate = f(SupplyState, MarketCap, TreasuryStrength, Governance)
```

**Invariant requirement:**

```
ReserveFraction r ≥ 53.85%
```

As long as Reserve ≥ required hedge + burn amounts, the system remains safe.

---

# 4️⃣ Stage-Based Burn Architecture

All burns apply **only to Reserve** — investor tokens are never touched.

| Stage | Escrow Released | Cumulative Burn (of Reserve) | Purpose |
|-------|-----------------|------------------------------|---------|
| Stage 0 | 0% | **5%** | Early abandonment |
| Stage 1 | 20–25% | **10%** | Limited progress |
| Stage 2 | 40–60% | **20%** | Mid-stage execution failure |
| Stage 3 | 80% | **25%** | Late-stage execution failure |
| Final Failure | Any | **35%** | Must reach 35% total burn |

### Burn Calculation
```
StageBurnTarget = Reserve × StagePercent  
ToBurnNow = StageBurnTarget − BurnAlreadyApplied  
```

---

# 5️⃣ Token Hedge Architecture (Mathematically Guaranteed)

Execution failure triggers:

```
LossUSD = InvestedUSD − EscrowBalance  
TokenHedgeTokens = LossUSD × MintRate × 0.35
```

Hedge tokens are:

- Taken from **ProjectReserve**
- Locked for 6 months
- Fully guaranteed by reserve sizing math

---

# 6️⃣ Full Mathematical Safety Proof

Reserve must cover both:

- Final failure burn  
- Token hedge  

Thus:

```
Reserve ≥ FailureBurn + TokenHedge
```

Substituting:

```
Reserve = r × M  
FailureBurn = 0.35 × Reserve = 0.35rM  
TokenHedge = 0.35M  
```

Therefore:

```
rM ≥ 0.35rM + 0.35M  
r ≥ 0.35r + 0.35  
r - 0.35r ≥ 0.35  
0.65r ≥ 0.35  
r ≥ 0.53846
```

### Final Reserve Fraction:
```
Reserve = 55% × M
```

This guarantees enough tokens for hedge + burn even in **full-loss scenarios**.

---

# 7️⃣ Final Failure Flow (End-to-End)

### 1. Compute loss
```
LossUSD = InvestmentUSD − EscrowBalance
```

### 2. Compute token hedge
```
TokenHedge = LossUSD × MintRate × 0.35
```

### 3. Determine failure burn
```
FinalBurnTarget = 0.35 × Reserve
ToBurnNow = FinalBurnTarget − BurnAlreadyApplied
```

### 4. Burn reserve tokens
### 5. Allocate token hedge to investors (locked)
### 6. If reserve insufficient → ProtocolReserve supplies shortfall (rare)

---

# 8️⃣ Numeric Examples (Clear & Step-by-Step)

## Example A — Early Failure

Investment: $10,000  
MintRate = 6  
```
M = 60,000  
Reserve = 33,000
```

Stage0 burn (5%):
```
1,650 burned → ReserveNow = 31,350
```

LossUSD = $1,000  
```
TokenHedge = 2,100 tokens
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

Reserve available:
```
31,350 → SAFE
```

---

## Example B — Mid-Stage Failure

Investment: $100,000  
```
M = 600,000  
Reserve = 330,000
```

Stage2 (20%) burn:
```
66,000 burned → ReserveNow = 264,000
```

Loss = $60,000  
```
TokenHedge = 126,000 tokens
```

Failure burn target:
```
115,500  
ToBurnNow = 49,500
```

Reserve needed:
```
49,500 + 126,000 = 175,500
```

Reserve available:
```
264,000 → SAFE
```

---

## Example C — Full Loss Worst Case

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

Leftover reserve = **2,250**

---

# 9️⃣ What Happens to Leftover Reserve?

Two governance options:

### A. Burn leftover → maximum deflation  
### B. Move leftover to ProtocolReserve → hedge stability

Recommended:  
**Move leftover to ProtocolReserve & burn annually.**

---

# 🔟 Smart Contract Components

- `RAZE` token (mint, burn, permit, snapshots, lock)
- `ProjectRegistry`
- `ReserveManager`
- `HedgeEngine`
- `FailureOracle`
- `ProtocolReserve`
- `Escrow`

---

# 1️⃣1️⃣ Why This Architecture is Safe & Scalable

✔ Hedge always fully fundable  
✔ Investor tokens never burned  
✔ Deflation guaranteed via burns  
✔ Reserve mathematically sized  
✔ Treasury protected from shock  
✔ Dynamic minting supported  
✔ Works for small, mid, and catastrophic failures  

---

# 1️⃣2️⃣ Final Statement

This is the **final, production-grade burn + reserve + hedge token architecture** for RazeSEED.  
It satisfies:

- Mathematical correctness  
- Treasury safety  
- Hedge guarantee  
- Long-term token deflation  
- Dynamic mint readiness  
- Audit readiness  

This is the blueprint the development, audit, and tokenomics team can rely on.

