# Non-Library/Interface files should use fixed compiler versions, not floating ones

## Details
Contracts should be deployed with the same compiler version/flags where they have been tested with. Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version that might introduce bugs that affect the contract system negatively.
see reference: https://github.com/code-423n4/2021-11-unlock-findings/issues/15, https://code4rena.com/reports/2022-03-paladin/ and https://swcregistry.io/docs/SWC-103

## Mitigation
I suggest removing `^` in `pragma solidity ^0.8.6` and change it to `pragma solidity 0.8.15` to be consistent with the rest of the contracts.

## Line of code
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L61
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L53
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L36

___
# Lack of indexed parameters in events

## Details
Some of the events throughout the codebase are not indexed. Indexing event parameters enable off-chain services to search and filter for specific events.
see reference: Low severity finding from OpenZeppelin Audit of HoldeFi
[L09] Lack of indexed parameters in events
https://blog.openzeppelin.com/holdefi-audit/#low

## Mitigation
Add the `indexed` keyword to the events.

## Line of code
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L37-L47
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L50-L62