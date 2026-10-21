# H-004 — Mainnet MeridianStaking reward math with zero reward flow
Status: KILLED for now (see killed.md)
Contract: mainnet 0x9043…aA40 (unverified). rewardIndex=0, totalDistributed=0, USDG bal=0, totalStaked≈4.12e26 ELEMENT.
Idea: first-distribution reward-index jump / division by totalWeightedStake; stake-before-distribute sniping.
Why killed for now: no USDG has ever been distributed and FeeDistributor holds 0 — nothing to steal; and no source. Re-open if FeeDistributor starts receiving USDG.
