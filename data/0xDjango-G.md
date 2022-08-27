### Gas Optimizations
|        | Finding                                                                                                      |  Instances  |
|:-------|:-------------------------------------------------------------------------------------------------------------|:-----------:|
| [G-01] | Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate |      2      |
| [G-02] | Cache `array.length` in `require()` statement                                                                |      2      |
| [G-03] | `for` loop increments should be `unchecked{}` if overflow is not possible                                    |      8      |
| [G-04] | `array.length` should be cached in `for` loop                                                                |      8      |
| [G-05] | Using prefix(`++i`) instead of postfix (`i++`)  saves gas                                                    |      8      |
| [G-06] | Use custom errors rather than `revert()`/`require()` strings to save gas                                     |      2      |
| [G-07] | Unnecessary inits to 0                                                                                       |     10      |
| [G-08] | `require()`/`revert()` checks used multiples times should be turned into a function or modifier              |      4      |
| [G-09] | String longer than 32 bytes cost more gas                                                                    |     64      |
| [G-10] | `bool` is gas inefficient when used in storage                                                               |      5      |
### Gas overview per contract
| Contract                                                                                                                                                           |  Instances  |  Gas Ops  |
|:-------------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------:|:---------:|
| [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol)       |     48      |     8     |
| [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol)       |     46      |     8     |
| [ERC721Checkpointable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol)   |      9      |     3     |
| [NounsDAOInterfaces.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol) |      5      |     1     |
| [NounsDAOProxy.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol)           |      2      |     1     |
| [ERC721Enumerable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol)           |      2      |     1     |
| [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol)       |      1      |     1     |
## Gas Optimizations
### [G-01] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operatio
*2 instances of this issue have been found:*
###### [G-01] [ERC721Checkpointable.sol#L44-L45](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L44-L45)
```solidity

    mapping(address => address) private _delegates;

```
###### [G-01b] [ERC721Checkpointable.sol#L53-L56](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L53-L56)
```solidity

mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

/// @notice The number of checkpoints for each account
mapping(address => uint32) public numCheckpoints;
```
### [G-02] Cache `array.length` in `require()` statement
`targets.length` is used multiple times. Caching it in memory can decrease gas usage from every read.
*2 instances of this issue have been found:*
###### [G-02] [NounsDAOLogicV2.sol#L201-L208](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L201-L208)
```solidity

require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```
###### [G-02b] [NounsDAOLogicV1.sol#L191-L198](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L191-L198)
```solidity

require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```
### [G-03] `for` loop increments should be `unchecked{}` if overflow is not possible
From Solidity `0.8.0` onwards using the `unchecked` keyword saves 30 to 40 gas per [loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked).
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*8 instances of this issue have been found:*
###### [G-03] [NounsDAOLogicV2.sol#L382-L383](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382-L383)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03b] [NounsDAOLogicV2.sol#L330-L331](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330-L331)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03c] [NounsDAOLogicV2.sol#L292-L293](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292-L293)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03d] [NounsDAOLogicV1.sol#L371-L372](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371-L372)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03e] [NounsDAOLogicV1.sol#L346-L347](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346-L347)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03f] [NounsDAOLogicV1.sol#L319-L320](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319-L320)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03g] [NounsDAOLogicV1.sol#L281-L282](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L282)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-03h] [NounsDAOLogicV2.sol#L357-L358](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357-L358)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
### [G-04] `array.length` should be cached in `for` loop
Caching the length array would save gas on each iteration by not having to read from memory or storage multiple times.
Example:
```solidity
uint length = array.length;
for (uint i; i < length;){
		uncheck { ++i }
}
```
*8 instances of this issue have been found:*
###### [G-04] [NounsDAOLogicV2.sol#L382-L383](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382-L383)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04b] [NounsDAOLogicV2.sol#L330-L331](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330-L331)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04c] [NounsDAOLogicV2.sol#L292-L293](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292-L293)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04d] [NounsDAOLogicV1.sol#L371-L372](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371-L372)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04e] [NounsDAOLogicV1.sol#L346-L347](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346-L347)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04f] [NounsDAOLogicV1.sol#L319-L320](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319-L320)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04g] [NounsDAOLogicV1.sol#L281-L282](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L282)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-04h] [NounsDAOLogicV2.sol#L357-L358](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357-L358)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
### [G-05] Using prefix(`++i`) instead of postfix (`i++`)  saves gas
It saves 6 gas per iteration.
*8 instances of this issue have been found:*
###### [G-05] [NounsDAOLogicV2.sol#L382-L383](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382-L383)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05b] [NounsDAOLogicV2.sol#L330-L331](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330-L331)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05c] [NounsDAOLogicV2.sol#L292-L293](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292-L293)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05d] [NounsDAOLogicV1.sol#L371-L372](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371-L372)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05e] [NounsDAOLogicV1.sol#L346-L347](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346-L347)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05f] [NounsDAOLogicV1.sol#L319-L320](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319-L320)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05g] [NounsDAOLogicV1.sol#L281-L282](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L282)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-05h] [NounsDAOLogicV2.sol#L357-L358](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357-L358)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
### [G-06] Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors save ~50 gas each time they're hit by avoiding having to allocate and store the revert string.
Only one instance was given but the issue can be attributed to all `require()`/`revert()` functions that do not implement custom errors in the contract.
*2 instances of this issue have been found:*
###### [G-06] [NounsDAOLogicV2.sol#L133-L134](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L133-L134)
```solidity

        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```
