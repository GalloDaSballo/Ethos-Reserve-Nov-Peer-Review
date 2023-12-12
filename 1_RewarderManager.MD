MIRO:
https://miro.com/app/board/uXjVNRPbdVo=/?moveToWidget=3458764569610614601&cot=14


### Risks:
- Permanent DOS if external contract reverts
- Hook for View-Reentrancy + Reentrancy + xContract Reentrancy if control is allowed in any way
- Pattern is dangerous and IMO not worth using, when fixing it would take a few weeks at most and will avoid a lot of extra audits
- Hooked contract would not have access to updated System State such as total debt and coll (it's hooked too early)

## M / QA - RewarderHooks break CEI, on non protected functions and are extensible.

While non-CEI conformity is not a vulnerability by itself, but rather a Code Smell.

In this case, the pattern seems to clearly break a pattern that was established well by Liquity and that the rest of the changes have followed very thoroughly.

The code hooks before perfoming vital changes to Troves, which means those same troves could under certain circumnstances be suject to further alterations.

While no vulnerability can be demonstrated at this time due to the rewarders not being available.

The pattern followed is condusive to:
- More Risk
- More expensive Audits

And forces all future updates of being aware of this gotcha.

For this reason, I highly recommend re-discussing the pattern chosen here as the implications seem to be very drastic, while the benefits don't seem to be there.

The lack of reentrancy guards around multi contracts (see eBTC) lead me to believe that a CEI conforming refactoring should be added

This will avoid extra spends once RewarderHooks are added, as otherwise a single hook with any non-view external logic could be a entry point for Cross Contract Reentrancy

## Remediation

Due to the tight coupling it may be difficult to simply add state updates at the end, this is because those may not account for redistributions

## M / QA - RewarderHooks could cause DOS in the future

Non existing contacts, reverting contracts, gas consuming contracts, or gas bombing could all cause reverts to the execution.

It's probably best to write a "tinfoil" mode to call the contract which would:

- Ignore the return value (don't load calldata)
- Use low level call
- Pass only a fixed amount of gas

This would harden the Core Contracts, reducing the risk of additional complexity causing exploits



# Gas

https://github.com/Byte-Masons/liquity-dev/blob/e0e528536d82943e4c45947416072038a92b8195/packages/contracts/contracts/RewarderManager.sol#L14-L16

```solidity
    bool public initialized = false;
    mapping(address => EnumerableSet.AddressSet) private childrenRewarders;
    address public troveManagerAddress;
```

Swap order to pack the values

```solidity
    bool public initialized = false;
    address public troveManagerAddress;
    mapping(address => EnumerableSet.AddressSet) private childrenRewarders;
```


also initialized is default false, but it's not a big deal