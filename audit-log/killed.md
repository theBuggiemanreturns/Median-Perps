# Killed / not-applicable leads

| ID | Lead | Verdict | Evidence |
|---|---|---|---|
| K-001 | Audit pool-perp core on mainnet 4663 | NOT APPLICABLE — no code at any of the 6 core addresses on mainnet | eth_getCode = 0x for PerpEngine/EquityVault/PerpOrders/OracleRouter/PriceFeed/USDG on rpc.mainnet.chain.robinhood.com, 2026-10-21 |
| K-002 | LP first-depositor / share inflation on EquityVault | NOT APPLICABLE on mainnet (vault not deployed). On testnet: eqLP supply 50,000e6, 2 holders, Mock USDG, `GENESIS_MIN_DEPOSIT` guard present in ABI — no value at risk | testnet Blockscout token info |
| K-003 | $2.83M TVL at risk | WRONG ATTRIBUTION — that TVL is meridian.xyz (ExchangeGateway/merUSD/ERC-4626), a different protocol | DefiLlama adapter projects/meridian-perps/index.js |
| K-004 | Mainnet Staking reward-index exploit (H-004) | KILLED for now — 0 rewards ever distributed, 0 USDG held | cast calls: rewardIndex/totalDistributed/totalClaimed = 0, USDG.balanceOf = 0 |
| K-005 | Approve-to-empty-address risk: docs tell users to approve USDG to PerpEngine 0xBa7e on "4663", which has no code on mainnet and whose deployer 0x9958 has nonce 0 on mainnet (could later CREATE arbitrary code at that exact address via nonce 6) | INFORMATIONAL / not a contract bug — trust/doc issue. Real mainnet USDG is 0x5fc5…d168, not the doc's 0x537D (which is empty on mainnet), so a doc-following approval targets a non-existent token on mainnet → no live allowance created. Worth a doc-accuracy note to the team, not a bounty | RPC + CREATE-address derivation |
