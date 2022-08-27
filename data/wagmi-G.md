# Summary

| Id | Title |
| -- | ----- |
| 1 | Should use memory variable when emiting events to save gas |
| 2 | Cache length in the for loop and uncheck index |


## 1. Should use memory variable when emiting events to save gas

In some function like `_setProposalThresholdBPS()`, we can use memory variable instead of storage variable with the same value when emiting events to save gas.

For example, in `_setProposalThresholdBPS()` function, we can use `newProposalThresholdBPS` instead of `proposalThresholdBPS` to save gas.

### Affected Codes
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L664
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L646
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L630


## 2. Cache length in the for loop and uncheck index

At each iteration of the loop, length is read from storage. We can cache the length and save gas per iteration.

Solidity 0.8.0 check safe math in every operation. Use uncheck to increase index can save gas.

For example
```
uint256 length = arr.length;
for (uint i; i < length; ) {
  // do stuff
  unchecked { ++i; }
}
```

### Affected Codes
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382
