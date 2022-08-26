

## 1. Not to initialize uint variables to zero 

Uint variable's default value is zero. So, you can save gas by just defining uint variables.

### Proof of Concept

[`getPriorVotes()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L181)
[`queue()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)

### Recommended Mitigation Steps

You can just define `uint32 lower` without initializing it to zero. Also, there are many `for` loops in `NounsDAOLogicV2` and `NounsDAOLogicV1` contracts that you can just define `uint256 i` without initializing it.

## 2. Use unchecked block if possible

Use unchecked blocks for arithmetic operations that can't underflow/overflow. 


### Proof of Concept

[`queue()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)

### Recommended Mitigation Steps

You can put `++i` or `i++` in for loops in an unchecked block.

## 3. Use !=0 instead of > 0

!= 0 is a cheaper operation compared to > 0, when dealing with uint. > 0 can be replaced with != 0 for gas optimization.


### Proof of Concept

[`castRefundableVoteInternal()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L541)
[`_writeQuorumParamsCheckpoint()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L967)
[`getCurrentVotes()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L153)
[`_moveDelegates()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L215)
[`_moveDelegates()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L218)
[`_moveDelegates()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L225)
[`_writeCheckpoint()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L243)

### Recommended Mitigation Steps

Replace > 0 with != 0 when comparing unsigned integer variables to save gas.

## 4. Use custom errors instead of `require()` statements with revert strings.  

Using custom errors can save gas instead of using string errors.

### Proof of Concept

[`initialize()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133-L148)
[`propose()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197-L220)
[`tokenOfOwnerByIndex`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L62)
[`tokenByIndex`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L77)

### Recommended Mitigation Steps

Internal functions used once can be written inline to save gas.

## 5. Use inline code instead of writing functions.  

### Proof of Concept

[`safe32()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L253)
[`safe96()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L258)
[`add96()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L263)
[`sub96()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L273)
[`getChainId()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L282)
[`getChainIdInternal()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1010)
[`queueOrRevertInternal()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L305)
[`_refundGas()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L974)

### Recommended Mitigation Steps

Write `safe32()`, `safe96()`, `add96()`, `sub96` and `getChainId()` functions inside their calling functions.

## 6. Cache an array length in a memory variable to save gas  

`targets.length` is read multiple times, especially in loops, you should cache it in a memory variable to save gas.

### Proof of Concept

[`propose()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L202-L208)
[`queue()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)
[`execute()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330)
[`cancel()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357)
[`veto()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)

### Recommended Mitigation Steps

Store `targets.length` in a memory variable to save gas.

## 7. Use external visibility for uncalled public functions  

Public functions that are never called from within the contracts should be declared external to save gas. 

### Proof of Concept

[`delegate()`](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L112)

### Recommended Mitigation Steps

Change `delegate()` function visiblity from public to external.