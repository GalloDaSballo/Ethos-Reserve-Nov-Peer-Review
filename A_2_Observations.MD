# Notes

- Can we make leverage open for self, and in some way attack that?
- Can we get leverager to abuse approvals?


# Redacted Findings

These 2 findings are based on my experience working on eBTC, we have sponsored a fee to raise it's heartbeat, that's to say that in lack of sponsor, CL may deprecated or change the update threshold, or frequency of oracles at their discretion.

The setters should be built consistently with those risks

## Oracle Update Time may change in the future and cause issues


## M / QA - Oracle Threshold may change and this may cause issues
https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/CollateralConfig.sol#L118-L140

```solidity
    // Admin function to lower the collateralization requirements for a particular collateral.
    // Can only lower, not increase.
    //
    // !!!PLEASE USE EXTREME CARE AND CAUTION. THIS IS IRREVERSIBLE!!!
    //
    // You probably don't want to do this unless a specific asset has proved itself through tough times.
    // Doing this irresponsibly can permanently harm the protocol.
    function updateCollateralRatios(
        address _collateral,
        uint256 _MCR,
        uint256 _CCR
    ) external onlyOwner checkCollateral(_collateral) {
        Config storage config = collateralConfig[_collateral];
        require(_MCR <= config.MCR, "Can only walk down the MCR");
        require(_CCR <= config.CCR, "Can only walk down the CCR");

        require(_MCR >= MIN_ALLOWED_MCR, "MCR below allowed minimum");
        config.MCR = _MCR;

        require(_CCR >= MIN_ALLOWED_CCR, "CCR below allowed minimum");
        config.CCR = _CCR;
        emit CollateralRatiosUpdated(_collateral, _MCR, _CCR);
    }
```


## Note that for fast oracles this may not be the case
https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/CollateralConfig.sol#L90-L91

```solidity
        require(_oracleTimeout > 20 minutes, "Timeout too low");

```

This should be fine for now, worth monitoring as newer oracles can expire very rapidly

I don't believe the CDP model with Fairly Static Redemption Fee Lends itself to work with faster oracles but worth monitoring long term


## If there's a hook that messes up the system in the future, is this one

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/TroveManager.sol#L735-L738

```solidity
    function _closeTrove(address _borrower, address _collateral, TroveStatus closedStatus) internal {
        assert(closedStatus != TroveStatus.nonExistent && closedStatus != TroveStatus.active);

        rewarderManager.onTroveClose(_borrower, _collateral, uint(closedStatus)); /// @audit CEI is broken
```


The call is done before state is updated, code is not in scope (and doesn't seem to be available)

The other extremely high risk line is in rebalancing, however, having manual rebalancing reduces the risk


## QA - System logic becoming fragmented

It seems like some of the checks are done before setters, while others are done at the beginning of functions

To be on the safer side it may be best to have redundant checks (e.g. OpenTrove checks `_requireValidCollateralAddress` and calls `increaseLUSDDebt`)

```solidity
    function increaseTroveDebt(address _borrower, address _collateral, uint _debtIncrease) external override returns (uint) {
        _requireCallerIsBorrowerOperations();
        require(collateralConfig.getCollateralDebtLimit(_collateral) >= getEntireSystemDebt(_collateral).add(_debtIncrease),
            "TroveManager: Debt increase exceeds limit");
        rewarderManager.onDebtIncrease(_borrower, _collateral, _debtIncrease);
        uint newDebt = Troves[_borrower][_collateral].debt.add(_debtIncrease);
        Troves[_borrower][_collateral].debt = newDebt;
        return newDebt;
    }
```

```solidity
    function increaseLUSDDebt(address _collateral, uint _amount) external override {
        _requireValidCollateralAddress(_collateral);
        _requireCallerIsBOorTroveM();
        LUSDDebt[_collateral] = LUSDDebt[_collateral].add(_amount);
        ActivePoolLUSDDebtUpdated(_collateral, LUSDDebt[_collateral]);
    }
```

Overall this is a sign of the extra complexity in adding to the liquity model

My main recommendation is to write new tests that automatically perform checks on each accessor and on each external function as to ensure that the intended checks are always performed under all logic paths
