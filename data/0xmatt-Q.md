## Vulnerability Details

## Low Findings

### L-01 Missing zero checks in constructor

#### Vulnerability Details

The [NounsDAOProxy.sol constructor](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L41-L72) does not correctly validate passed parameters. This could allow admin_ to be set to address(0), or invalid values could be set for timelock_, nouns_, vetoer_, votingPeriod_, votingDelay_, proposalThresholdBPS_, and quorumVotesBPS_.

The contract should implement bounds checks for key variables. Values such as address(0) or unit256(0) should be rejected. The proposalThresholdBPS_ variable should be checked to ensure it's equal to or greater than MIN_PROPOSAL_THRESHOLD_BPS and is below or equal to MAX_PROPOSAL_THRESHOLD_BPS as defined in [NounsDAOLogicv1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L70-L73) and [NounsDaoLogicv2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L62-L65).

This will reduce the chance of risk through error or omission of parameter values at construction.

Please note that while not checked in the constructor, the implementation_ parameter is checked in the [_setImplementation()](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L78-L86) call later.

#### Other instances

Although technically not listed as in scope this also applies to [NounsDaoProxyv2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxyV2.sol#L44-L54) with the shift from quorumVoteBPS_ to dynamicQuorumParams_.
