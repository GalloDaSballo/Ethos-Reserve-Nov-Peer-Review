## M - Tellor has an implicit deviation threshold which has to be accounted for

Tellor is an optimistic oracle, this means it will accept any price and only in lack of disputes for 20 minutes will that price be accepted

The question is:
- What kind of deviation will be accepted
- What implicit deviation would 20 minute have

Going back from Economic Research, done via CG prices, we know that ETH has had a 4% rally in 1 hour, meaning that in certain scenarios, Tellor could have a drastically high deviation threshold, which would be accepted.

This can create scenarios where:
- Tellor price is 5% away from real price
- MCR is less than 105
- System is minting debt that is undercollateralized

As soon as Tellor Updates or CL Resumes working, the system will lock in bad debt.

I'm very skeptical of Tellor being usable under reasonable circumstances, and recommend further research is done on using such a oracle which doesn't seem to have a clearly predictable behaviour

### Theoretical Tellor Attack

Based on the above, we can imagine that Tellor could have a 1% deviation threshold without causing slashing

In those scenarios, it's easy to think of Tellor having a value that is off by 1% and a following value that is not

Since Tellor is completely predictable (20 min delay means price is valid), this could be done to set-up free arbitrages against the system, based on the Redemption Fee.

#### Mitigation

Tellor will generally need to have a higher Deviation Threshold and most likely a higher fee as it otherwise will allow risk-free trades to be setup under many circumstances

## M - Theoretical Spam Tellor Attack

Tellor Gas Loop Fail
```
6000 * 2 / 60 / 60
```

3 hours and a half to cause Tellor to revert indefinitely

I have reason to believe that the whole system can be denied by setting up Tellor to Revert

This can be done since as long as a new price is added, tellor must loop back until a valid value is found

In order for that to be done, all findings would have to be disputed

The question left is how long it would take for dispute to be resolved

If it takes more than 4 hours, than theoretically it's possible to create 6k invalid prices that get disputed which would cause the oracle to stop working indefinitely

While the above attack sounds pretty dumb, it seems to be feasible as Disputes take 24 hours to settle:
https://optimistic.etherscan.io/address/0xB30b1B98d8276b80bC4f5aF9f9170ef3220EC27D#code#F1#L196

Meaning that Tellor should be DOSsable and it could be used to DOS the System under most circumstances

### Cost of the attack

10 Tellor Slashed per 6000 times

https://optimistic.etherscan.io/address/0xB30b1B98d8276b80bC4f5aF9f9170ef3220EC27D#code

```solidity
            // calculate dispute fee based on number of open disputes on query ID
            if (openDisputesOnId[_queryId] > 4) {
                _disputeFee = oracle.getStakeAmount();
            } else {
                _disputeFee =
                    _disputeFee *
                    2 ** (openDisputesOnId[_queryId] - 1);
            }
```

5.8 Million Dollars

I believe if the TVL reached over 100 MLN, then some consideration should be given to this
## M - Both CL and Tellor share the same, immutable timeout

```solidity
    function _chainlinkIsFrozen(ChainlinkResponse memory _response, address _collateral) internal view returns (bool) {
        return block.timestamp.sub(_response.timestamp) > collateralConfig.getCollateralOracleTimeout(_collateral);
    }
```

```solidity
     function _tellorIsFrozen(TellorResponse  memory _tellorResponse, address _collateral) internal view returns (bool) {
        return block.timestamp.sub(_tellorResponse.timestamp) > collateralConfig.getCollateralOracleTimeout(_collateral);
    }
```

Both oracles share the same timeout duration

This can create, in many scenarios, issues as:
- CL can be considered reliable, and the timeout is an indicator of a SLA update
- Tellor cannot be considered reliable, the timeout is an indicator of Disputes, the dispute in it of itself is part of the Security Model that Tellor offers, meaning that Tellor could have a different, longer delay than chainlink, or potentially a shorter one that trips more often


### Recommendation

- Allow 2 different delays
- Model the risks of Tellor against Chainlink and determine which one to use

## QA - Comment is inconsistent

```solidity
    /*
    * Return true if the relative price difference is <= 3%: if so, we assume both oracles are probably reporting
    * the honest market price, as it is unlikely that both have been broken/hacked and are still in-sync.
    */
    return percentPriceDifference <= MAX_PRICE_DIFFERENCE_BETWEEN_ORACLES;
```

```solidity
uint constant public MAX_PRICE_DIFFERENCE_BETWEEN_ORACLES = 5e16; // 5%
```


## Gas - External call for timeout is done many times

`getCollateralOracleTimeout` is called multiple times, you may want to cache it and pass it

Should save around 200 gas per instance replaced