###### [G-06b] [NounsDAOLogicV1.sol#L122-L123](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L122-L123)
```solidity

        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```
### [G-07] Unnecessary inits to 0
These values are set by default.
*10 instances of this issue have been found:*
###### [G-07] [ERC721Checkpointable.sol#L41-L42](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L41-L42)
```solidity

    uint8 public constant decimals = 0;

```
###### [G-07b] [NounsDAOLogicV2.sol#L382-L383](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382-L383)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07c] [NounsDAOLogicV2.sol#L357-L358](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357-L358)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07d] [NounsDAOLogicV2.sol#L330-L331](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330-L331)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07e] [NounsDAOLogicV2.sol#L292-L293](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292-L293)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07f] [NounsDAOLogicV1.sol#L371-L372](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371-L372)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07g] [NounsDAOLogicV1.sol#L346-L347](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346-L347)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07h] [NounsDAOLogicV1.sol#L319-L320](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319-L320)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07i] [NounsDAOLogicV1.sol#L281-L282](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L282)
```solidity

        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
###### [G-07j] [NounsDAOLogicV1.sol#L230-L235](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L230-L235)
```solidity

newProposal.forVotes = 0;
newProposal.againstVotes = 0;
newProposal.abstainVotes = 0;
newProposal.canceled = false;
newProposal.executed = false;
newProposal.vetoed = false;
```
### [G-08] `require()`/`revert()` checks used multiples times should be turned into a function or modifier
Doing so increases code readability decreases number of instructions for the compiler.
*4 instances of this issue have been found:*
###### [G-08] [NounsDAOLogicV2.sol#L801](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L801)
```solidity

