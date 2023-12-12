## Generally speaking Tellor can always be made to return an old value

```solidity
    function getTellorCurrentValue(bytes32 _queryId)
        external
        override
        returns (
            bool ifRetrieve,
            uint256 value,
            uint256 _timestampRetrieved
        )
    {
        (bytes memory data, uint256 timestamp) = getDataBefore(_queryId, block.timestamp - 20 minutes);
        uint256 _value = abi.decode(data, (uint256));
        if (timestamp == 0 || _value == 0) return (false, _value, timestamp);
        if (timestamp > lastStoredTimestamps[_queryId]) {
            lastStoredTimestamps[_queryId] = timestamp;
            lastStoredPrices[_queryId] = _value;
            return (true, _value, timestamp);
        } else {
            return (true, lastStoredPrices[_queryId], lastStoredTimestamps[_queryId]);
        }
    }
```

- Buy a shitton of tellor (I think on OP Slash Cost is low also)
- Get slashed
- On each proposal, immediately dispute

The price will remain stale indefinitely or at least, for a long time (a day seems like)

The cost of the attack tends to go up to the cost of becoming a Tellor Broadcaster, which should be fairly low on OP (10 Tellor / $800 USD)

### Example

- CL stops
- Tellor is used
- Tellor price is old and arbitrageable
- Do the above
- Arb out the value from Ethos via redemptions and potentially overborrowing

## Side note to above - Tellor "perceived deviation" has to be accounted in MCR and CCR

Since tellor is delayed by 20 minutes, you have to account for:
- Delay in Tellor
- Imprecision Drift of tellor that wouldn't result in slashing (not sure if this is a soft consensus or real consensus)

This makes the case for having a moderately high CCR and MCR as tellors delay could be fatal.

## Math Comment

Generally speaking the highest volatility ETH has had in the past 4 years was a 10% in a day, 4% in an hour, 6% in 4 hours.

It is within reason to believe that that 4% would have been a scenario for such an attack to happen, if CCR and MCR are set too low