# Math / Economic Modelling

WETH | wBTC | wstETH, rETH, sfrxETH - Math around CCR and MCR, show spikes and volatility, "historically safe CCR and MCR" based on different times

no PSM for Ethos, but when we have the stablecoin trove it would be USDC

CCR 105 / 110

-------

Given the above, a 105 MCR is already fairly risky in specific edge cases.

-------

## Feeds 

The following analysis is on the Deviation Threshold of the Price Feeds usable for LSTs and wBTC and WETH

It should be noted that LSTs will, in general not change that drastically in their value, barring some crazy high volatility (e.g. MEV donations, or exploits causing Priority Fees to be paid to the LST exclusively)

## frxETH - WARNING - No Feed

https://data.chain.link/optimism/mainnet/crypto-eth/sfrxeth-frxeth-exchange-rate

86400 | .5%

No support

This + Slippage + Eth = Scary AF
+
Delay of a day and .5% -> sfrxETH has linear growth -> So worst case is non-trivial

Latest:
1063912837004105671

Assume 50 BPS fixed mistake as well


## rETH

https://data.chain.link/optimism/mainnet/crypto-eth/reth-eth-exchange-rate

86400 | .2%

This + ETH

Latest: `1088674613563376066`



## wstETH

https://data.chain.link/optimism/mainnet/crypto-eth/wsteth-steth%20exchangerate

86400 | .5%

Latest: `1145813167107669109`

https://data.chain.link/optimism/mainnet/crypto-usd/steth-usd

86400 | .5%

Latest: `189688301923`


## Usual stuff

https://data.chain.link/optimism/mainnet/crypto-usd/wbtc-usd

1200 sec | .1%


https://data.chain.link/optimism/mainnet/crypto-usd/eth-usd

1200 sec | .15%

Latest: `190027000000`

----

# Oracle Drift Considerations

Explanation

https://miro.com/app/board/uXjVNRPbdVo=/?moveToWidget=3458764569389251297&cot=14

I considered a rational path from TOKEN -> USD

Given the available Oracles

These are all theoretical limits with the information available

Keep in mind that CL can change thresholds at any time

## Notes on Likelihood of drift

rETH and stETH have some level of front-runnability where they just rebase spot

sfrxETH has a linear decay to it's yield

I believe that sfrxETH is the most likely to be off due to the fact that the oracle must wait for either a 50 BPS change (not nothing)
Or a day to have passed.

That said, this is mostly reliant on the underlying yield to fully realize the risk

Napkin math says that for the Drift on Chainlink oracle update to fully take risk, the yearly linear projection of the yield would have to be

182% APR

182.5 / 365 / 100 = 0.005 change

Assuming 50 BPS for minting fees, any APR below that should be safe

## Protection against drift

The Protocol will have to enforce fees or limits that are built around the drift

Specifically:

- Do not allow borrowing that could cause ERN to be issued below 1-1 due to Oracle Drift
- Prevent Redemptions at fees that are not above the Oracle Drift Range (risk free arbitrage)

  - Example scenarios:
  (Simplified as linear interpolation)

  ERN trades at 1.01
  Oracle has 2% Drift
  Price in oracle is 1.02
  ERN is redeemed, the attacker receives 1 extra cent
  The redeemed against is losing that value and if they re-lever they will also lock in a fee + slippage from the pool


## Drift Math

## rETH/ETH + ETH/USD

```ts
const drifted = doubleDrift(1088674613563376066, 20, 190027000000, 20);

max delta 100.70245649034672
max up 100.35029999999998
max down 99.6503
```

----

### Suggest min buffer

Not accounting for profitability (Price Impact of Liquidations and Trading on Pools)

.71% has got to be accounted for oracle drift

## wstETH/ETH + stETH / USD

```ts
max delta 102.02015100628769
max up 101.0025
max down 99.00250000000001
```

### Suggested buffer

2.02%

## sfrxETH/frxETH + 50 BPS + ETH / USD

```ts
max delta 102.32672240684255
max up 101.1515
max down 98.85150000000003
```

### Suggested buffer

2.33%

------

Another aspect to consider is the liquidity of ERN against these assets as well as the willingness for Short Term Liquidators to borrow them

This is not feasible to simulate in a review and most of the time is influenced by incentives as well

## Conclusion on Oracle Drift

- Allowing CR below those thresholds can allow for insta-profits that depeg the system
The likelihood of said arbitrage is fairly low when it comes to LSTs that have a Rate -> ETH / USD pair
The likelihood can be higher for LSTs that directly denominate LST / USD as the desynchronized values can be greater

- Allowing Redemptions with fees below those values can allow for insta-profits that MEV the system out of it's value
This is explored further below




# Economic Risks

## Unprofitable Liquidations

