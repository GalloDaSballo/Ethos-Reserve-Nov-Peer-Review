I have conducted a Peer Review of the ETHOS codebase, based on the following diff

Diff
https://github.com/Byte-Masons/liquity-dev/compare/master...one-click-leverage-2

Miro Board
https://miro.com/app/board/uXjVNRPbdVo=/?share_link_id=849769473289

I did a differential review of the changes and for some aspects of the system I also investigated various attacks scenarios

Overall the main concern long term is that as the new features increase, the complexity of the system will require having to check for compositional issues "down the stack"

There are many areas of concern, the following is a non exhaustive list:
- Active Pool Rebalancing and ERC4626 Logic
- Oracle Timeouts, CCRs and MCRs
- Same Denomination Token Arbitrage
- Tellor
- Non CEI Conformant Hooks
- Usage of Leverage under high volatility periods
- Fees and their removal


### Severity Classification

I used Spearbits Matrix



| Severity level        | Impact: High | Impact: Medium | Impact: low |
| --------------------- | ------------ | -------------- | ----------- |
| **Likelihood:high**   | Critical     | High           | Medium      |
| **Likelihood:medium** | High         | Medium         | Low         |
| **Likelihood:low**    | Medium       | Low            | Low         |

### Disclaimer

Disclaimer: This security review does not guarantee against a hack. It is a snapshot in time of the codebase according to
the specific commit. Any modifications to the code will require a new security review.

Additionally this is a best effort attempt at identifying bugs and vulnerabilities in the code, I recommend doing a bug bounty contest as well as expand the Invariant Testing of the Codebase before deploying the contracts as a way to further harden the codebase