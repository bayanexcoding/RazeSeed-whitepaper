# RAZESEED HEDGE ARCHITECTURE (2025 MODEL)
## Execution-Failure Based Downside Protection

## 1. Overview
RazeSEED provides investors with **up to 70% downside protection** against **execution failure**, not market failure.

The hedge activates **only when a project fails before completing all milestones**.

The protection is split into:
- **35% Token Hedge** (locked for 6 months)
- **35% Stable Hedge** (paid linearly over 6 months)

Escrow funds are always **100% refundable**.

---

## 2. Key Definitions

### Total Investment
Amount invested into the project.

### Escrow Balance
Unreleased funds held securely in the smart contract.

### Released Funds
Money already withdrawn by the founder after milestone approval.

### Loss Amount (Capital at Risk)
This is the *actual* capital at risk when a project fails.

**Formula:**
```
Loss Amount = Total Investment – Escrow Balance
```

### Hedge Split
```
Total Hedge = 70% of Loss Amount
Token Hedge = 35% of Loss Amount (Locked for 6 months)
Stable Hedge = 35% of Loss Amount (Vested over 6 months)
```

---

## 3. When Hedge ACTIVATES (Execution Failure)

Hedge applies only when execution breaks:
- Milestones not completed  
- Founder quits or disappears  
- AI verification fails  
- Severe delays  
- Fraud / misuse  
- Early-stage abandonment  

**Hedge = ACTIVE**

---

## 4. When Hedge DOES NOT Activate (Market Failure)

Hedge does NOT apply if:
- All milestones are completed  
- Founder delivered the product/service  
- Failure is due to market, customers, competition, or scale  

Funds were used correctly; thus hedge does not apply.

**Hedge = NOT ACTIVE**  
**Token hedge may still apply as goodwill (optional).**

---

## 5. Hedge Payout Structure

### (A) Escrow Refund — Always 100%
Any funds remaining in escrow are returned in full.

### (B) Hedge Refund (Only on Loss Amount)
```
Hedge = 70% of Loss Amount
```
Split:
- **35% in RazeSEED Tokens (6-month lock)**
- **35% in Stablecoin (Paid monthly over 6 months)**

---

## 6. Token Burn Mechanism

To support token value and maintain scarcity, burns occur:
- On project failure  
- On founder penalty  
- Part of protocol supply  
- During hedge events  

Burns make the token hedge more valuable over time.

---

## 7. Examples

---

### Example 1 — Failure Midway
**Investment:** ₹10,00,000  
**Spent:** ₹4,00,000  
**Escrow:** ₹6,00,000  

**Step 1 — Refund Escrow**
₹6,00,000 returned immediately.

**Step 2 — Loss Amount**
```
Loss = 10,00,000 – 6,00,000 = ₹4,00,000
```

**Step 3 — Hedge Calculation**
```
Total Hedge = 70% × 4,00,000 = ₹2,80,000
Token Hedge = ₹1,40,000
Stable Hedge = ₹1,40,000 (₹23,333 per month for 6 months)
```

**Final Recovery**
₹6,00,000 + ₹1,40,000 + ₹1,40,000 = **₹8,80,000 returned**  
Loss = **₹1,20,000**

---

### Example 2 — Failure Very Early
**Investment:** ₹20,00,000  
**Spent:** ₹2,00,000  
**Escrow:** ₹18,00,000  

**Refund**  
₹18,00,000 immediately.

**Loss**
```
Loss = 20,00,000 – 18,00,000 = ₹2,00,000
```

**Hedge**
```
Total Hedge = ₹1,40,000
Token = ₹70,000
Stable = ₹70,000 (₹11,667/month for 6 months)
```

**Final Recovery**
Total = **₹19,40,000**  
Loss = **₹60,000**

---

### Example 3 — Failure After Full Execution (No Stable Hedge)
**Investment:** ₹25,00,000  
**Spent:** ₹25,00,000  
**Escrow:** ₹0  

All milestones completed → Execution successful → Market failure.

**Hedge Trigger:** NO

Optional goodwill token hedge = 35% = ₹8,75,000 in tokens.

---

### Example 4 — Late Failure (75% spent)
**Investment:** ₹25,00,000  
**Spent:** ₹18,00,000  
**Escrow:** ₹7,00,000  

**Refund**
₹7,00,000

**Loss**
```
Loss = 18,00,000
```

**Hedge**
```
Total Hedge = 12,60,000
Token = 6,30,000
Stable = 6,30,000 (1,05,000/month over 6 months)
```

Final Recovery = **₹19,60,000**  
Loss = **₹5,40,000**

---

## 8. Why This Architecture Works

### Investor-Friendly
- Major portion of funds protected  
- Execution failure hedge  
- Token upside grows due to burns  
- Smooth 6-month stable payout  

### Platform-Safe
- Hedge applies only on real loss  
- No payouts for market failures  
- Treasury protected  
- Predictable liabilities  

### Legally Safe
- No guaranteed returns  
- No insurance-like structure  
- Conditional protection based on execution  

### Tokenomics-Strong
- Burns reduce supply  
- Locked tokens prevent dumping  
- Hedge tokens gain value  

---

## 9. Final Formula Summary

```
Loss Amount = Total Investment – Escrow Balance
Hedge = 70% of Loss Amount
Token Hedge = 35% of Loss Amount (Locked 6 months)
Stable Hedge = 35% of Loss Amount (6-month vest)
Escrow Refund = Always 100%
```

---

## End of Document
