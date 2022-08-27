# QA

## Non-critical

### [N-01] Same variable with different names across different functions

Consider renaming `pos` in `NounsDAOLogicV2._writeQuorumParamsCheckpoint` to `len` in order to have the same notation as in `NounsDAOLogicV2.getDynamicQuorumParamsAt`.

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L966

```solidity
        uint256 pos = quorumParamsCheckpoints.length;
```

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L924

```solidity
       uint256 len = quorumParamsCheckpoints.length;
```

### [N-02] Incomplete error message

Following the standard that have been used for the rest of the code `safe32(block.number, 'block number exceeds 32 bits');` error message should be : `safe32(block.number, 'NounsDAO::_writeQuorumParamsCheckpoint: block number exceeds 32 bits');` in `_writeQuorumParamsCheckpoint`.

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L965

```solidity
        uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
```
### [N-03] `receipt.hasVoted == false` can be refactor to `!receipt.hasVoted` to improve readability

In `NounsDAOLogicV1.sol` and `NounsDAOLogicV2.sol` :
`require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');` could be written `require(!receipt.hasVoted, 'NounsDAO::castVoteInternal: voter already voted');`

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597

```solidity
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```
File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505

```solidity
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

### [N-04] Floating pragma used in implementation contract

Implementation contracts should not use a floating pragma in order to ensure that the code has been tested and deployed with the same version.

`NounsDAOLogicV1.sol` and `NounsDAOLogicV2.sol` should not have a floating pragma.

### [N-05] `NounsDAOLogicV2.sol` does not compile for version 0.8.6

`NounsDAOLogicV2.sol` does not compile for compiler version 0.8.6 because block.basefee has been added in version 0.8.7.

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L980

```solidity
         uint256 gasPrice = min(tx.gasprice, block.basefee + MAX_REFUND_PRIORITY_FEE);
```


## Low severity

### [L-01] Wrong comments

In `NounsDAOLogicV1.sol` and `NounsDAOLogicV2.sol` the comment  `// Check caller is pendingAdmin and pendingAdmin ≠ address(0)` is incorrect. It should be `// Check if caller is vetoer`.

File : https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L851-L853


```solidity
   function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

File : 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L649-L651

```solidity
   function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```



In `NounsDAOLogicV2.sol` the comment `// 4,000 basis points or 60%` is incorrect and should be `// 6,000 basis points or 60%` instead.

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86

```solidity
       uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```