If the Oracle changes too slowly, then ETHOS may mint debt that is instantly under-collateralized, or that is impossible to liquidate profitably

The loss can happen either at:
- The Liquidation level (the liquidation is factually causing a loss of value to Stability Pool Stakers because the ICR is < 100 but the system doesn't know)
- The Stability Pool Rebalancing Level (the liquidation was profitable, but by the time the staker swaps it into ERN they lose more than what they paid, effectively making the liquidation unprofitable to them)

While data below 1 hour is attainable only at the Price Feed level, it is helpful to check that as a way to determine which liquidations would have been unprofitable, while technically 20 minutes is a lot of time, I believe it's a pretty decent time frame to account for any delay (bridging funds, rebalancing pool)

## Redemptions Skim Arbitrage

Redemptions are a mechanism through which we trade ERN for Coll.

TroveManagers are deployed for different collaterals.

TroveManagers seem to have the same base redemption rate of 50 BPS.

Some Oracles, due to chained Drifting, have more than 50 BPS of Inaccuracy

This opens up to Triangular Arbitrage Opportunities that would cause losses to Depositors:

### POC
Attacker sees Pool A and Pool B

Pool A being:
ERN - LSTA

Pool B being:
ERN - LSTB

Imagine both pools being perfectly balanced (no single segment arb)

The following Arbitrage is possible under certain drift scenarios:
-> Buy ERN with LSTA (Assuming fairly Priced Pool)
-> Redeem ERN for LSTB (Assume Pool is pricing fairly, but oracle has yet to update, ETHOS is paying more LSTB per ERN)
-> Sell LSTB for LSTA
-> Profit

The profit from such a triangular arbitrage would come from the:
Difference in value that ETHOS is paying for redemptions (effectively a "dumb" AMM that asks for 50 BPS to swap from any to any collateral)
Value which is paid directly by depositors whom are enabling the fee to be paid

## Redemption Skim Arbitrage Oracle Race Condition

While the above is a theoretical attack which can be applied to any fairly valued pool, provided inaccuracies of the oracle above 50 BPS.

This is a subset of attacks that could be made possible by using same kind tokens.

Intuitively, adding more tokens with the same denomination (ETH) should offer no additional risks

In reality, if the price feeds are desynchronized, they may end up causing more risk.

To summarize:
- LST Rate + ETH / USD Feed MAY BE SAFER
Than
- LST / USD Feed

The reason is due to combinatorial complexity and race conditions.

More specifically, a LST Rate can be front-run once per day, when it updates, and the inaccuracy is quantifiable in the delay / the value
And can be avoided by raising the fee to cover for such a delay under most circumstances.

Instead a ETH / USD vs LST / USD Feed could be fully desynchronized, inheriting an additional level of Drift which, based on the Oracle Deviation Thresholds, may enable Triangular Arbitrage via Redemptions.

## Considerations around mitigation Redemption Skim Arbitrages

In both cases, the only way to completely eliminate said Arbitrage would be to enforce a Minting and a Redemption Fee that accounts for the combined Drift between the two pairs

## Full POC for Redemption Skim Arbitrage

Assume:
- LST_A and LST_B have the same price due to having the same ppfs
- The oracle is desynched and LST_A is priced fairly
- LST_B is underpriced

Because LST_A and LST_B have the same price, the Pool LST_A - LST_B is priced at 1-1
Because LST_A is priced fairly, via arbitrage, the Pool LST_A - ERN is priced fairly (1-1 dollar per dollar)

Deposit LST_A into ETHOS
- Deposit $100 of LST_A into ETHOS
- Redeem the $100 for $102 of LST_B
(Option A)
- Swap LST_B for LST_A (1-1)
- Swap LST_A for ERN (1-1)
(Option B)
- Swap LST_B (102) for ERN (102 or more)
- Close the position

This shows how a triangular arbitrage can be performed against ETHOS, the people losing are the people being redeemed against

You can seem my research on why being redeemed against is Bad here:
https://docs.google.com/document/d/1Bor6alPcmodWT9sMjX0M7-vSO62eJWT0CERihoC0G2Q/edit

The above would show a scenario that is not just "normally bad", but it's worse as the Redemption underselling the collateral unless the:
- Minting fee covers the full drift inaccuracy of LST_A
- Redemption fee covers the full drift inaccuracy of LST_B

Those cases means that peg will swing higher, but normal people will not be arbitraged against



### Conclusions 

The above is higher impact if LST_A is overpriced (especially possible if the feed is LST_A/USD) and LST_B is underpriced

I would expect the best arbitrage to be always done via stETH (Separate Multiple Feeds, vs other LSTs)

This is imo worth modelling further as otherwise depositors are effectively taking a loss without fully understanding it