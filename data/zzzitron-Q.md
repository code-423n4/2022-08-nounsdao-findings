# NounsDAO QA Report

## Summary

Risk | Title
---|---
L00 | one can set vetoer to zero address without using `_burnVetoPower` function
L01 | unused constant value from version 1
L02 | typo / misleading comments

## Low

### L00: one can set vetoer to zero address without using `_burnVetoPower` function

Although `_burnVetoPower` and `_setVetoer` are separated to prevent accidents of setting the vetoer to the zero address, the `_setVetoer` allows to set zero address as vetoer. So it does not prevent accidentally setting vetoer to zero address.

To prevent this, write an internal function with the current `_setVetoer` code and use it in the `_setVetoer` and `_burnVetoPower` function. And add zero address check in the `_setVetoer` function.

```solidity
// NounsDAOLogicV2.sol

 839     function _setVetoer(address newVetoer) public {
 840         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
 841
 842         emit NewVetoer(vetoer, newVetoer);
 843
 844         vetoer = newVetoer;
 845     }

 851     function _burnVetoPower() public {
 852         // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
 853         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
 854
 855         _setVetoer(address(0));
 856     }
```

### L01: unused constant value from version 1

The `MAX_QUORUM_VOTES_BPS` is not used in the NounsDAOLogicV2.
```
// NounsDAOLogicV2.sol
89     uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
```

### L02: typo / misleading comments

- The below comment does not match the code: the value is 6,000 basis points not 4,000 basis points.

```
// NounsDAOLogicV2.sol
86     uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```

- The comment below (line 263) says `minQuorumVotes` is always zero, but `minQuorumVotes()` is not always zero. (`newProposal.quorumVotes` is always zero). The `minQuorumVotes()` is using information from the current block, so it is not final: it can be overwritten.

```
// NounsDAOLogicV2.sol
 262         /// @notice Updated event with `proposalThreshold` and `minQuorumVotes`
 263         /// @notice `minQuorumVotes` is always zero since V2 introduces dynamic quorum with checkpoints
 264         emit ProposalCreatedWithRequirements(
 265             newProposal.id,
 266             msg.sender,
 267             targets,
 268             values,
 269             signatures,
 270             calldatas,
 271             newProposal.startBlock,
 272             newProposal.endBlock,
 273             newProposal.proposalThreshold,
 274             minQuorumVotes(),
 275             description
 276         );
```

<!-- zzzitron QA -->
