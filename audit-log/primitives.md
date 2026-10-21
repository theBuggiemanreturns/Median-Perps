# Primitives ledger — decoded ABI (testnet bytecode, selectors resolved via openchain)

Source: runtime bytecode `target/bytecode-testnet/*.hex`. Names from openchain signature DB (best match). Unresolved: 0x12aa1a0c, 0xf2d7e042.

## PerpEngine (testnet 0xBa7e…4141)
Trader: openPosition(bytes32,uint8,uint256,uint256,uint256) · closePosition(bytes32,uint256,uint256) · liquidate(bytes32,uint256)
Orders hooks (onlyOrders): openFromOrder(address,bytes32,uint8,uint256,uint256,uint256) · closeFromOrder(bytes32,uint256,uint256) · escrowForOrder(address,uint256) · refundForOrder(address,bytes32,uint8,uint256)
Funding/ADL: updateFunding(bytes32) · applyQueueHaircut(bytes32,uint8)
Admin: listMarket(bytes32,tuple) · updateMarketConfig(bytes32,tuple) · setProtocolCfg(tuple) · setMarketPaused(bytes32,bool) · setTradingHalt(uint8) · setPauseAuthority(address) · setOrders(address) · setGateway(address)
Views: positions(bytes32) · getMarket(bytes32) · marketStatus(bytes32) · walletMarketGross(address,bytes32) · openPositionCount(address) · positionNonce(address) · haltMode() · protocolCfg() · getProtocolCfg() · pauseAuthority() · orders() · vault() · router() · gateway() · CLOSED_BY_{ORDER,LIQUIDATION,OWNER}()

## EquityVault (testnet 0xF8Aa…2148, ERC-20 "Element Equity LP")
LP: deposit(uint256,uint256) · withdraw(uint256,uint256) · queue(bytes32,uint256) · voidQueueEntry(bytes32,uint256) · harvest(bytes32,uint256)
Engine-only settlement: releaseAndSettle(...) · releaseAndEnqueue(...) · creditCollateral(uint256) · chargeEscrowedFee(uint256) · accrueFee(uint256,bool) · accrueToInsurance(uint256) · reimburseLiquidator(address,uint256) · reimbursePoolFromInsurance(uint256) · updateReserve(bytes32,uint8,int256) · haircutQueueTail(bytes32,uint16,uint8,uint256)
Admin: setEngine · setPaused(bool) · setAutoPayoutsFrozen(bool) · setFees(uint16,uint16) · setLpFeeBps(uint16) · setMinFreeLiquidityBps(uint16) · sweepFees(address) · withdrawInsurance(address,uint256)
Views: totalUsdg · lpPrincipal · freeLiquidity · totalReserved · escrowedCollateral · insuranceBalance · cumulativeFees · queueTotalOwed · navSolvent · marketRisk(bytes32) · adlState(bytes32) · sideBucket · GENESIS_MIN_DEPOSIT · depositFeeBps · withdrawFeeBps · lpFeeBps · minFreeLiquidityBps · autoPayoutsFrozen

## PerpOrders: createOrder(uint8,bytes32,uint8,uint256,uint256,uint256,uint256,uint256,bytes32) · executeOrder(bytes32)[keeper] · cancelOrder(bytes32) · setKeeper · orders(bytes32) · orderNonce(address)
## OracleRouter: readPrice(bytes32) · configureMarket(bytes32,address,uint16,uint16) · setMarkStaleness(uint64) · markStalenessSec · CL_STALENESS · setRthWindow(uint16,uint16) · rthOpenMinutesUtc · rthCloseMinutesUtc · marketOracles(bytes32) · priceFeed
## PriceFeed: pushPrices(bytes32[],uint64[])[relayer] · prices(bytes32) · setRelayer · relayer

## Mainnet-only (4663)
- MeridianStaking 0x9043…aA40: stake(uint256,uint8) · unstake() · claimReward() · updateRewardIndex() · pendingReward(address) · positions(address) · stakedBalanceOf(address) · setTiers(uint64[3],uint256[3]) · tierDurations/tierMultipliers · totalStaked · totalWeightedStake · rewardIndex · lastUpdate · totalDistributed · totalClaimed · element() · usdg()
- FeeDistributor 0xcae4…D5b6: distribute() · setSplit(uint16,uint16,uint16) · setStakerPool · setReferralPool · setTreasury · stakerBps · treasuryBps · referralBps · stakerPool · referralPool · treasury · usdg()

## Representations / units (to verify when live)
- USDG 6 decimals; eqLP 6 decimals; ELEMENT 18 decimals. PriceFeed pushes uint64 prices (decimals unknown — in missing lib).
