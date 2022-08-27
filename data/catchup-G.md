

## G01 Saving SLOAD's
Below changes would save 18 SLOADs (each ~100 gas):

```NounsDAOLogicV2.sol```
- ```proposalCount``` is read twice. Cache to save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L227-L228
- replace ```votingDelay``` with ```newVotingDelay```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L630
- replace ```votingPeriod``` with ```newVotingPeriod```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L646
- replace ```proposalThresholdBPS``` with ```newProposalThresholdBPS```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L664
- ```pendingAdmin``` is read 3 times. Cache to save 2 SLOADs.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L823
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L826
- replace ```pendingAdmin``` with address(0). Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L832
- ```vetoer``` is read twice. Cache to save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L840
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L842

```NounsDAOLogicV1.sol```
- ```proposalCount``` is read twice. Cache to save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L217-L219
- replace ```votingDelay``` with ```newVotingDelay```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L538
- replace ```votingPeriod``` with ```newVotingPeriod```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L554
- replace ```proposalThresholdBPS``` with ```newProposalThresholdBPS```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L572
- replace ```quorumVotesBPS``` with ```newQuorumVotesBPS```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L589
- ```pendingAdmin``` is read 3 times. Cache to save 2 SLOADs.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L621
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L624
- replace ```pendingAdmin``` with address(0). Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L630
- ```vetoer``` is read twice. Cache to save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L638
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L640

```NounsDAOProxy.sol```
- replace ```implementation``` with ```implementation_```. Save 1 SLOAD.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L85




## G02 for loops can be optimized

1- For loop index increments can be made unchecked. There is no risk of overflowing the index of the for loops. Therefore, they can be changed to unchecked to save gas. This would save more gas as iterations in the loop increases.

2- Postfix increments can be changed as prefix as it is cheaper.

### Lines of code
There are 8 instances
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382




## G03 Redundant initialisation with default value

Some variables are initialised with their default values which cause unnecessary gas consumption

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L223
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L230-L235
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L231
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#LL238-L243




## G04 Error strings longer than 32 characters

Error strings take space in the deployed bytecode. Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.

### Lines of code
Most of the error strings of the require statements are longer than 32 characters. There are around 90 instances in the files within scope. Each instance would save 6 gas.

