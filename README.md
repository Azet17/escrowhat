# EscrowHat — Natural-Language Escrow for Agentic Commerce

**A reusable Intelligent Contract primitive where deal terms are written in plain language, evaluated by AI validator jury at release time, and protected by a two-sided deadlock breaker.**

> Builder contribution · GenLayer Intelligent Contracts category

---

## The Problem

Agent-to-agent commerce (x402/ERC-8004) dies on one question: **who decides the deliverable is acceptable?**

- Buyer pays Agent B for *"a working scraper for site X"* — what counts as "working"?
- Freelance escrow: *"a logo I'm happy with"* — subjective acceptance
- Data purchases: *"the dataset must be fresh and complete"* — needs reading the deliverable

Solidity escrows force binary outcomes with hardcoded checks — or freeze forever when buyer and seller disagree. **Subjective satisfaction of natural-language terms is exactly what GenLayer's adjudication layer solves.**

## What EscrowHat Does

1. **Open escrow** with terms written in plain language + a live deliverable URL + deadline
2. **Happy path**: buyer `release()` or seller `refund()` — no consensus needed
3. **Deadlock**: nobody budges → after deadline, anyone calls `force_resolve()`
4. **Jury breaks the deadlock**: the deliverable itself is fetched LIVE (`gl.nondet.web.render`), evaluated against the written terms, and validators agree via `gl.eq_principle.strict_eq` — verdict `RELEASE` or `REFUND` with full rationale stored on-chain

## What Makes It Different (vs TrustOracle / TrustLeaf)

| | TrustLeaf / TruthOracle | **EscrowHat** |
|---|---|---|
| Judges | FACTS (did X happen?) | **SATISFACTION** (is X acceptable per written terms?) |
| Input | URLs of evidence | The deliverable itself, read live |
| Outcome | VERIFIED/REFUTED | RELEASE/REFUND + deadlock breaker |
| Game theory | challenge flips verdict | two-sided standoff has guaranteed resolution |

## Why Impossible on Ethereum

- Reading the deliverable at resolution time (live web render)
- Judging "does this satisfy the written terms?" — subjective, needs jury
- Storing the terms and rationale in natural language, on-chain

## Consensus & State Design

- **State**: `TreeMap[str, str]` escrows + `bigint` counter — typed per GenVM rules
- **Consensus**: `force_resolve` runs the fetch+judge inside `strict_eq`; deterministic given fetched content
- **Methods**: `open_escrow(terms, deliverable_url, deadline_ts)`, `submit_deliverable`, `release`, `refund`, `force_resolve`, views `get_escrow`, `total_escrows`

## License

MIT
