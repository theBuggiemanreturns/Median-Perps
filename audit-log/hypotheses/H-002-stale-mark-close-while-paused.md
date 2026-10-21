# H-002 — Close/liquidate at stale mark while market paused or partially halted
Status: PARKED
Where: closePosition/liquidate only revert on HaltMode.Full (L493, L622); openPosition reverts on any halt or m.paused. Both closes read `router.readPrice(marketId)`.
Idea: if a market is paused because its feed is frozen, but readPrice still returns the last mark within markStalenessSec (or RTH/off-hours logic relaxes staleness), a trader who knows the true price moved can close at the stale favorable mark against the LP vault.
Why parked: depends on OracleRouter.readPrice staleness logic (fail-closed claimed) — OracleRouter source not reconstructed. Testnet-only, Mock USDG. Revisit with fork once on mainnet.
