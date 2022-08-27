QA Findings
---
## Checking of same logic twice
In `_burnVetoPower()`, `require(msg.sender == vetoer)` is checked. This is unneccessary as the same check is done in `_setVetoer(address(0))`, called in the second line.
```solidity
function _burnVetoPower() public {
  // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
  _setVetoer(address(0));
}
```
Recommendations: Remove the `require(msg.sender == vetoer)` check in `_burnVetoPower()`.

Affected:
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L651
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853



## Typo error in MAX_QUORUM_VOTES_BPS_UPPER_BOUND
Comment indicates 4,000 bps or 60% and value is set to 6,000. Likely a typo (4,000 should be 6,000). Or wrong value set in MAX_QUORUM_VOTES_BPS_UPPER_BOUND, which should be 4,000.
```solidity
uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```
Affected:
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86