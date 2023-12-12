# Scope

This is a working Doc with some miscellaneous notes

I used this to determine my next steps, the notes are scribbled and generic, they may inspire you to check further logic paths, which is why I'm sharing them

# Contents

Diff
https://github.com/Byte-Masons/liquity-dev/compare/master...one-click-leverage-2


Swapper (OOS just for reference):
https://gist.github.com/0xBebis/1255855f46a2fa9f9ed4bccaf55addfb


3 Days Left
+ Math


-----------

Math Deliverables

Economic analysis around arbitraging feeds and risk if the feed is too slow or too fast
Economic analysis around same token feeds arbs
Economic analysis around MCRs and CCRs given the constraint that you cannot reduce them


TODO: Some thoughts around
Liquidity of L2 can cause arbs behaviour to be different meaning that price action is slightly different

-----------





# Scope


- packages/contracts/contracts/LiquidationHelper.sol - DONE

TODO: Check one last time the Diff when fresh (Monday morning)



- packages/contracts/contracts/StabilityPool.sol - DONE

TODO: Check old issues and see if anything is wrong there

- packages/contracts/contracts/BorrowerOperations.sol -> DONE
- openTrove
-> Sends tokens and moves tokens from msg.sender (caller or Leverager)
-> Leverage has special new function `openTroveFor`
-> Events can be improved

TODO: One last check Monday

--------


- packages/contracts/contracts/ActivePool.sol -> DONE
contracts are unique and others are not
  - Risks with vaults
  - Risks with donations
  - Immutable vaults = Can we cause it to revert = Fucked?

IMO More risks here, long term, bug bounty perhaps

TODO: Check MultiStrat for more Dosses
Long term stuff to check

--------





------

- packages/contracts/contracts/CollSurplusPool.sol - DONE
All Good

- packages/contracts/contracts/PriceFeed.sol - DONE
Change to Staleness check, sent a note


------


- packages/contracts/contracts/SortedTroves.sol - DONE
Adds way to track current Hints, useful
Not much to say
Double check Prev and Next makes sense when used

SAFE - I want to see how these new hints are used.

TODO: Just write an invariant for this shit, separate from the rest

TODO: Recommend Invariants for this, although it seems like no meaningful change was made here


Diff LG

Same as before

I believe that there may be some edge cases and would reccommend writing an invariant test for passing swapped / wrong hints

I do not have the time to write those and beleive that there is no meaningful difference from the previous code.


- packages/contracts/contracts/RewarderManager.sol - DONE

- packages/contracts/contracts/CollateralConfig.sol - DONE
Basically done.


- packages/contracts/contracts/Dependencies/EnumerableSet.sol - Verified that it's not changed beside a comment above - Verified SAFE


- packages/contracts/contracts/Leverager.sol - DONE | was OOS

It's worth noting that:
- UniV3 is unlikely to have hooks, since it will callback to the msg.sender (all under control)
- Balancer is fairly likely to have hooks, as Pool Implementations are:
  - Generally poorly understood (very few people completely understand balancer)
  - New Pools tend to have tweaks that can be more harmful than good
  - It's very hard to keep track of these due to the decentralized process that Balancer Follows
  - Some reentrancies, arbitrary code execution and race-conditons have been found very recently.

My 2 cents on Balancer are that the extremely old, no rates, weighted and stable pools are safe, except for View-Reentrancy.
Whereas newer pools should be considered "hookable" and IMO should be separately Audited as part of ETHOS before ever being used.

- https://optimistic.etherscan.io/address/0x344fdc08f133d854a22f04393f390536139eb61a#code
Courtesy check on contract
Due to injected router address, allowances can be farmed
As they are not alwyas (but only on revert), set back to 0

- packages/contracts/contracts/RedemptionHelper.sol - DONE
Just fixing a Enum usage. LG

- packages/contracts/contracts/LQTY/LQTYStaking.sol
Added Redemption Helper for Staking
Nothing else 
DONE


------------

Economic:
-> MCR and CCR
-> Assuming certain ratios of other stakers
-> Showing the worst case scenario for redistributions

-> Cross Token, Drift Based Arbitrage

-> Oracle being LST_ETH / USD
Means more likely to be desynched with
-> ETH / USD

Can open up for Triangular Arbitrage via Redemptions any time the Double Drift is above .5 (in spite of Pools being in equilibrium)

------------

Preamble:

The following Security Review was performed on the above diff, with the information available to me at that time.

The review should not be viewed as exhaustive and the comments made should be viewed as suggestions about math and software engineering.

------------

Severity Classification

I am using Spearbits Matrix:
https://polygon.technology/blog/polygon-zkevm-results-of-spearbits-security-audit



## Deployment Notes

packages/contracts/mainnetDeployment/deploymentParams.optimismMainnet.js

vs
packages/contracts/mainnetDeployment/deploymentParams.localForkUpgrade.js

wBTC Settings are different

---