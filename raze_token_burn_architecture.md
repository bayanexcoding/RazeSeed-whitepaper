# RAZESEED TOKEN BURN ARCHITECTURE

## Overview

The Token Burn Architecture for RazeSEED defines when, why, and how tokens are removed from circulation. Burns are a core leverage mechanism to:

- Maintain token scarcity and long-term value
- Support the token-based hedge mechanism
- Penalize abusive behavior and reward protocol health
- Offset minting (6 tokens per $1) to avoid supply inflation

This document specifies burn triggers, suggested percentages, on-chain mechanics, accounting, governance, and implementation checklist.

---

## Objectives

1. Ensure net deflation (burn > mint over time).
2. Align token supply with platform growth and hedge obligations.
3. Penalize fraud/delays to increase protocol integrity.
4. Provide predictable, auditable, and on-chain burns.
5. Communicate transparent burn policies to investors and community.

---

## Burn Triggers & Recommended Percentages

> **Important:** Percentages are recommendations; governance (DAO) can change them.

1. **Project Failure Burn (Primary)**
   - **Trigger:** Project classified as failed by protocol rules (execution failure before completing milestones) or by DAO vote.
   - **Scope:** Burn project-linked tokens (minted to investors & any project allocations tied to that project) or a protocol reserve equivalent.
   - **Recommended %:** **35%** of the project-related token allocation or minted amount tied to that project.
   - **Rationale:** Matches hedge mechanics; failure reduces supply and increases token hedge value.

2. **Delay / Missed Milestone Burn (Penalty)**
   - **Trigger:** Founder misses milestone deadline beyond allowed grace period.
   - **Scope:** Burn part of the founder's locked tokens or the project's token allocation.
   - **Recommended %:** **10%** of locked tokens for that milestone.
   - **Rationale:** Discourages delays and enforces discipline.

3. **Unlock Burn (Anti-Inflation on Unlock)**
   - **Trigger:** When tokens unlock after the lock period (investor/founder unlock, staking unlocks).
   - **Scope:** Apply a small burn on tokens that are about to be released.
   - **Recommended %:** **5%** of unlocked amount.
   - **Rationale:** Continuous small deflation; encourages long-term holding.

4. **Revenue Buyback & Burn (Growth-Linked Burn)**
   - **Trigger:** Monthly/quarterly revenue flows from subscriptions, commissions, and fees.
   - **Scope:** Use a percentage of fiat/USDT revenue to buy tokens from open market and burn them.
   - **Recommended %:** **2%–5%** of monthly revenue (scale with performance).
   - **Rationale:** Directly ties platform success to token scarcity.

5. **Treasury Oversupply / Strategic Burn (Periodic)**
   - **Trigger:** Governance decision or automatic condition when new-mint > burn or supply growth exceeds thresholds.
   - **Scope:** Protocol-level burn from treasury reserves.
   - **Recommended %:** Target **10%** of total supply annually (adjustable)
   - **Rationale:** Keeps long-term supply disciplined.

6. **Anti-Abuse / Fraud Burn**
   - **Trigger:** Proven fraud, abuse, duplicate accounts, or malicious activity discovered by AI or audit.
   - **Scope:** Confiscation and burn of offending accounts' locked tokens (with dispute process).
   - **Recommended %:** **100%** of problematic locked tokens (case-by-case).
   - **Rationale:** Strong deterrent against gaming the system.

---

## Burn Mechanics & On-Chain Implementation

### A. Who can trigger burns?
- **Smart contracts** (automatic): Milestone checks, unlock events, failure detection modules.
- **Governance (DAO):** Strategic burns, annual burns, exception handling.
- **Admin multisig:** Emergency burns with high-level checks and timelocks.

### B. Core smart-contract features
- **burnTokens(address, amount, reasonHash)** – public burn function that destroys tokens and emits event with reason.
- **lockTokens(address, amount, unlockTimestamp)** – lock mechanism for investor/founder tokens.
- **projectRegistry** – mapping of projectId → token allocations, mintedAmount, burnedAmount, status.
- **failureOracle** – AI-signal + manual verification oracle to set project status to `failed`.
- **timelock module** – delays for governance burns and admin emergency operations.

### C. Atomicity & Accounting
- Burns should be atomic and accompanied by events containing: projectId, triggerType, amount, initiator.
- All burns are recorded on-chain and mirrored in off-chain accounting for treasury reconciliations.

### D. Buyback-to-Burn Flow
1. Protocol transfers revenue (fiat/USDT) to on-chain vault.
2. Vault swaps stable → RAze token on DEX via oracle-checked price slippage limits.
3. Swap results are burned via `burnTokens()`.
4. Event logged with revenue reference.

---

## Accounting & Treasury Treatment

- **On-chain balance:** Burn reduces totalSupply (ERC-20 burn).
- **Off-chain ledger:** Maintain a reconciliation file showing fiat used for buybacks, DEX trades, fees, and tokens burned.
- **Reporting cadence:** Monthly public burn reports and quarterly deep-dive audits.

---

## Governance & Safety

- **DAO control:** Core burn rate parameters (e.g., failure burn %, unlock burn %) should be modifiable by DAO proposals.
- **Emergency locks:** Admin multisig only for exceptional operations with time-locked execution.
- **Dispute resolution:** Burns tied to anti-abuse must include a dispute process (appeal window, bounty for evidence of false positive).

---

## Communication Strategy

- Publish a **monthly Burn Report** with:
  - Tokens burned this month (by category)
  - Fiat spent on buybacks
  - Net supply change
  - Rationale & notable events
- Real-time burn events visible in the dashboard (project pages show associated burns).
- Educational materials that explain why burns protect investor hedges.

---

## Examples (Quick)

1. **Project Failure:** Project X minted 100,000 RAZE tokens across investors and allocations. On failure, burn 35,000 tokens from the project pool and emit `Burn(projectX, 35000, 'failure')`.

2. **Unlock Event:** Investor's 10,000 tokens unlock; system burns 5% = 500 tokens automatically.

3. **Revenue Buyback:** Platform uses $10,000 revenue; swaps to RAZE at market and burns tokens received.

---

## Implementation Checklist

- [ ] Smart-contract `burnTokens()` implemented & audited
- [ ] Lock/unlock module in place
- [ ] Project registry with clear accounting fields
- [ ] Failure oracle + AI integration tested
- [ ] Buyback vault and DEX swap module with slippage controls
- [ ] Multisig & timelock for governance burns
- [ ] Public burn reporting UI
- [ ] Legal review of buyback operations in target jurisdictions

---

## Appendix: Suggested Solidity Function Signatures (Simplified)

```solidity
function burnTokens(address account, uint256 amount, bytes32 reason) public onlyAuthorized {
    _burn(account, amount);
    emit TokenBurned(account, amount, reason);
}

function lockTokens(address account, uint256 amount, uint256 unlockTimestamp) public onlyAuthorized {
    // transfer to lock registry
}

function markProjectFailed(uint256 projectId) public onlyOracle {
    // set status and trigger failure burn
}
```

---

## Closing Notes

This burn architecture is designed to keep RazeSEED's token supply robust and aligned with platform activity and hedging responsibilities. The parameters are conservative and can be tuned by governance as the platform scales.


