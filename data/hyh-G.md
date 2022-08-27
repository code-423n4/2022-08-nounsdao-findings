Excessive storage reads in configuration variables setters

## Proof of Concept

Config variable is read from storage extra time for events emission in 3 cases:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L627-L630

```solidity
        uint256 oldVotingDelay = votingDelay;
        votingDelay = newVotingDelay;

        emit VotingDelaySet(oldVotingDelay, votingDelay);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L643-L646

```solidity
        uint256 oldVotingPeriod = votingPeriod;
        votingPeriod = newVotingPeriod;

        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L661-L664

```solidity
        uint256 oldProposalThresholdBPS = proposalThresholdBPS;
        proposalThresholdBPS = newProposalThresholdBPS;

        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
```

## Recommended Mitigation Steps

Consider using memory variable with the same value each time:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L627-L630

```solidity
        uint256 oldVotingDelay = votingDelay;
        votingDelay = newVotingDelay;

-       emit VotingDelaySet(oldVotingDelay, votingDelay);
+       emit VotingDelaySet(oldVotingDelay, newVotingDelay);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L643-L646

```solidity
        uint256 oldVotingPeriod = votingPeriod;
        votingPeriod = newVotingPeriod;

-       emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
+       emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L661-L664

```solidity
        uint256 oldProposalThresholdBPS = proposalThresholdBPS;
        proposalThresholdBPS = newProposalThresholdBPS;

-       emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
+       emit ProposalThresholdBPSSet(oldProposalThresholdBPS, newProposalThresholdBPS);
```