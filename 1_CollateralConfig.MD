## M / L - `OracleTimeout` cannot be changed

This will force you to have a way larger value than what could happen

The DAO could setup an SLA with Chainlink to guarantee those timeouts

In lack of that, such check should be expected to potentially change and cause a deprecation of the token or the inability for the collateral to be used

----

## QA: updateCollateralRatios Further Risks notice

Not being able to increase MCRs and CCRs can be extremely dangerous

Tokens not being re-settable or having a V2 may also be an issue

I believe it should not be possible without caps and without a way to kill it

More specifically, reducing the CRs without being able to bring them back up means that all economic parameter decisions for the platform must be made in a more conservative way, a collateral may have reached a stabler short term volatility without losing it's long term volatility. Meaning that reducing CRs should account for longer time frames and more pessimistic values than short term volatility.

For example it may be best to look at volatility over each bull / bear cycle instead of just a year or two

### A fundamental issue with assumption of reducing CCR and MCR is that it ignores the cyclicality of Crypto

Crypto is not just extremely volatile.

Similarly to rain statistics, you have period of droughts (low volatility) and rainstorms (insane volatility)

A system in which CCR and MCR is only reduced implies the assumption that volatity will reduce over time.

This can be true, but is not just a difficult decision to make, it's a decision that has to be done with a very broad outlook.

For example, if we used the last 6 months of volatility on ETH, we would reach the conclusion that it's not as volatile as if we took the last 2 years.

This, mixed with new attacks at the oracle levels leads me to suggest that the idea of always decreasing CCR and TCR (adding risk) is a risky one and should be changed.

Over time you'll either have 2 options:
1) Super conservative values to prevent not being able to raise them (which is an approach I would recommend either way in spite of its limitations)
2) Needing to tweak CCR every 3 to 6 months to predict future volatility (implying the vol can go up and down)

Liquidity onChain can be bought (see Arb vs OP incentive models), but that's a micro-aspect of the discussion (the one that could be controlled as liquidity lowers you can deprecate the system)

That said raising CCR and MCR does create very clear risks, such as front-running the Proposal and Triggering RM (see my Sandwich Sniper POC), which means this is a tough choice either way.


## QA: DebtLimit change frontrun / race condition

It's in the interest of an exploiter to use as much debt as possible

They may frontrun changes to that setter or predict a reduction

This would not be a risk unless the cap for debt is added reactively, meaning it will always be best to put the caps pro-actively in expectation of volatility events more-so than in reaction to them (I have a theory you can monitor OI and predict possible liquidations based on price swings, which could help predicting said scenarios)




