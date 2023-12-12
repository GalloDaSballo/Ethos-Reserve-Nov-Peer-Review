## QA: Leverages Exempt from Fees Idea

Lever up via the Leverager to generate the debt you want

Then you deposit the rest of the Coll

You now have Skipped the Fee

---

Note that the Borrowing Fee is a big aspect as to why Sniping is not Profitable

Removing the Fee Completely would cause the System to be riskier

---

Not only that. But if any "button" is given to trigger RM at no cost, then the attack becomes more likely

Ultimately no "button" to trigger RM at no risk should be given, meaning that ExemptFromFee could exclusively be used by the DAO and IMO not even by an AMO unless the Step of Minting is separate from the Step of Swapping and Redeeming (again the "Button" goes to the Attacker)


I think only the treasury should be allowed to have zero fees, a vault may but only if the minting is separate from depositing of collateral

Synching the minting with a deposit implicitly allows to mint at zero fee, which opens a broader attack surface

## QA: Adjust Trove assert check is not necessary
https://github.com/Byte-Masons/liquity-dev/blob/dbc6465496fa9e9afe350c1079cb88da890cdcee/packages/contracts/contracts/BorrowerOperations.sol#L379-L380

```solidity
        assert(msg.sender == params._borrower || msg.sender == leveragerAddress);

```

Could run invariants, pretty sure that will never be hit

## QA: Events do not show who triggered the tx

May be best to add `msg.sender` as well
As that would allow easier tracking of who's using the Leverager

## QA: Comment: No longer borrower

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/BorrowerOperations.sol#L264

```solidity
Pull collateral, move it to the Active Pool, and mint the LUSDAmount to the borrower
```
### it actually is minting to the caller

```solidity
Pull collateral, move it to the Active Pool, and mint the LUSDAmount to the borrower or the Leverager
```

## QA: Use Named Fields for Struct

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/BorrowerOperations.sol#L350-L351

```solidity
        _adjustTrove(Params_adjustTroveFor(msg.sender, _collateral, _maxFeePercentage, _collTopUp, _collWithdrawal, _LUSDChange, _isDebtIncrease, _upperHint, _lowerHint));

```

You've used named fields everywhere else, I think it makes sense to keep the consistency.

No vuln found.

## Gas: ExemptFromFee loaded twice

Both to check if exempt and to compute it

I don't think this is a big deal, however you could cache it in the Struct `vars`


