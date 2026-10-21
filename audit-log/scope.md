# Scope — Meridian Perps (meridiantrade.xyz)

- Briefed chain: Robinhood Chain mainnet, chain id 4663. RPC `https://rpc.mainnet.chain.robinhood.com` (official, rate-limited).
- Testnet: chain id 46630, RPC `https://rpc.testnet.chain.robinhood.com`, explorer `explorer.testnet.chain.robinhood.com` (Blockscout, API open).
- Mainnet explorer `robinhoodchain.blockscout.com` / `rh-scan.com`: Cloudflare-blocked to curl.
- NOT in scope / different project: meridian.xyz (ex-Ethereal; ExchangeGateway/merUSD/ERC-4626 vault, Guardian+ChainSecurity audited). DefiLlama "Meridian Perps" $2.8M = meridian.xyz, not this.
- No public audit, no published security contact, no bug-bounty program for meridiantrade.xyz.
- Rules: fork-only PoC, never broadcast to live chain, no contact with project, no push of confirmed findings to the public repo.
- RESULT: briefed perp core is testnet-only (Mock USDG). Mainnet has only dormant Staking + FeeDistributor (ELEMENT staking token, 0 reward flow). No value at risk → no bounty-grade audit possible today.
