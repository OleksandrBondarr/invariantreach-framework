# Case Study: EXP5 — UUPS tx.origin Upgrade Authorization

## Contract Type
UUPS-style upgradeable proxy with tx.origin-based upgrade authorization.

---

# STRICT MODE ANALYSIS (Closed-Set)

## Root-Cause Clusters

### Cluster 1 — tx.origin-based Upgrade Authorization
_authorizeUpgrade() validates:
require(tx.origin == owner)

This allows upgrade authorization to succeed even when the owner interacts through an intermediary contract.

Impact: Upgrade control can be phished via relay contracts.

---

### Cluster 2 — No Implementation Validation
_setImplementation(newImpl) writes directly to EIP-1967 slot without:

- extcodesize check
- zero-address validation
- allowlist enforcement

Impact: Proxy can be bricked or pointed to arbitrary logic.

---

### Cluster 3 — upgradeToAndCall Delegatecall Execution
After setting implementation, upgradeToAndCall performs:

newImpl.delegatecall(data)

Executed in proxy storage context.

Impact: Immediate arbitrary state mutation is possible after upgrade.

---

# Invariant Violations

| Invariant | Reachability | Outcome | Severity |
|-----------|--------------|---------|----------|
| Upgrade must require msg.sender == owner | A | Loss of control | Critical |
| Implementation slot must not accept address(0) | A | Permanent DoS | High |
| upgradeToAndCall must not allow arbitrary delegatecall | A | Theft / Freeze | Critical |
| Withdraw protected by msg.sender | U | None | Secure |

---

# STRICT MODE VERDICT

Ownership takeover: Not R (requires owner-originated tx)  
Upgrade hijack: A-class risk (phishing)  
Proxy bricking: A-class risk  
Unrestricted drain: Conditional on malicious implementation  

Strongest provable flaw:
Authorization uses tx.origin instead of msg.sender.
