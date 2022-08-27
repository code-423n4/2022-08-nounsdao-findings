## Table of Contents
### Low Risk Issues
- Performing a Multiplication on the Result of a Division

### Non-critical Issues
- Use fixed compiler versions instead of floating version
- Events Not Emitted for Important State Changes

&ensp;
## Low Risk Issues
### Performing a Multiplication on the Result of a Division

#### Issue
Solidity integer division might truncate. As a result, performing multiplication before division can sometimes avoid loss of precision.
Reference: https://github.com/crytic/slither/wiki/Detector-Documentation#divide-before-multiply

#### PoC
1. NounsDAOLogicV2.dynamicQuorumVotes() 
```solidity
908:        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
909:        uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L908-L909

#### Mitigation
Consider ordering multiplication before division.

&ensp;
## Non-critical Issues
### Use fixed compiler versions instead of floating version

#### Issue
it is best practice to lock your pragma instead of using floating pragma.
the use of floating pragma has a risk of accidentally get deployed using latest complier
which may have higher risk of undiscovered bugs.
Reference: https://consensys.github.io/smart-contract-best-practices/development-recommendations/solidity-specific/locking-pragmas/

#### PoC
```
./NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
./NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
./NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
./ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
./ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
./NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
```

#### Mitigation
I suggest to lock your pragma and aviod using floating pragma.
```
// bad
pragma solidity ^0.8.10;

// good
pragma solidity 0.8.10;
```

&ensp;
### Events Not Emitted for Important State Changes

#### Issue
It is best practice to emit an event when we there is important state changes like update a 
dynamic array or mapping because it allows monitoring activities with off-chain monitoring tools.

#### PoC
Total of 2 instances found.
1. _writeQuorumParamsCheckpoint() of NounsDAOLogicV2.sol
No event is emitted even though dynamic array is added to a quorumParamsCheckpoints array.
```solidity
quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L970

2. _addTokenToAllTokensEnumeration() of ERC721Enumerable.sol
No event is emitted even though dynamic array is added to a _allTokens array.
```solidity
_allTokens.push(tokenId);
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L130-L133

#### Mitigation
Emit an event when there is important state changes.

&ensp;