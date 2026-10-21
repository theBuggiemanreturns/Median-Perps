# Meridian Perps (meridiantrade.xyz) — audit workspace

Day 1 of a 30-day Robinhood Chain / Arc bug-hunting challenge (theBuggieman pipeline, hunt mode).

**Result:** the briefed pool-based perp system (PerpEngine / EquityVault / PerpOrders / OracleRouter / PriceFeed, USDG collateral) is **deployed only on Robinhood Chain testnet (46630) with Mock USDG**. Robinhood Chain mainnet (4663) has only a dormant Staking + FeeDistributor pair (ELEMENT staking token, zero rewards ever distributed). No verified source exists; a partial reconstruction of PerpEngine is in `target/reconstructed-devIykee/`. No value at risk, no confirmed finding.

Start at `audit-log/AGENT_HANDOFF.md` → `audit-log/STATUS.md`.

Layout: `audit-log/` (status, scope, primitives/ABI ledger, hypotheses, killed, crossref) · `target/` (runtime bytecode for all contracts + reconstructed PerpEngine.sol) · `poc/` (none — no live target to fork-test).
