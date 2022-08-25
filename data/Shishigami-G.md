# **Gas optimizations**



## [G-01] Use prefix not postfix especially in loops
Saves 6 gas per loop.

1. NounsDAOLogicV1.sol
  - [NounsDAOLogicV1.sol#281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#281)
  - [NounsDAOLogicV1.sol#319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#319)
  - [NounsDAOLogicV1.sol#346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#346)
  - [NounsDAOLogicV1.sol#371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#371)


2. NounsDAOLogicV2.sol
  - [NounsDAOLogicV2.sol#292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#292)
  - [NounsDAOLogicV2.sol#330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#330)
  - [NounsDAOLogicV2.sol#357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#357)
  - [NounsDAOLogicV2.sol#382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#382)


Recommended mitigation steps: \
Use `++i` instead


## [G-02] Do not initialize non constant/non immutable vairable to zero
Not overwriting the default value for stack variable saves 8 gas per loop.

1. NounsDAOLogicV1.sol
  - [NounsDAOLogicV1.sol#281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#281)
  - [NounsDAOLogicV1.sol#319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#319)
  - [NounsDAOLogicV1.sol#346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#346)
  - [NounsDAOLogicV1.sol#371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#371)


2. NounsDAOLogicV2.sol
  - [NounsDAOLogicV2.sol#292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#292)
  - [NounsDAOLogicV2.sol#330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#330)
  - [NounsDAOLogicV2.sol#357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#357)
  - [NounsDAOLogicV2.sol#382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#382)

3. ERC721Checkpointable.sol
- [ERC721Checkpointable.sol#41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#41)

Recommended mitigation steps: \
Do not initialize them to 0.

## [G-03] `++i/i++` should be unchecked{++i}/unchecked{i++} when it is not possible to overflow
In solidity 0.8.0 and above, checked arithmetic is implemented but can be deactivated by using `unchecked` keyword. This saves 30-40gas per loop. Since `i` is bounded in the loop, there is no need for checked arithmetic if `proposal.targets.length` < 2<sup>256</sup>-1.

1. NounsDAOLogicV1.sol
  - [NounsDAOLogicV1.sol#281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#281)
  - [NounsDAOLogicV1.sol#319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#319)
  - [NounsDAOLogicV1.sol#346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#346)
  - [NounsDAOLogicV1.sol#371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#371)


2. NounsDAOLogicV2.sol
  - [NounsDAOLogicV2.sol#292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#292)
  - [NounsDAOLogicV2.sol#330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#330)
  - [NounsDAOLogicV2.sol#357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#357)
  - [NounsDAOLogicV2.sol#382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#382)

Implementing [G-01], [G-02], [G-03] would result in for each instance:
```javascript
  for (uint256 i; i < proposal.targets.length) {
  // ...
  unchecked{++i;}
  }
  ```

## [G-04] <= cost less gas than <
The compiler uses opcodes `LT` and `ISZERO` for solidity code that uses <, but only requires `GT` for <=, which saves 3 gas.

[NounsDAOLogicV2.sol#989](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#989)

Recommended mitigation steps: \
`return a <= b ? a : b;`

## [G-05] require()/revert() strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 incurs an `MSTORE` which cost 3 gas.

1. NounsDAOLogicV2
- [NounsDAOLogicV2.sol#133](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#133)
- [NounsDAOLogicV2.sol#135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#135)
- [NounsDAOLogicV2.sol#136](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#136)
- [NounsDAOLogicV2.sol#139](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#139)
- [NounsDAOLogicV2.sol#143](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#143)
- [NounsDAOLogicV2.sol#147](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#147)
- [NounsDAOLogicV2.sol#199](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#199)
- [NounsDAOLogicV2.sol#205](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#205)
- [NounsDAOLogicV2.sol#207](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#207)
- [NounsDAOLogicV2.sol#208](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#208)
- [NounsDAOLogicV2.sol#215](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#215)
- [NounsDAOLogicV2.sol#219](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#219)
- [NounsDAOLogicV2.sol#314](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#314)
- [NounsDAOLogicV2.sol#326](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#326)
- [NounsDAOLogicV2.sol#347](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#347)
- [NounsDAOLogicV2.sol#375](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#375)
- [NounsDAOLogicV2.sol#377](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#377)
- [NounsDAOLogicV2.sol#433](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#433)
- [NounsDAOLogicV2.sol#577](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#577)
- [NounsDAOLogicV2.sol#593](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#593)
- [NounsDAOLogicV2.sol#594](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#594)
- [NounsDAOLogicV2.sol#597](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#597)
- [NounsDAOLogicV2.sol#622](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#622)
- [NounsDAOLogicV2.sol#625](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#625)
- [NounsDAOLogicV2.sol#638](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#638)
- [NounsDAOLogicV2.sol#641](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#641)
- [NounsDAOLogicV2.sol#655](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#655)
- [NounsDAOLogicV2.sol#659](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#659)
- [NounsDAOLogicV2.sol#674](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#674)
- [NounsDAOLogicV2.sol#684](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#684)
- [NounsDAOLogicV2.sol#702](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#702)
- [NounsDAOLogicV2.sol#707](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#707)
- [NounsDAOLogicV2.sol#711](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#711)
- [NounsDAOLogicV2.sol#727](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#727)
- [NounsDAOLogicV2.sol#801](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#801)
- [NounsDAOLogicV2.sol#819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#819)
- [NounsDAOLogicV2.sol#840](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#840)
- [NounsDAOLogicV2.sol#853](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#853)
- [NounsDAOLogicV2.sol#923](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#923)

2. NounsDAOProxy
- [NounsDAOProxy.sol#79](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#79)
- [NounsDAOProxy.sol#80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#80)

3. ERC721Checkpointable
- [ERC721Checkpointable.sol#164](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#164)

4. ERC721Checkpointable
- [ERC721Enumerable.sol#62](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#62)
- [ERC721Enumerable.sol#77](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#77)

Recommended mitigation steps: \
Use shorter string or preferably use [custom errors](https://blog.soliditylang.org/2021/04/21/custom-errors/). Indeed, custom errors from Solidity 0.8.4 not only saves gas upon deployment - ~9500 gas saved per custom error instead of a require statement, but it is also cheaper in a function call, 22 gas saved per require statement replaced with a custom error.

## [G-06] Use a more recent version of solidity

### 1. Use of solidity version 0.8.6
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

- [NounsDAOLogicV2.sol#53](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#53)
- [NounsDAOLogicV1.sol#61](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#61)
- [NounsDAOInterfaces.sol#33](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#33)
- [NounsDAOProxy.sol#36](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#36)
- [ERC721Checkpointable.sol#35](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#35)

### 2. Use of solidity version 0.8.0
Use a solidity version of at least 0.8.2 to get compiler automatic inlining. Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

- [ERC721Enumerable.sol#28](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#28)

## [G-07] When using keccak256, use immutable instead of constant
Constant expressions are re-calculated each time they are in use, costing an extra 97 gas than a constant every time they are called.
See discussion [here](https://github.com/ethereum/solidity/issues/9232)

- [NounsDAOLogicV2.sol#102](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#102)
- [NounsDAOLogicV2.sol#105](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#105)
- [ERC721Checkpointable.sol#60](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#60)
- [ERC721Checkpointable.sol#64](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#64)

## [G-08] Use private instead of public for constant variable where possible saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

- [NounsDAOLogicV2.sol#58](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#58)
From line 58 to 98

## [G-09] Multiplication/division by two should use bit shifting

`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas.

Here is a division by 2 done in a `while` loop :
- [NounsDAOLogicV2.sol#951](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#951)

## [G-10] `abi.encode()` is less efficient than `abi.encodePacked()`

- [ERC721Checkpointable.sol#135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#135)
- [ERC721Checkpointable.sol#137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#137)

## [G-11]`proposal.targets.length` should not be looked up in every loop of a for-loop
The overheads outlined below are PER LOOP, excluding the first loop

  - storage arrays incur a Gwarmaccess (100 gas)
  - memory arrays use `MLOAD` (3 gas)
  - calldata arrays use `CALLDATALOAD` (3 gas)

Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset

- [NounsDAOLogicV2.sol#292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#292)
- [NounsDAOLogicV2.sol#330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#330)
- [NounsDAOLogicV2.sol#357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#357)
- [NounsDAOLogicV2.sol#382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#382)

## [G-12] Avoid emitting a storage variable when a memory vairable is available
When the argument of the function and the memory value are the same, you should rather emit the argument variable (memory value).

- [NounsDAOLogicV2.sol#630](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#630) : should be `emit VotingDelaySet(oldVotingDelay, newVotingDelay);` (i.e. replace `votingDelay` by `newVotingDelay`)
- [NounsDAOLogicV2.sol#646](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#646) : should be `emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);` (i.e. replace `votingPeriod` by `newVotingPeriod`)
- [NounsDAOLogicV2.sol#664](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#664) : should be `emit ProposalThresholdBPSSet(oldProposalThresholdBPS, newProposalThresholdBPS);` (i.e. replace `proposalThresholdBPS` by `newProposalThresholdBPS`)

- [NounsDAOLogicV2.sol#778](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#778) : should be `emit MinQuorumVotesBPSSet(oldParams.minQuorumVotesBPS, newMinQuorumVotesBPS);` (i.e. replace `params.minQuorumVotesBPS` by `newMinQuorumVotesBPS`)
- [NounsDAOLogicV2.sol#779](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#779) : should be `emit MaxQuorumVotesBPSSet(oldParams.maxQuorumVotesBPS, newMaxQuorumVotesBPS);` (i.e. replace `params.maxQuorumVotesBPS` by `newMaxQuorumVotesBPS`)
- [NounsDAOLogicV2.sol#780](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#780) : should be `emit QuorumCoefficientSet(oldParams.quorumCoefficient, newQuorumCoefficient);` (i.e. replace `params.quorumCoefficient` by `newQuorumCoefficient`)

## [G-13] Use of the `memory` keyword instead of `storage`
When copying a state struct in memory, there are as many SLOADs and MSTOREs as there are slots. When reading the whole struct multiple times is not needed, it's better to actually only read the relevant field(s). When only some of the fields are read several times, these particular values should be cached instead of the whole state struct.

Here, the `storage` keyword should be used instead of `memory`: (see @audit)

- [NounsDAOLogicV2.sol#903](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#903)
```javascript
function dynamicQuorumVotes(
    uint256 againstVotes,
    uint256 totalSupply,
    DynamicQuorumParams memory params //@audit should be storage instead of memory
) public pure returns (uint256) {
    uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
    uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;//@audit params occurence 1
    uint256 adjustedQuorumBPS = params.minQuorumVotesBPS + quorumAdjustmentBPS;//@audit params occurence 2
    uint256 quorumBPS = min(params.maxQuorumVotesBPS, adjustedQuorumBPS);//@audit params occurence 3
    return bps2Uint(quorumBPS, totalSupply);
}
}
```

## [G-14] Use `!= 0` instead of `> 0` on uint variables
uint variables will never be lower than 0. Therefore, > 0 and != 0 have same meanings. Using != 0 can reduce the gas deployment cost, so it is worth using != 0 wherever possible.

- [NounsDAOLogicV2.sol#541](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#541)
- [NounsDAOLogicV2.sol#967](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#967)
- [ERC721Checkpointable.sol#153](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#153)
- [ERC721Checkpointable.sol#215](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#215)
- [ERC721Checkpointable.sol#218](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#218)
- [ERC721Checkpointable.sol#225](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#225)
- [ERC721Checkpointable.sol#243](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#243)