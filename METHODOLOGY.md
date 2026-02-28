# Methodology v1.0

## 1. Invariant-Based Review
Every contract is analyzed through architectural invariants and reachability classification:
- R = reachable by external attacker
- A = requires privileged/owner action
- U = unreachable under provided semantics

## 2. Dual-Mode Evaluation

### Strict Mode
Closed-set reasoning. Only code and explicit semantics allowed.

### Practical Mode
Production-aware reasoning. Evaluates real-world exploit feasibility.

## 3. Meta-Judge Layer
- Filters hallucinations
- Detects assumption creep
- Enforces reasoning discipline
