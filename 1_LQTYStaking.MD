

## QA: only RH uses `increaseF_Collateral`

The change is innocuous, but only `redemptionHelperAddress` calls `increaseF_Collateral`

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/RedemptionHelper.sol#L197-L198

```solidity
        lqtyStaking.increaseF_Collateral(_collateral, totals.collateralFee);

```

So this line can be simplified

```solidity
    function _requireCallerIsTroveMorRH() internal view {
        require(
            msg.sender == troveManagerAddress ||
            msg.sender == redemptionHelperAddress,
            "LQTYStaking: caller is neither TroveM nor RH"
        );
    }
```

To
```solidity
    function _requireCallerIsTroveMorRH() internal view {
        require(
            msg.sender == redemptionHelperAddress,
            "LQTYStaking: caller is neither TroveM nor RH"
        );
    }
```