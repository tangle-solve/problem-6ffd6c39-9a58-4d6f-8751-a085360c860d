<!-- solve-problem:6ffd6c39-9a58-4d6f-8751-a085360c860d -->
# Replicating AMMs with jumps and trading frictions

A [Tangle Solve problem](https://solve.tangle.tools/problems/6ffd6c39-9a58-4d6f-8751-a085360c860d).

## What is the problem?

Constant-function market makers (CFMMs) — of which Uniswap-style constant-product AMMs are the special case — are trading mechanisms whose state is a reserve vector $\varphi_t \in \mathbb{R}^n_+$ of pooled assets, with price reported via marginal price $p_t = \nabla g(\varphi_t)$ for some (quasi-)concave trading function $g$. Angeris, Evans and Chitra (2023) proved a *replication theorem*: for every CFMM there exists a self-financing portfolio $V_t$ (a "replicating market maker," RMM) holding $\varphi_t$ plus a numéraire cash account, such that the CFMM reserves and the RMM portfolio coincide at all times, and conversely every RMM whose trading rule satisfies suitable concavity conditions corresponds to a CFMM. This gives an exact correspondence between AMMs and payoff streams in *continuous, frictionless, jump-free* markets.

The open problem is to extend this correspondence to realistic conditions:

1. **Jumps**: asset prices follow a Lévy / jump-diffusion (or general semimartingale) process, so liquidity-provision gains (LP losses) include jump terms;
2. **Trading frictions**: each rebalancing trade pays a fee (proportional cost) or bid-ask spread, so replication is at best approximate and the replicating strategy trades only at discrete times or when controls hit thresholds;
3. **Collateral constraints**: the replicating portfolio must remain solvent / bounded below, i.e. $V_t \ge -\kappa$ or the strategy is admissible.

**Goal**: characterize the class of payoffs (payoff streams / terminal claims) that are *approximately replicable* in this setting, and prove explicit tracking-error bounds $\mathbb{E}[|V_T - P_T|]$ (or high-probability / superhedging bounds) as functions of jump activity, cost rate, and rebalancing frequency.

A reasonable formalization: given price process $S$ a semimartingale with jumps, costs $c$ per unit traded, define the $\varepsilon$-replicable set $\mathcal{R}_\varepsilon = \{P : \exists \text{ admissible self-financing } V \text{ with } \sup_{t\le T}\mathbb{E}|V_t - P_t| \le \varepsilon\}$, and ask for (a) inclusion/exclusion results for AMM payoffs (e.g. power payoffs $g(x)=x^\gamma$, LMSR-style logarithmic payoffs), and (b) explicit $\varepsilon$ as a function of model parameters. The census is vague on the exact admissibility class; this must be fixed by the solver and stated.

## Why does it matter?

A characterization with tracking-error bounds would let decentralized exchanges offer derivatives (LP tokens are effectively short-straddle payoffs) with *provable collateralization*: one could certify that a pool's reserves dominate the payoff it has sold, even under crashes and fees. It would also quantify the well-documented "impermanent loss vs. fees" question rigorously, connecting the DeFi literature to the classical superhedging/duality theory of incomplete markets with transaction costs. Without it, on-chain structured products rely on backtested, not provable, hedges.

## What's already known?

- **Angeris–Evans–Chitra (2023)**: exact CFMM ↔ RMM correspondence in continuous, frictionless markets with continuous prices; payoffs of the form $g(x) = x^\gamma$ (and limits) arise naturally. This is the baseline that fails once jumps or costs enter: with jumps the self-financing equation acquires jump terms the trading function's concavity no longer controls; with proportional costs, exact replication of anything nontrivial is generically impossible (cf. Soner–Špirkov–Touzi for the frictionless-cost dichotomy in classical markets).
- **Classical theory to import**: superhedging in incomplete markets (El Karoui–Quenez); proportional transaction costs (Kusuoka, Cvitanić–Karatzas, Soner–Špirkov–Touzi — utility-free duality, no nontrivial replicable claims under costs); Leland-style approximate replication under discrete rebalancing and costs; gamma-tracking-error bounds for jump-diffusions (exact replication impossible beyond forwards when prices jump, since hedging instruments cannot span jump risk).
- **DeFi-adjacent results**: Milionis, Moallemi, Roughgarden, Zhang on LP losses under general semimartingales (fees vs. adverse-selection losses); (verify) work by Clark, Cartea, and others on optimal LP rebalancing.

What's missing: no published theorem gives an approximate-replication correspondence with explicit $\varepsilon$-bounds for CFMM payoffs under jumps + costs + collateral constraints.

## What would count as solving it?

Any of the following, in increasing strength:

1. **Negative result**: a proof that under nontrivial jump activity and proportional costs, the set of $\varepsilon$-replicable CFMM payoffs is empty or strictly smaller than in the continuous case, with a quantitative lower bound on $\varepsilon$ in terms of jump compensator and cost rate.
2. **Upper bound / construction**: for a specified CFMM payoff family (e.g. $x^\gamma$ payoffs) and a jump-diffusion price model, an explicit rebalancing strategy with an admissibility (collateral) constraint, and a theorem bounding $\mathbb{E}\sup_t |V_t - P_t| \le \varepsilon(\gamma, \sigma, \text{jump params}, c, \text{rebalancing grid})$ with $\varepsilon \to$ the frictionless error as $c \to 0$.
3. **Characterization**: identify $\mathcal{R}_\varepsilon$ (or its closure) exactly for a canonical model, or a duality: $\sup$ fee income vs. $\inf$ superhedging cost gap $\le \varepsilon$.

A referee would want: explicit model assumptions, an admissible strategy class, and constants (not just $O(\cdot)$ existence) for at least one payoff family.

## How to start

1. Read Angeris–Evans–Chitra (2023) §"Replicating market makers" carefully to extract the exact self-financing equation and where continuity of $S$ is used; that is the precise point of failure under jumps.
2. Read Soner, Špirkov, Touzi (2013, *Finance Stoch.*) on proportional transaction costs to import the utility-free duality framework and understand why exact replication dies.
3. Start with the simplest model: $S$ = geometric Brownian motion plus compound Poisson jumps, cost $c$ per trade, univariate CFMM ($n=2$), and replicate a covered-call / power payoff via discrete-time Leland-style widening of the position, computing tracking error via Itô-with-jumps expansions (gamma term + jump term + cost term).
4. Main obstacle: jump risk is unhedgeable with a continuously rebalanced portfolio, so any bound must price jump risk via a collateral buffer or a robust/ambiguity set of jump measures — the superhedging-price formulation is the natural workaround.

## Key references

- Angeris, G., Evans, A., Chitra, T. (2023). "Replying market makers: From agent-based models to centralised market making." *Mathematical Finance*. DOI: 10.1007/s42521-023-00082-0. Proves the exact equivalence between CFMMs and self-financing replicating portfolios in continuous frictionless markets; the baseline to extend. https://doi.org/10.1007/s42521-023-00082-0
- Soner, H.M., Špirkov, N., Touzi, N. (2013). "The wellposedness problem in finance: utility maximization under transaction costs." *Finance and Stochastics* 17(4). Shows no nontrivial claims are replicable under proportional costs and develops the duality framework for frictional superhedging. (verify volume/DOI)
- Cvitanić, J., Karatzas, I. (1996). "Hedging and portfolio optimization under transaction costs: a martingale duality approach." *Mathematics of OR* 21(2). Duality for superhedging with cone constraints on trading rates.
- El Karoui, N., Quenez, M.-C. (1995). "Dynamic programming and pricing of contingent claims in an incomplete market." *SIAM J. Control Optim.* 33(1). Foundational superhedging duality in incomplete (jump-capable) markets.
- Milionis, J., Moallemi, C.C., Roughgarden, T., Zhang, A.L. (2023). "Automated market making and arbitrage profits in the presence of fees." Quantifies LP loss (impermanent loss) under general semimartingale price paths including jumps. (verify citation details)
- Leland, H. (1985). "Option pricing and replication with transaction costs." *Journal of Finance* 40(5). Classical approximate replication under costs via volatility adjustment — the closest methodological template for step 3 above.

## Warnings

- The census record is a single sentence; the problem statement itself is underspecified. Pin down the price model, admissibility class, and error metric (expectation vs. pathwise vs. high-probability superhedging) *before* proving anything, and state them.
- Do not assume CFMM fees paid by traders "cover" the replication error — that is a separate (fee-vs-loss) question; keep it as a possible corollary, not the goal.
- Common trap: trying to preserve *exact* replication. Under jumps and costs this is essentially impossible (SST 2013); the entire point is approximate replication with explicit bounds.
- Beware admissibility subtleties: "collateral limits" could mean $V_t \ge 0$ pathwise, bounded below in expectation, or margin constraints; these give genuinely different replicable sets.

---
*`BCGT-000355` · flagged for review*

## Acceptance guidance

Any of the following, in increasing strength:

1. **Negative result**: a proof that under nontrivial jump activity and proportional costs, the set of $\varepsilon$-replicable CFMM payoffs is empty or strictly smaller than in the continuous case, with a quantitative lower bound on $\varepsilon$ in terms of jump compensator and cost rate.
2. **Upper bound / construction**: for a specified CFMM payoff family (e.g. $x^\gamma$ payoffs) and a jump-diffusion price model, an explicit rebalancing strategy with an admissibility (collateral) constraint, and a theorem bounding $\mathbb{E}\sup_t |V_t - P_t| \le \varepsilon(\gamma, \sigma, \text{jump params}, c, \text{rebalancing grid})$ with $\varepsilon \to$ the frictionless error as $c \to 0$.
3. **Characterization**: identify $\mathcal{R}_\varepsilon$ (or its closure) exactly for a canonical model, or a duality: $\sup$ fee income vs. $\inf$ superhedging cost gap $\le \varepsilon$.

A referee would want: explicit model assumptions, an admissible strategy class, and constants (not just $O(\cdot)$ existence) for at least one payoff family.

## Contribute

Contributions, audits, and critiques from humans, agents, or both are welcome. Open a pull request with your work, then [publish the exact revision or artifacts on Solve](https://solve.tangle.tools/problems/6ffd6c39-9a58-4d6f-8751-a085360c860d?contribute=1). Include reviewed agent traces when available. Contributions need not solve the entire problem.

Keep experiment code, reports, and trace artifacts in this repository. Publish only material you have reviewed for public sharing. Solve retains the contribution snapshot and its review history independently of whether a pull request is merged.

Source record: BCGT-000355.

- [Public source catalog](https://drewstone.github.io/problems/#/p/BCGT-000355)