require(msg.sender == admin -> repeated 8 times
```
###### [G-08b] [NounsDAOLogicV2.sol#L853](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L853)
```solidity

require(msg.sender == vetoer -> repeated 3 times
```
###### [G-08c] [NounsDAOLogicV1.sol#L365](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L365)
```solidity

require(msg.sender == vetoer -> repeated 3 times
```
###### [G-08d] [NounsDAOLogicV1.sol#L599](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L599)
```solidity

require(msg.sender == admin -> repeated 6 times
```
### [G-09] String longer than 32 bytes cost more gas
" Each 32 byte chunk of the string requires an extra mstore. That is, additional cost for mstore, memory expansion costs, as well as stack operations. Note that, this runtime cost is only relevant when the revert condition is met."
[Source](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings)
*64 instances of this issue have been found:*
###### [G-09] [NounsDAOProxy.sol#L80-L81](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L80-L81)
```solidity

        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

```
###### [G-09b] [NounsDAOProxy.sol#L79-L80](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L79-L80)
```solidity

        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

```
###### [G-09c] [ERC721Enumerable.sol#L77-L78](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L77-L78)
```solidity

        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

```
###### [G-09d] [ERC721Enumerable.sol#L62-L63](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L62-L63)
```solidity

        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

```
###### [G-09e] [ERC721Checkpointable.sol#L292-L293](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L292-L293)
```solidity

 "ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits"
        );
```
###### [G-09f] [ERC721Checkpointable.sol#L277-L278](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L277-L278)
```solidity

  "ERC721Checkpointable::_moveDelegates: amount overflows"
                );
```
###### [G-09g] [ERC721Checkpointable.sol#L264-L265](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L264-L265)
```solidity

   "ERC721Checkpointable::_moveDelegates: amount underflows"
                );
```
###### [G-09h] [ERC721Checkpointable.sol#L201-L204](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L201-L204)
```solidity

require(
            blockNumber < block.number,
            "ERC721Checkpointable::getPriorVotes: not yet determined"
        );
```
###### [G-09i] [ERC721Checkpointable.sol#L163-L174](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L163-L174)
```solidity

require(
            signatory != address(0),
            "ERC721Checkpointable::delegateBySig: invalid signature"
        );
        require(
            nonce == nonces[signatory]++,
            "ERC721Checkpointable::delegateBySig: invalid nonce"
        );
        require(
            block.timestamp <= expiry,
            "ERC721Checkpointable::delegateBySig: signature expired"
        );
```
###### [G-09j] [ERC721Checkpointable.sol#L91-L94](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L91-L94)
```solidity

safe96(
                balanceOf(delegator),
                "ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits"
            );
```
###### [G-09k] [NounsDAOLogicV2.sol#L923-L924](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L923-L924)
```solidity

        uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');

```
###### [G-09l] [NounsDAOLogicV2.sol#L853-L854](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L853-L854)
```solidity

        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
###### [G-09m] [NounsDAOLogicV2.sol#L840-L841](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L840-L841)
```solidity

        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

```
###### [G-09n] [NounsDAOLogicV2.sol#L819-L820](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L819-L820)
```solidity

        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
###### [G-09o] [NounsDAOLogicV2.sol#L801-L802](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L801-L802)
```solidity

        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

```
###### [G-09p] [NounsDAOLogicV2.sol#L727-L728](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L727-L728)
```solidity

        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

```
###### [G-09q] [NounsDAOLogicV2.sol#L702-L703](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L702-L703)
```solidity

        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

```
###### [G-09r] [NounsDAOLogicV2.sol#L705-L712](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L705-L712)
```solidity

require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
        require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );
```
###### [G-09s] [NounsDAOLogicV2.sol#L674-L685](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L674-L685)
```solidity

require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
        require(
            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );
```
###### [G-09t] [NounsDAOLogicV2.sol#L655-L660](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L655-L660)
```solidity

require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
```
###### [G-09u] [NounsDAOLogicV2.sol#L638-L642](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L638-L642)
```solidity

require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
```
###### [G-09v] [NounsDAOLogicV2.sol#L623-L626](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L623-L626)
```solidity

require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
```
###### [G-09w] [NounsDAOLogicV2.sol#L622-L623](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L622-L623)
```solidity

        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

```
###### [G-09x] [NounsDAOLogicV2.sol#L593-L594](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L593-L594)
```solidity

require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
```
###### [G-09y] [NounsDAOLogicV2.sol#L597-L598](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L597-L598)
```solidity

        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```
###### [G-09z] [NounsDAOLogicV2.sol#L433-L434](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L433-L434)
```solidity

        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

```
###### [G-09{] [NounsDAOLogicV2.sol#L577-L578](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L577-L578)
```solidity

        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

```
###### [G-09|] [NounsDAOLogicV2.sol#L375-L376](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L375-L376)
```solidity

        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

```
###### [G-09}] [NounsDAOLogicV2.sol#L377-L378](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L377-L378)
```solidity

        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

```
###### [G-09~] [NounsDAOLogicV2.sol#L347-L348](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L347-L348)
```solidity

        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

```
###### [G-09] [NounsDAOLogicV2.sol#L312-L315](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L312-L315)
```solidity

require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );
```
###### [G-09] [NounsDAOLogicV2.sol#L286-L289](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L286-L289)
```solidity

