# Meridian Perps (meridiantrade.xyz) — Audit STATUS

**Date:** 2026-10-21 (WAT) · Day 1 of 30-day challenge · Auditor: KIZZITO / theBuggieman pipeline
**Target as briefed:** Pool-based synthetic US-equity perps on Robinhood Chain (chain id 4663), USDG collateral, shared LP `EquityVault` counterparty, up to 200x, fail-closed oracle, pause-but-never-unpause.
**Verdict:** **NO AUDIT PERFORMED ON LIVE VALUE AT RISK — the target as briefed is not deployed on Robinhood Chain mainnet.** This is a *scoping* outcome, not a clean bill of health. See "What's actually on-chain".

---

## What's actually on-chain (verified by RPC, 2026-10-21)

RPC used: `https://rpc.mainnet.chain.robinhood.com` (chainId `0x1237` = 4663) and
`https://rpc.testnet.chain.robinhood.com` (chainId `0xb626` = 46630). Both official per
docs.robinhood.com/chain/connecting.

| Contract (brief address) | Mainnet 4663 | Testnet 46630 |
|---|---|---|
| PerpEngine `0xBa7e…4141` | **no code** (EOA, 0 nonce, 0 bal) | **has code** (47.7 KB), creator `0x9958…C871` |
| EquityVault `0xF8Aa…2148` | **no code** | has code (22.8 KB), ERC-20 "Element Equity LP" (eqLP, 6 dec), 2 holders |
| PerpOrders `0x0A49…6859` | no code | has code (10 KB) |
| OracleRouter `0x0064…6EF4` | no code | has code (5.6 KB) |
| PriceFeed `0xb605…108B` | no code | has code (2.8 KB) |
| USDG `0x537D…C6BF` | no code | has code — **"Mock USDG"** (USDG, 6 dec, mintable mock) |
| FeeDistributor `0xcae4…D5b6` | **has code** (4.5 KB) | no code |
| MeridianStaking `0x9043…aA40` | **has code** (8.7 KB) | no code |
| Relayer `0x4BDd…4bD8` | EOA, active (nonce 186,840) | no code |
| Keeper `0xdC02…34bA` | EOA, active (nonce 34,680) | no code |

**Deployer split:**
- Testnet perp core (USDG, EquityVault, PriceFeed, OracleRouter, PerpEngine, PerpOrders) = CREATE nonces **2–7** of deployer `0x9958F00d2212c1fC8475CED755FCe80BadC5C871`. That deployer has **nonce 0 on mainnet** (never deployed anything on mainnet).
- Mainnet Staking + FeeDistributor = CREATE nonces **3 and 4** of a *different* owner `0x847BC243509D9Bdce6DDbf38577Ba9bae092c4FA`.

**Frontend confirms testnet.** meridiantrade.xyz's Next.js bundle hard-codes `chainId: 46630` and `rpc.testnet.chain.robinhood.com`. The live app trades the **testnet** perp deployment against **Mock USDG**. The docs "Contracts" page lists these testnet addresses under a "chain id 4663" header (doc error / pre-mainnet placeholder).

**Mainnet Staking/FeeDistributor are dormant & detached from any perp engine:**
- Staking `0x9043…aA40`: `owner`=`0x847B…c4FA`, `usdg`=`0x5fc5360D0400a0Fd4f2af552ADD042D716F1d168` (real **Global Dollar USDG**, 6 dec), **stakingToken (`element()`)** = `0xe4f338…7d26` ("Element" / ELEMENT, 18 dec, 1e27 supply — NOT $MRD as the brief/docs say). `totalStaked` ≈ 4.12e26 ELEMENT, but `rewardIndex=0`, `totalDistributed=0`, `totalClaimed=0`, USDG balance **0**. No fees have ever flowed. It is a staking contract with deposits but no reward stream wired to it.
- FeeDistributor `0xcae4…D5b6`: `owner`=`0x847B…c4FA`, `treasury`/`referralPool`=`0x5970…16Ea`, `stakerPool`=the Staking contract, `usdg`=Global Dollar, split `stakerBps=5000`, `treasuryBps=5000`, `referralBps=0`. Holds 0 USDG. Never distributed.
- These two mainnet contracts are **unverified** (bytecode only). Discoverable selectors are stock `Ownable2Step` + a simple single-reward-index staking model. No perp logic, no trader funds, no counterparty vault.

## TVL reality check
- DefiLlama's **"Meridian Perps" ($2.8M)** entry points at `app.meridian.xyz` / `@meridiandotxyz` and its adapter (`projects/meridian-perps/index.js`) counts an **ExchangeGateway (`0xD540…f44A`) + merUSD (`0xad22…f009`, USDe-backed) + ERC-4626 LP vault (`0x24b8…bF9d`)** — a **completely different architecture** from the PerpEngine/EquityVault pool model in the brief. The brief conflated two separate "Meridian" projects. The meridiantrade.xyz pool-perp system has **no DefiLlama TVL entry and no USDG/real value on mainnet**.
- `devIykee`'s own prior recon (github.com/devIykee/security-research) files this exact pool-perp system under codename **"arcus"** and separately tracks `meridian.xyz` — confirming they are distinct.

