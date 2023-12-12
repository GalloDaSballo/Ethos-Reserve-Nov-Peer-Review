## Only RH calls this

https://github.com/Byte-Masons/liquity-dev/blob/d6692902cebb6e61c6c42da7f7fc3d3913478c38/packages/contracts/contracts/LQTY/LQTYStaking.sol#L186-L198

```solidity
function increaseF_Collateral(address _collateral, uint _collFee) external override {
        _requireCallerIsTroveMorRH();
        uint collFeePerLQTYStaked;
     
        if (totalLQTYStaked != 0) {
            uint256 collFeeNumerator = _collFee.mul(DECIMAL_PRECISION).add(lastF_CollateralError[_collateral]);
            collFeePerLQTYStaked = collFeeNumerator.div(totalLQTYStaked);
            lastF_CollateralError[_collateral] = collFeeNumerator.sub(collFeePerLQTYStaked.mul(totalLQTYStaked));
        }

        F_Collateral[_collateral] = F_Collateral[_collateral].add(collFeePerLQTYStaked);
        emit F_CollateralUpdated(_collateral, F_Collateral[_collateral]);
    }
```