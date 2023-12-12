# Leverager

## NOTE - OOS

The Leverager and the Swapper were OOS for this review

I did a quick check and wrote my thoughts below

The main considerations I have are:
- DOS during High Leverage Times
- UX / Usage can be made simpler via minOut at end Check + CR check
- Callbacks in UniV3 and Balancer are a big concern long term

-----


A few notes

Comments
```solidity
    uint public minERNPrice = 0.995 ether; /// @audit When people lever up, they dump ERN, this prevents them from doing too much (except if using Curve | Bal)
    uint public maxERNPrice = 1.08 ether;
    uint public minSwapPercentOut = 0.99 ether; /// @audit 1% PI | Depends on the stability of the supporting pool
```

### M - Leverager can stop working in many "normal scenarios"

minERNPrice of 995 implies the Redemption Fee

But buying ERN has a Swap Fee

So ERN will naturally move below that price.

Additionally, in many Bullish Scenarios, people will willing take more than 1% slippage to lever up

Redemptions under those cases would take a lot of time due to time decay, for that reason, .995 for lower depeg should not be sufficient in many cases, especially as a "handholding value"

2% should work under most scenarios, except mania (e.g. 1k% apy on levered ERN)

----

### Gas: Hints are saving some gas, but offChain hints will save more gas in general

Since you can preview the whole tx, it should be fairly easy to add those hints

Specifically you could create an array (index of array = iteration of leverage)

[NICR_1, NICR_2, etc..]

Which will cost a few hundreds extra gas per call, but should save on average at least 2k per each CDP that is skipped in the list

-----

### Gas Considerations

UniV3 and Balancer Loops are also super expensive (I believe 300k per loop)

My main concern with UniV3 and Balancer is potential reentrancies

Something to consider:
- UniV3 -> Callback is safe (and swapper is safe as well, afaict)
- Balancer -> Vault Hooks + Rate Providers + Cached Providers etc + Potentially a Malicious Pool - I believe extreme diligence is necessary for balancer pools, and believe many new pools should not be added without proper DD (old, basic pool implementations seem to be safe afaict)

I believe the pattern is safer as is and that you can over time look into a flashLoan like pattern

----

### QA: Note on slippage checks

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/Leverager.sol#L435-L436

```solidity
        require(amountOut != 0, "Leverager: Swap failed");

```
Amount Out will always be non-zero

Even a 99.9999999999 loss is still non-zero

I would suggest adding a check there or a global last check at the end of the entire loop

The last check would avoid getting inconsistent results / MEVd away due to improper usage and would make the whole usage of the Leverager a little easier

A check as simple as verifying that the balance here is above a certain threshold

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/Leverager.sol#L234-L235

```solidity
        lusdToken.safeTransfer(msg.sender, lusdToken.balanceOf(address(this)));

```


# QA: Math for slippage limits is incorrect

https://github.com/Byte-Masons/liquity-dev/blob/dbc6465496fa9e9afe350c1079cb88da890cdcee/packages/contracts/contracts/Leverager.sol#L138-L141

```solidity
        require(
            _minSwapPercentOut >= LiquityMath.DECIMAL_PRECISION - LiquityMath.DECIMAL_PRECISION / 150,
            "More than 1.5% slippage"
        );
```

```python
>>> 1e18 - 1e18 // 150
9.933333333333334e+17
>>> 1/0.993
1.0070493454179255
>>> 1/0.93
1.075268817204301
>>> 1/0.933333
1.0714289540817694
```

the math doesn't match the goal of 1.5%

This is not a big deal but worth thinking about

## Misc notes

Also could just do a ERN vault with flashloans
The vault just does that + provides to SP
The FL can be used for one off Lever Up and Down
And should be on average better than other stuff

If MEV concern, you can do a commit - execute style system

Unclear hint system cause you're re-inserting the trove multiple times

I think a step wise system may be necessary
(Pre-compute hints offChain per iteration)

I think code will be safe but all hints will be wrong at most times