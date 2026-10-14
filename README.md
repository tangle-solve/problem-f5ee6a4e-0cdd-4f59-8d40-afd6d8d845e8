<!-- solve-problem:f5ee6a4e-0cdd-4f59-8d40-afd6d8d845e8 -->
# Quantitative incentive-relaxation frontier

A [Tangle Solve problem](https://solve.tangle.tools/problems/f5ee6a4e-0cdd-4f59-8d40-afd6d8d845e8).

## What is the problem?

Transaction-fee mechanism (TFM) design asks how a blockchain should select transactions from user-submitted bids and set payments, so that users and the block producer (miner/validator) have no incentive to deviate. The standard model: each user i submits a bid b_i for inclusion; the mechanism chooses an allocation x(b) (which transactions are included, subject to a block-size constraint) and payments p(b); utility of user i is v_i·x_i − p_i; the miner receives the payment revenue (possibly with a burn) R(b). The base property is *incentive compatibility* (IC, aka SCP — strict user strategyproofness combined with miner strategyproofness against fake or withheld bids). A stronger property, *c-SCP* or collusion-resilience, requires that no coalition of up to c users together with the miner can jointly deviate (via misreported bids or fake transactions) to increase their combined surplus.

This problem asks for the **quantitative frontier under approximate incentives**: suppose deviations are only "profitable" if they yield more than an explicit tolerance (an ε-, α-, or multiplicative-factor-relaxed notion of IC, user-IC, miner-IC, and c-SCP). Characterize, as a function of the tolerance parameters:

1. The maximum achievable **welfare** (sum of true values of included transactions), and
2. The maximum achievable ** miner revenue** (unburned payments),

over all mechanisms satisfying the relaxed incentive constraints. The goal is matching upper and lower bounds — a tight frontier, not just asymptotics.

Formalization choice to note: "bounded deviation gains by explicit approximation tolerances" admits additive (ε-IC), multiplicative (1+ε), and per-agent-vs-coalition parameterizations; the solver should fix one (additive ε per deviating agent is the most standard) and state it. An alternative reading treats the relaxation as *approximate* c-SCP à la Chung–Roughgarden–Shi; both readings are legitimate and should ideally be related.

## Why does it matter?

Exact collusion-resilience in TFMs is essentially impossible beyond restricted settings — e.g., second-price-style mechanisms burn revenue to prevent miner–bidder collusion, and even then fixed-descent variants fail. Knowing exactly how much welfare/revenue must be sacrificed for each unit of slack in the incentive constraints converts impossibility theorems into engineering budgets: protocol designers can pick a point on the frontier. This matters directly for Ethereum's EIP-1559-style designs and any TFM where 100% collusion-resistance is unaffordable, and it parallels the classical approximate-strategyproofness literature in auction/resource-allocation theory.

## What's already known?

- **Base results (verify details in Chung–Roughgarden–Shi 2024):** In the strict (exact) regime, mechanisms like the "burning second-price auction" achieve user-IC + miner-IC + 2-SCP at the cost of burning all revenue above a reserve; the "fixed descent" mechanism achieves c-SCP for all c under a transacted-fee assumption. Roughgarden's earlier "transaction fee mechanism design" framework (EC'20/2021) established that (user-IC + miner-IC + c-SCP for all c) essentially forces welfare losses or burning.
- **Gafni (2026):** proves that 2-SCP and c-SCP coincide under consistent tie-breaking — deterring the smallest collusion deters all. This is the key structural input: the frontier for coalitions of size ≥2 reduces to the 2-coalition case, so the quantitative problem largely collapses to parameterizing a two-agent deviation.
- **Adjacent literature:** approximate strategyproofness in assignment/matching (e.g., ε-strategyproof exchange mechanisms by Shapley–Scarf manipulability bounds; Erdil's work on approximate IC in school choice) provides the definitional toolkit but not blockchain-specific bounds.
- **Gap:** No tight quantitative trade-off curves between tolerance, welfare, and revenue for TFMs exist; known results are qualitative (possible vs. impossible), not frontier-shaped.

## What would count as solving it?

1. **Lower bound:** A family of mechanisms M_ε satisfying ε-relaxed user-IC, miner-IC, and ε-relaxed 2-SCP (hence c-SCP by Gafni's equivalence, if applicable), with provable welfare ≥ W(ε) and miner revenue ≥ R(ε).
2. **Upper bound:** An impossibility result showing no ε-relaxed mechanism achieves welfare > W*(ε) or revenue > R*(ε), matching (1) up to constants or exactly.
3. A tight frontier statement: characterization of the achievable (welfare, revenue, tolerance) region, or a clean closed-form trade-off (e.g., "welfare loss must scale as Θ(f(ε))").
Partial results (matching bounds for one objective, one relaxation type, single-parameter bid model) would also be significant.

## How to start

1. **Read Roughgarden's TFM framework first** (the "Transaction Fee Mechanism Design" paper, roughly 2020–21; see (verify) for the exact arXiv ID) — Sections defining SCP, side-contract-proofness, and the burning-second-price analysis. Then Chung–Roughgarden–Shi arXiv:2402.09321 for the collusion-resilience hierarchy.
2. **Pin down the relaxation:** define ε-IC per agent (deviation gain > ε counts as a violation). Decide whether fake bids cost the coalition the payment or nothing; this changes hardness.
3. **Natural approach:** analyze the burning second-price auction under ε-relaxation — compute exactly how much burn can be replaced by miner revenue once ε slack exists; this plausibly gives one frontier endpoint. Use Gafni's 2-SCP ≡ c-SCP reduction to handle coalitions.
4. **Main obstacle:** upper-bound arguments in the exact regime use a single profitable deviation; with ε slack, deviations must be amplified (replicated across many blocks/bidders) to cross the tolerance, requiring multi-instance or lottery-style constructions that standard single-shot proofs lack.

## Key references

- **Chung, H.-C., Roughgarden, T., Shi, E. (2024). "Collusion-Resilience in Transaction Fee Mechanism Design." arXiv:2402.09321.** Studies collusion-resilience in TFMs, relating c-SCP strength to mechanism design constraints like fee burning and fixed-descent rules. https://arxiv.org/abs/2402.09321
- **Gafni (2026). "Deterring A Small Collusion is All You Need." DOI:10.1145/3774904.3792121.** Proves that 2-SCP and c-SCP are equivalent under consistent tie-breaking, so deterring 2-agent miner–user coalitions suffices. https://doi.org/10.1145/3774904.3792121
- **Roughgarden, T. "Transaction Fee Mechanism Design." (EC 2020 / JACM; verify exact venue and arXiv ID).** Foundational paper defining the user/miner/coalition incentive-compatibility framework (SCP, side contracts) for TFMs and analyzing burning second-price auctions. (verify)
- **Erdil, A., and the approximate-strategyproofness literature (e.g., on matching markets).** Provides standard definitions and welfare-loss bounds for approximate incentive compatibility that can be ported to the TFM setting. (verify specific papers)

## Warnings

- The relaxation definition is genuinely ambiguous in the source (additive vs. multiplicative vs. per-coalition); fix yours explicitly and note results may not transfer across definitions.
- Fake-bid cost model matters: whether the miner must pay for fake transactions it creates changes which deviations are profitable — state your assumption.
- Gafni's equivalence is conditional on consistent tie-breaking; randomization may break the reduction, so don't blindly apply it to randomized mechanisms.
- Don't re-derive the exact-regime impossibilities; they are the ε = 0 endpoints of your frontier, not the contribution.
- The 2026 citation and some bibliographic details are from a secondary census record and were only partially verifiable; confirm before citing.

---
*`BCGT-000278` · flagged for review*

## Acceptance guidance

1. **Lower bound:** A family of mechanisms M_ε satisfying ε-relaxed user-IC, miner-IC, and ε-relaxed 2-SCP (hence c-SCP by Gafni's equivalence, if applicable), with provable welfare ≥ W(ε) and miner revenue ≥ R(ε).
2. **Upper bound:** An impossibility result showing no ε-relaxed mechanism achieves welfare > W*(ε) or revenue > R*(ε), matching (1) up to constants or exactly.
3. A tight frontier statement: characterization of the achievable (welfare, revenue, tolerance) region, or a clean closed-form trade-off (e.g., "welfare loss must scale as Θ(f(ε))").
Partial results (matching bounds for one objective, one relaxation type, single-parameter bid model) would also be significant.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/f5ee6a4e-0cdd-4f59-8d40-afd6d8d845e8?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000278.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000278)
