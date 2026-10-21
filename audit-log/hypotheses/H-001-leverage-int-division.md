# H-001 — Leverage computed with integer division before tier cap
Status: PARKED (no live deployment; source reconstruction incomplete)
Where: reconstructed PerpEngine._openKernel L396-405 — `uint256 leverage = r.size / margin;` then compared to tier/market caps.
Idea: truncation floors leverage, so a position at e.g. 200.9x reads as 200x and passes a 200x cap. Combined with `initialMarginBps` check (L407) which is a separate, non-truncated guard.
Why parked: the separate initialMargin check likely bounds real leverage; exploitability depends on initialMarginBps vs maxLeverage config and on size/margin units (missing libs). Needs mainnet deployment + full source to test.
