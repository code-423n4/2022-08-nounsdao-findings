# Gas report

## [G-01] Caching length on `for` and use `unchecked` for increment

**In NounsDAOLogicV1.sol:** L281, L319, L346, L371
**In NounsDAOLogicV2.sol:** L292, L330, L357, L382

### Example:

From:
```solidity
for (uint256 i; i < array.length; i++) {
  ...
}
```
To:
```solidity
uint256 arrayLength = array.length;

for (uint256 i; i < arrayLength;) {
  ...
  unchecked {
    ++i;
  }
}
```

## [G-02] In NounsDAOLogicV1.sol#L617 and NounsDAOLogicV2.sol#L819: The `msg.sender != address(0)` it's redundant

## [G-03] The `id` in the `Proposal` and `ProposalCondensed` structs it's redundant

To simplify code and save gas, remove the id of the structs

In **NounsDAOInterfaces.sol** file remove the lines: [L175-L176](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L175-L176), [L275-L276](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L275-L276), [L369-L370](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L369-L370)

In **NounsDAOLogicV1** contract change [L216-L267](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L216-L267):
```diff
-       proposalCount++;
-       Proposal storage newProposal = proposals[proposalCount];
+       uint256 _proposalCount = ++proposalCount;
+       Proposal storage newProposal = proposals[_proposalCount];

-       newProposal.id = proposalCount;
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply);
        newProposal.eta = 0;
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;

-       latestProposalIds[newProposal.proposer] = newProposal.id;
+       latestProposalIds[newProposal.proposer] = _proposalCount;

        /// @notice Maintains backwards compatibility with GovernorBravo events
        emit ProposalCreated(
-           newProposal.id,
+           _proposalCount,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            description
        );

        /// @notice Updated event with `proposalThreshold` and `quorumVotes`
        emit ProposalCreatedWithRequirements(
-           newProposal.id,
+           _proposalCount,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            newProposal.proposalThreshold,
            newProposal.quorumVotes,
            description
        );

-       return newProposal.id;
+       return _proposalCount;
```

In **NounsDAOLogicV2** contract
  - Change [L226-L278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226-L278):
```diff
-       proposalCount++;
-       Proposal storage newProposal = _proposals[proposalCount];
-       newProposal.id = proposalCount;
+       uint256 _proposalCount = ++proposalCount;
+       Proposal storage newProposal = _proposals[_proposalCount];
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.eta = 0;
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
        newProposal.totalSupply = temp.totalSupply;
        newProposal.creationBlock = block.number;

-       latestProposalIds[newProposal.proposer] = newProposal.id;
+       latestProposalIds[newProposal.proposer] = _proposalCount;

        /// @notice Maintains backwards compatibility with GovernorBravo events
        emit ProposalCreated(
-           newProposal.id,
+           _proposalCount,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            description
        );

        /// @notice Updated event with `proposalThreshold` and `minQuorumVotes`
        /// @notice `minQuorumVotes` is always zero since V2 introduces dynamic quorum with checkpoints
        emit ProposalCreatedWithRequirements(
-           newProposal.id,
+           _proposalCount,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            newProposal.proposalThreshold,
            minQuorumVotes(),
            description
        );

-       return newProposal.id;
+       return _proposalCount;
```

  - Change [L443](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L443):
```diff
-        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {
+        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposalId)) {
```

  - Change [L466-L469](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L466-L469):
```diff
-               id: proposal.id,
                proposer: proposal.proposer,
                proposalThreshold: proposal.proposalThreshold,
-               quorumVotes: quorumVotes(proposal.id),
+               quorumVotes: quorumVotes(proposalId),
```

## [G-04] Use `implementation_` to save SLOAD gas

In [**NounsDAOProxy.sol#L85**](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L85) use `implementation_` instead of `implementation`:
```solidity
emit NewImplementation(oldImplementation, implementation_);
```