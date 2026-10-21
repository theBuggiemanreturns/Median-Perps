# H-003 — Liquidation fee / bad-debt / insurance waterfall rounding
Status: PARKED
Where: _fullLiquidate L665-723 (calcLiquidationFee → split.badDebt → accrueToInsurance / reimbursePoolFromInsurance); partial liquidation slice path L733-819.
Idea: reward+insurance computed from equity before bad-debt cap; partial-liq slices may each round down fees or leak dust to liquidator; maxBadDebt is caller-chosen (liquidator can choose to skip bad-debt positions → griefing/bad-debt accumulation).
Why parked: math in missing PerpMath/VaultMath/AdlLib. No live value.