## Source acquisition
- **No verified source on any explorer.** Testnet Blockscout API (`explorer.testnet.chain.robinhood.com/api/v2/smart-contracts/{addr}`) returns bytecode but `is_verified` is false / "Not found". Mainnet Blockscout (`robinhoodchain.blockscout.com`) and `rh-scan.com` are behind Cloudflare (403 to curl). Sourcify: no match.
- **Reconstructed source exists** in the user's own prior repo: `github.com/devIykee/security-research` → `hunts/robinhood-chain/arcus/PerpEngine.sol` (933 lines, clean hand-reconstructed Solidity, SPDX/pragma 0.8.24). Saved to `target/reconstructed-devIykee/PerpEngine.sol`. **Incomplete** — imports `libs/PerpMath.sol`, `SessionLib`, `VaultMath`, `MarketConfigLib`, `AdlLib`, `interfaces/IEquityVault`, `OracleRouter` that are NOT present in the repo, so it does not compile. `ArcusSettlement.sol` in that folder is an unrelated Permit2 swap router, not the EquityVault.
- Full runtime bytecode saved for all 6 testnet core contracts (`target/bytecode-testnet/`) and the 2 mainnet contracts (`target/bytecode/`). Full decoded ABI (selector→signature via openchain) recorded in `audit-log/primitives.md`.

## Why no PoC fork testing was done
Per the challenge rule "nothing counts as a finding without a working PoC" and "never send to live chain": a PoC must demonstrate **value at risk**. There is **no mainnet deployment of the pool-perp system and no real collateral** (mainnet USDG balance of every perp contract = 0; the only live contracts are dormant staking with 0 reward flow). A testnet-only, Mock-USDG deployment that the project itself has not promoted to mainnet is **out of scope for a bounty-grade finding** — any bug there risks no user funds and there is no disclosure channel / program for it. Forking testnet to exploit Mock USDG proves nothing economically. I did not fabricate a PoC against zero value.

## Observations from reconstructed PerpEngine (NOT findings — unverified, incomplete, no live value)
Recorded in `hypotheses/` as leads to revisit IF/when this deploys to mainnet with real USDG. None are confirmed; the source is a reconstruction and the math libs are missing.
1. `leverage = r.size / margin` uses **integer division** (truncation) before the tier-cap check (PerpEngine L397). Caps compared against a floored leverage — needs the real `size`/`margin` decimals (both 6-dec USDG?) to say if it's exploitable; likely just rounds in protocol's favor.
2. Fee is computed on `size` via `PerpMath.selectFeeBps` with a favorable/base split by OI imbalance — rounding/edge behaviour lives in the missing `PerpMath` lib.
3. Pause/halt semantics: `openPosition` reverts on any `haltMode != None` and `m.paused`; `closePosition`/`liquidate` only revert on `HaltMode.Full`, so **closes & liquidations remain available under partial halt and while a single market is paused** — matches the brief's "closes/cancels/liquidations stay available when paused" claim; need to confirm the stale-mark path (`router.readPrice` on a paused/frozen market) does not let a closer pick a favorable stale price. Lives in missing `OracleRouter`/`PriceFeed`.
4. Bad-debt/insurance accounting in `_fullLiquidate` splits via `PerpMath.calcLiquidationFee(equity, liqFeeBps)` then `vault.accrueToInsurance` — the insurance-vs-pool loss waterfall is in the missing `VaultMath`/`AdlLib`.

All four require the missing libs + a mainnet deployment with real value to pursue. Parked, not killed.

## Blockers (stop-after-2-loops rule honored)
1. **No mainnet deployment of the briefed system** → nothing with value at risk to audit. (Confirmed, not a transient blocker.)
2. **No verified source** anywhere; only an incomplete reconstruction. (2 routes tried: explorers [Cloudflare/unverified], Sourcify [no match], GitHub [found partial reconstruction], app bundle [testnet-only addresses]. Stopped per rule.)

## Recommendation to user
- The meridiantrade.xyz "pool perp" is **testnet-only today**. Set a watch for its mainnet deployment (deployer `0x9958…C871` getting a mainnet nonce, or the docs flipping to a mainnet RPC/verified contracts). When it goes live with real USDG, the reconstructed PerpEngine + the 4 parked leads are a running start.
- If the intended Day-1 target was the **$2.8M meridian.xyz** protocol (ExchangeGateway/merUSD/ERC-4626 LP vault), that is a different codebase and should be scoped separately — say the word and I'll pivot to it tomorrow.
- Honest Day-1 result: **audited the deployment surface, found the briefed target is not live on mainnet; no confirmed finding, no value at risk.**
