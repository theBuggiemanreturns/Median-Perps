# Cross-reference (20-family pass) — status

NOT RUN against live code: the briefed system has no mainnet deployment and no complete source. Running a 20-family crossref on a testnet Mock-USDG deployment would produce unverifiable, valueless leads.

Families pre-mapped to the reconstructed PerpEngine for when it goes live:
- Oracle staleness / frozen market → H-002 (OracleRouter.readPrice, markStalenessSec, RTH window)
- Rounding / precision (6-dec USDG) → H-001 (leverage int-div), fee calc in PerpMath
- Liquidation / bad debt / insurance → H-003
- LP share pricing around unrealized PnL → EquityVault deposit/withdraw/queue + navSolvent/lpPrincipal/totalReserved (vault source NOT reconstructed)
- Reward-index math → H-004 (mainnet Staking)
- Access control → keeper (executeOrder), relayer (pushPrices), onlyOrders hooks, pauseAuthority (can pause, not unpause) — ABI-level map in primitives.md
- Reentrancy → settlement paths releaseAndSettle/releaseAndEnqueue (vault) — needs source