require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );
```
###### [G-09] [NounsDAOLogicV2.sol#L213-L220](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L213-L220)
```solidity

require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );
```
###### [G-09] [NounsDAOLogicV2.sol#L197-L209](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L197-L209)
```solidity

require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
        require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

```
###### [G-09] [NounsDAOLogicV2.sol#L133-L134](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L133-L134)
```solidity

        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```
###### [G-09] [NounsDAOLogicV2.sol#L135-L148](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L135-L148)
```solidity

require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );
```
###### [G-09] [NounsDAOLogicV2.sol#L105-L106](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L105-L106)
```solidity

    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
###### [G-09] [NounsDAOLogicV2.sol#L102-L103](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L102-L103)
```solidity

        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

```
###### [G-09] [NounsDAOLogicV1.sol#L651-L652](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L651-L652)
```solidity

        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
###### [G-09] [NounsDAOLogicV1.sol#L638-L639](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L638-L639)
```solidity

        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

```
###### [G-09] [NounsDAOLogicV1.sol#L617-L618](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L617-L618)
```solidity

        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
###### [G-09] [NounsDAOLogicV1.sol#L599-L600](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L599-L600)
```solidity

        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

```
###### [G-09] [NounsDAOLogicV1.sol#L581-L585](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L581-L585)
```solidity

require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
        require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L563-L568](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L563-L568)
```solidity

require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L546-L550](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L546-L550)
```solidity

require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L530-L534](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L530-L534)
```solidity

require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L505-L506](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L505-L506)
```solidity

        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```
###### [G-09] [NounsDAOLogicV1.sol#L501-L502](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L501-L502)
```solidity

        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
```
###### [G-09] [NounsDAOLogicV1.sol#L485-L486](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L485-L486)
```solidity

        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

```
###### [G-09] [NounsDAOLogicV1.sol#L422-L423](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L422-L423)
```solidity

        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

```
###### [G-09] [NounsDAOLogicV1.sol#L366-L367](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L366-L367)
```solidity

        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

```
###### [G-09] [NounsDAOLogicV1.sol#L364-L365](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L364-L365)
```solidity

        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

```
###### [G-09] [NounsDAOLogicV1.sol#L339-L343](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L339-L343)
```solidity

require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L336-L337](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L336-L337)
```solidity

        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

```
###### [G-09] [NounsDAOLogicV1.sol#L313-L316](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L313-L316)
```solidity

require(
            state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L301-L304](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L301-L304)
```solidity

require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L275-L278](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L275-L278)
```solidity

require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L203-L210](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L203-L210)
```solidity

require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );
```
###### [G-09] [NounsDAOLogicV1.sol#L197-L198](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L197-L198)
```solidity

        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```
###### [G-09] [NounsDAOLogicV1.sol#L191-L196](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L191-L196)
```solidity

    require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L187-L190](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L187-L190)
```solidity

   require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L124-L141](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L124-L141)
```solidity

require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
        require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
```
###### [G-09] [NounsDAOLogicV1.sol#L122-L123](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L122-L123)
```solidity

        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```
###### [G-09 ] [NounsDAOLogicV1.sol#L101-L102](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L101-L102)
```solidity

    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
### [G-10] `bool` is gas inefficient when used in storage
Instead use `uint256` values to represent true/false instead.
[Reference](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27)
*5 instances of this issue have been found:*
###### [G-10] [NounsDAOInterfaces.sol#L390-L394](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L390-L394)
```solidity

bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
```
###### [G-10b] [NounsDAOInterfaces.sol#L320-L321](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L320-L321)
```solidity

        bool hasVoted;

```
###### [G-10c] [NounsDAOInterfaces.sol#L304-L308](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L304-L308)
```solidity

bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
```
###### [G-10d] [NounsDAOInterfaces.sol#L215-L220](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L215-L220)
```solidity

/// @notice Whether or not a vote has been cast
        bool hasVoted;
        /// @notice Whether or not the voter supports the proposal or abstains
        uint8 support;
        /// @notice The number of votes the voter had, which were cast
        uint96 votes;
```
###### [G-10e] [NounsDAOInterfaces.sol#L204-L208](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L204-L208)
```solidity

bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
```
