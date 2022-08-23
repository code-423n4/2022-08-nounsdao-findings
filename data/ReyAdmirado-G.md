## 1. expressions for constant values such as a call to `keccak256()`, should use immutable rather than constant

- [ERC721Checkpointable.sol#L59](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59)
- [ERC721Checkpointable.sol#L63](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L63)

- [NounsDAOLogicV1.sol#L97](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L97)
- [NounsDAOLogicV1.sol#L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L101)

- [NounsDAOLogicV2.sol#L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L101)
- [NounsDAOLogicV2.sol#L105](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L105)


## 2. using argument instead of StateVar saves gas

use `newVotingDelay` instead of `votingDelay` in this line can save gas

- [NounsDAOLogicV1.sol#L538](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L538)

use `newVotingPeriod` instead of `votingPeriod` in this line can save gas

- [NounsDAOLogicV1.sol#L554](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L554)

use `newProposalThresholdBPS` instead of `proposalThresholdBPS` in this line can save gas

- [NounsDAOLogicV1.sol#L572](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L572)

use `newQuorumVotesBPS` instead of `quorumVotesBPS` in this line can save gas

- [NounsDAOLogicV1.sol#L589](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L589)

use `newVotingDelay` instead of `votingDelay` in this line can save gas

- [NounsDAOLogicV2.sol#L630](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L630)

use `newVotingPeriod` instead of `votingPeriod` in this line can save gas

- [NounsDAOLogicV2.sol#L646](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L646)

use `newProposalThresholdBPS` instead of `proposalThresholdBPS` in this line can save gas

- [NounsDAOLogicV2.sol#L664](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L664)

use `implementation_` instead of `implementation` in this line can save gas

- [NounsDAOProxy.sol#L85](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L85)


## 3. use stack variables instead of state variables

use `oldPendingAdmin` instead of `pendingAdmin` in this line can save gas

- [NounsDAOLogicV1.sol#L624](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L624)

use `oldPendingAdmin` instead of `admin` in this line can save gas

- [NounsDAOLogicV1.sol#L629](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L629)

use `oldPendingAdmin` instead of `pendingAdmin` in this line can save gas

- [NounsDAOLogicV2.sol#L826](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L826)

use `oldPendingAdmin` instead of `admin` in this line can save gas

- [NounsDAOLogicV2.sol#L831](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L831)


## 4. state variables should be cached in stack variables rather than re-reading them from storage

before line 227 `proposalCount` should be cached, and it can be used in line 247 instead of `newProposal.id` to save gas:

- [NounsDAOLogicV2.sol#L227](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L227)
- [NounsDAOLogicV2.sol#L247](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L247)


## 5. not using the named return variables when a function returns, wastes deployment gas

- [NounsDAOLogicV1.sol#L395](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L395)

- [NounsDAOLogicV2.sol#L406](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L406)


## 6. `<array>.length` should not be looked up in every loop of a for-loop

This reduce gas cost as show here https://forum.openzeppelin.com/t/a-collection-of-gas-optimisation-tricks/19966/5

- [NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)
- [NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)
- [NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)
- [NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

- [NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
- [NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
- [NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
- [NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)


## 7. `++i` costs less gas than `i++`, especially when it’s used in for-loops (--i/i-- too)

- [ERC721Checkpointable.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L141)

- [NounsDAOLogicV1.sol#L216](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L216)
- [NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)
- [NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)
- [NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)
- [NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

- [NounsDAOLogicV2.sol#L226](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226)
- [NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
- [NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
- [NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
- [NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)


## 8. it costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied

- [ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)
- [ERC721Checkpointable.sol#L181](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181)

- [NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)
- [NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)
- [NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)
- [NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

- [NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
- [NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
- [NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
- [NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)
- [NounsDAOLogicV2.sol#L948](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L948)


## 9. ` ++i/i++` should be `unchecked{++i}/unchecked{i++}` when it is not possible for them to overflow, as is the case when used in for-loop and while-loops

- [ERC721Checkpointable.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L141)

- [NounsDAOLogicV1.sol#L216](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L216)
- [NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)
- [NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)
- [NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)
- [NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

- [NounsDAOLogicV2.sol#L226](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226)
- [NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
- [NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
- [NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
- [NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)


## 10. require()/revert() strings longer than 32 bytes cost extra gas

- [ERC721Checkpointable.sol#L140](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140)
- [ERC721Checkpointable.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L141)
- [ERC721Checkpointable.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L142)
- [ERC721Checkpointable.sol#L164](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L164)

- [ERC721Enumerable.sol#L62](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L62)
- [ERC721Enumerable.sol#L77](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L77)

- [NounsDAOLogicV1.sol#L122](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L122)
- [NounsDAOLogicV1.sol#L124](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124)
- [NounsDAOLogicV1.sol#L125](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L125)
- [NounsDAOLogicV1.sol#L126](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126)
- [NounsDAOLogicV1.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130)
- [NounsDAOLogicV1.sol#L134](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134)
- [NounsDAOLogicV1.sol#L138](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138)
- [NounsDAOLogicV1.sol#L187](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L187)
- [NounsDAOLogicV1.sol#L191](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191)
- [NounsDAOLogicV1.sol#L197](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L197)
- [NounsDAOLogicV1.sol#L198](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L198)
- [NounsDAOLogicV1.sol#L203](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L203)
- [NounsDAOLogicV1.sol#L207](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L207)
- [NounsDAOLogicV1.sol#L275](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L275)
- [NounsDAOLogicV1.sol#L301](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L301)
- [NounsDAOLogicV1.sol#L313](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L313)
- [NounsDAOLogicV1.sol#L336](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L336)
- [NounsDAOLogicV1.sol#L342](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L342)
- [NounsDAOLogicV1.sol#L364](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L364)
- [NounsDAOLogicV1.sol#L366](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L366)
- [NounsDAOLogicV1.sol#L422](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L422)
- [NounsDAOLogicV1.sol#L485](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L485)
- [NounsDAOLogicV1.sol#L501](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L501)
- [NounsDAOLogicV1.sol#L502](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L502)
- [NounsDAOLogicV1.sol#L505](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505)
- [NounsDAOLogicV1.sol#L530](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L530)
- [NounsDAOLogicV1.sol#L531](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531)
- [NounsDAOLogicV1.sol#L546](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L546)
- [NounsDAOLogicV1.sol#L547](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547)
- [NounsDAOLogicV1.sol#L563](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L563)
- [NounsDAOLogicV1.sol#L564](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564)
- [NounsDAOLogicV1.sol#L581](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L581)
- [NounsDAOLogicV1.sol#L582](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582)
- [NounsDAOLogicV1.sol#L599](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L599)
- [NounsDAOLogicV1.sol#L617](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)
- [NounsDAOLogicV1.sol#L638](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L638)
- [NounsDAOLogicV1.sol#L651](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L651)

- [NounsDAOLogicV2.sol#L133](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L133)
- [NounsDAOLogicV2.sol#L135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135)
- [NounsDAOLogicV2.sol#L136](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L136)
- [NounsDAOLogicV2.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137)
- [NounsDAOLogicV2.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141)
- [NounsDAOLogicV2.sol#L145](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145)
- [NounsDAOLogicV2.sol#L197](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L197)
- [NounsDAOLogicV2.sol#L201](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201)
- [NounsDAOLogicV2.sol#L207](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L207)
- [NounsDAOLogicV2.sol#L208](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L208)
- [NounsDAOLogicV2.sol#L213](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L213)
- [NounsDAOLogicV2.sol#L217](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L217)
- [NounsDAOLogicV2.sol#L286](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L286)
- [NounsDAOLogicV2.sol#L312](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L312)
- [NounsDAOLogicV2.sol#L324](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L324)
- [NounsDAOLogicV2.sol#L347](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347)
- [NounsDAOLogicV2.sol#L350](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L350)
- [NounsDAOLogicV2.sol#L375](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375)
- [NounsDAOLogicV2.sol#L377](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377)
- [NounsDAOLogicV2.sol#L433](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L433)
- [NounsDAOLogicV2.sol#L577](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L577)
- [NounsDAOLogicV2.sol#L593](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L593)
- [NounsDAOLogicV2.sol#L594](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L594)
- [NounsDAOLogicV2.sol#L597](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597)
- [NounsDAOLogicV2.sol#L622](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L622)
- [NounsDAOLogicV2.sol#L623](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623)
- [NounsDAOLogicV2.sol#L638](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L638)
- [NounsDAOLogicV2.sol#L639](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639)
- [NounsDAOLogicV2.sol#L655](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L655)
- [NounsDAOLogicV2.sol#L656](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656)
- [NounsDAOLogicV2.sol#L674](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L674)
- [NounsDAOLogicV2.sol#L677](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677)
- [NounsDAOLogicV2.sol#L682](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L682)
- [NounsDAOLogicV2.sol#L702](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L702)
- [NounsDAOLogicV2.sol#L705](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L705)
- [NounsDAOLogicV2.sol#L709](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L709)
- [NounsDAOLogicV2.sol#L727](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L727)
- [NounsDAOLogicV2.sol#L801](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L801)
- [NounsDAOLogicV2.sol#L819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)
- [NounsDAOLogicV2.sol#L840](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L840)
- [NounsDAOLogicV2.sol#L853](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L853)

- [NounsDAOProxy.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79)
- [NounsDAOProxy.sol#L80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L80)


## 11. splitting require() statements that use `&&` saves gas

- [NounsDAOLogicV1.sol#L126](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126)
- [NounsDAOLogicV1.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130)
- [NounsDAOLogicV1.sol#L134](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134)
- [NounsDAOLogicV1.sol#L138](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138)
- [NounsDAOLogicV1.sol#L191](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191)
- [NounsDAOLogicV1.sol#L531](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531)
- [NounsDAOLogicV1.sol#L547](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547)
- [NounsDAOLogicV1.sol#L564](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564)
- [NounsDAOLogicV1.sol#L582](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582)
- [NounsDAOLogicV1.sol#L617](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)

- [NounsDAOLogicV2.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137)
- [NounsDAOLogicV2.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141)
- [NounsDAOLogicV2.sol#L145](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145)
- [NounsDAOLogicV2.sol#L201](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201)
- [NounsDAOLogicV2.sol#L623](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623)
- [NounsDAOLogicV2.sol#L639](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639)
- [NounsDAOLogicV2.sol#L656](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656)
- [NounsDAOLogicV2.sol#L677](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677)
- [NounsDAOLogicV2.sol#L819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)


## 12. use custom errors rather than revert()/require() strings to save deployment gas

https://blog.soliditylang.org/2021/04/21/custom-errors/

- [ERC721Checkpointable.sol#L140](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140)
- [ERC721Checkpointable.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L141)
- [ERC721Checkpointable.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L142)
- [ERC721Checkpointable.sol#L164](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L164)

- [ERC721Enumerable.sol#L62](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L62)
- [ERC721Enumerable.sol#L77](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L77)

- [NounsDAOLogicV1.sol#L122](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L122)
- [NounsDAOLogicV1.sol#L124](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124)
- [NounsDAOLogicV1.sol#L125](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L125)
- [NounsDAOLogicV1.sol#L126](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126)
- [NounsDAOLogicV1.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130)
- [NounsDAOLogicV1.sol#L134](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134)
- [NounsDAOLogicV1.sol#L138](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138)
- [NounsDAOLogicV1.sol#L187](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L187)
- [NounsDAOLogicV1.sol#L191](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191)
- [NounsDAOLogicV1.sol#L197](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L197)
- [NounsDAOLogicV1.sol#L198](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L198)
- [NounsDAOLogicV1.sol#L203](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L203)
- [NounsDAOLogicV1.sol#L207](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L207)
- [NounsDAOLogicV1.sol#L275](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L275)
- [NounsDAOLogicV1.sol#L301](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L301)
- [NounsDAOLogicV1.sol#L313](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L313)
- [NounsDAOLogicV1.sol#L336](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L336)
- [NounsDAOLogicV1.sol#L342](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L342)
- [NounsDAOLogicV1.sol#L364](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L364)
- [NounsDAOLogicV1.sol#L366](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L366)
- [NounsDAOLogicV1.sol#L422](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L422)
- [NounsDAOLogicV1.sol#L485](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L485)
- [NounsDAOLogicV1.sol#L501](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L501)
- [NounsDAOLogicV1.sol#L502](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L502)
- [NounsDAOLogicV1.sol#L505](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505)
- [NounsDAOLogicV1.sol#L530](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L530)
- [NounsDAOLogicV1.sol#L531](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531)
- [NounsDAOLogicV1.sol#L546](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L546)
- [NounsDAOLogicV1.sol#L547](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547)
- [NounsDAOLogicV1.sol#L563](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L563)
- [NounsDAOLogicV1.sol#L564](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564)
- [NounsDAOLogicV1.sol#L581](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L581)
- [NounsDAOLogicV1.sol#L582](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582)
- [NounsDAOLogicV1.sol#L599](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L599)
- [NounsDAOLogicV1.sol#L617](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)
- [NounsDAOLogicV1.sol#L638](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L638)
- [NounsDAOLogicV1.sol#L651](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L651)

- [NounsDAOLogicV2.sol#L133](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L133)
- [NounsDAOLogicV2.sol#L135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135)
- [NounsDAOLogicV2.sol#L136](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L136)
- [NounsDAOLogicV2.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137)
- [NounsDAOLogicV2.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141)
- [NounsDAOLogicV2.sol#L145](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145)
- [NounsDAOLogicV2.sol#L197](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L197)
- [NounsDAOLogicV2.sol#L201](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201)
- [NounsDAOLogicV2.sol#L207](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L207)
- [NounsDAOLogicV2.sol#L208](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L208)
- [NounsDAOLogicV2.sol#L213](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L213)
- [NounsDAOLogicV2.sol#L217](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L217)
- [NounsDAOLogicV2.sol#L286](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L286)
- [NounsDAOLogicV2.sol#L312](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L312)
- [NounsDAOLogicV2.sol#L324](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L324)
- [NounsDAOLogicV2.sol#L347](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347)
- [NounsDAOLogicV2.sol#L350](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L350)
- [NounsDAOLogicV2.sol#L375](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375)
- [NounsDAOLogicV2.sol#L377](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377)
- [NounsDAOLogicV2.sol#L433](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L433)
- [NounsDAOLogicV2.sol#L577](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L577)
- [NounsDAOLogicV2.sol#L593](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L593)
- [NounsDAOLogicV2.sol#L594](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L594)
- [NounsDAOLogicV2.sol#L597](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597)
- [NounsDAOLogicV2.sol#L622](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L622)
- [NounsDAOLogicV2.sol#L623](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623)
- [NounsDAOLogicV2.sol#L638](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L638)
- [NounsDAOLogicV2.sol#L639](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639)
- [NounsDAOLogicV2.sol#L655](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L655)
- [NounsDAOLogicV2.sol#L656](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656)
- [NounsDAOLogicV2.sol#L674](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L674)
- [NounsDAOLogicV2.sol#L677](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677)
- [NounsDAOLogicV2.sol#L682](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L682)
- [NounsDAOLogicV2.sol#L702](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L702)
- [NounsDAOLogicV2.sol#L705](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L705)
- [NounsDAOLogicV2.sol#L709](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L709)
- [NounsDAOLogicV2.sol#L727](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L727)
- [NounsDAOLogicV2.sol#L801](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L801)
- [NounsDAOLogicV2.sol#L819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)
- [NounsDAOLogicV2.sol#L840](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L840)
- [NounsDAOLogicV2.sol#L853](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L853)

- [NounsDAOProxy.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79)
- [NounsDAOProxy.sol#L80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L80)


## 13. use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have `external` calls skip contract existence checks if the external call has a return value	
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions

and for `ERC721Enumerable.sol` file:

Use a solidity version of at least 0.8.2 to get compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings


## 14. using `bools` for storage incurs overhead

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

- [NounsDAOLogicV2.sol#L789](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L789)
- [NounsDAOLogicV2.sol#L983](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L983)

- [NounsDAOProxy.sol#L95](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L95)
- [NounsDAOProxy.sol#L110](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L110)


## 15. internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

- [ERC721Checkpointable.sol#L97](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L97)
- [ERC721Checkpointable.sol#L253](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L253)
- [ERC721Checkpointable.sol#L258](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L258)
- [ERC721Checkpointable.sol#L263](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L263)
- [ERC721Checkpointable.sol#L273](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L273)
- [ERC721Checkpointable.sol#L282](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L282)

- [ERC721Enumerable.sol#L96](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L96)

- [NounsDAOLogicV1.sol#L294](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L294)
- [NounsDAOLogicV1.sol#L676](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L676)

- [NounsDAOLogicV2.sol#L305](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L305)
- [NounsDAOLogicV2.sol#L866](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L866)
- [NounsDAOLogicV2.sol#L974](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L974)
- [NounsDAOLogicV2.sol#L1010](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1010)

- [NounsDAOProxy.sol#L94](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94)


## 16. abi.encode() is less efficient than abi.encodepacked()

- [ERC721Checkpointable.sol#L135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L135)
- [ERC721Checkpointable.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L137)

- [NounsDAOLogicV1.sol#L302](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L302)
- [NounsDAOLogicV1.sol#L480](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L480)
- [NounsDAOLogicV1.sol#L482](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L482)

- [NounsDAOLogicV2.sol#L313](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L313)
- [NounsDAOLogicV2.sol#L572](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L572)
- [NounsDAOLogicV2.sol#L574](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L574)


## 17. usage of uint/int smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Use a larger size then downcast where needed

- [ERC721Checkpointable.sol#L152](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L152)
- [ERC721Checkpointable.sol#L166](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L166)
- [ERC721Checkpointable.sol#L181](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181)
- [ERC721Checkpointable.sol#L182](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L182)
- [ERC721Checkpointable.sol#L184](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184)
- [ERC721Checkpointable.sol#L217](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L217)
- [ERC721Checkpointable.sol#L224](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L224)
- [ERC721Checkpointable.sol#L234](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L234)
- [ERC721Checkpointable.sol#L238](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L238)
- [ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)
- [ERC721Checkpointable.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L130)
- [ERC721Checkpointable.sol#L205](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L205)
- [ERC721Checkpointable.sol#L213](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L213)
- [ERC721Checkpointable.sol#L218](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L218)
- [ERC721Checkpointable.sol#L219](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L219)
- [ERC721Checkpointable.sol#L225](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L225)
- [ERC721Checkpointable.sol#L226](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L226)
- [ERC721Checkpointable.sol#L235](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L235)
- [ERC721Checkpointable.sol#L236](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L236)
- [ERC721Checkpointable.sol#L260](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L260)
- [ERC721Checkpointable.sol#L264](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L264)
- [ERC721Checkpointable.sol#L265](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L265)
- [ERC721Checkpointable.sol#L268](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L268)
- [ERC721Checkpointable.sol#L274](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L274)
- [ERC721Checkpointable.sol#L275](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L275)

- [NounsDAOLogicV1.sol#L450](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L450)
- [NounsDAOLogicV1.sol#L462](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L462)
- [NounsDAOLogicV1.sol#L474](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L474)
- [NounsDAOLogicV1.sol#L475](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L475)
- [NounsDAOLogicV1.sol#L499](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L499)
- [NounsDAOLogicV1.sol#L508](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L508)

- [NounsDAOLogicV2.sol#L673](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L673)
- [NounsDAOLogicV2.sol#L687](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L687)
- [NounsDAOLogicV2.sol#L701](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L701)
- [NounsDAOLogicV2.sol#L714](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L714)
- [NounsDAOLogicV2.sol#L749](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L749)
- [NounsDAOLogicV2.sol#L750](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L750)
- [NounsDAOLogicV2.sol#L1027](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1027)
- [NounsDAOLogicV2.sol#L726](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726)
- [NounsDAOLogicV2.sol#L730](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L730)
- [NounsDAOLogicV2.sol#L751](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L751)
- [NounsDAOLogicV2.sol#L923](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L923)
- [NounsDAOLogicV2.sol#L965](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L965)
- [NounsDAOLogicV2.sol#L1020](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1020)
- [NounsDAOLogicV2.sol#L489](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L489)
- [NounsDAOLogicV2.sol#L503](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L503)
- [NounsDAOLogicV2.sol#L520](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L520)
- [NounsDAOLogicV2.sol#L535](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L535)
- [NounsDAOLogicV2.sol#L554](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L554)
- [NounsDAOLogicV2.sol#L566](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L566)
- [NounsDAOLogicV2.sol#L567](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L567)
- [NounsDAOLogicV2.sol#L591](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L591)
- [NounsDAOLogicV2.sol#L539](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L539)
- [NounsDAOLogicV2.sol#L600](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L600)


## 18. using private rather than public for constants, saves gas

If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it’s used, and not adding another entry to the method ID table

- [ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)
- [ERC721Checkpointable.sol#L59](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59)
- [ERC721Checkpointable.sol#L63](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L63)

- [NounsDAOLogicV1.sol#L67](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67)
- [NounsDAOLogicV1.sol#L70](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L70)
- [NounsDAOLogicV1.sol#L73](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L73)
- [NounsDAOLogicV1.sol#L76](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L76)
- [NounsDAOLogicV1.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L79)
- [NounsDAOLogicV1.sol#L82](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L82)
- [NounsDAOLogicV1.sol#L85](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L85)
- [NounsDAOLogicV1.sol#L88](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L88)
- [NounsDAOLogicV1.sol#L91](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L91)
- [NounsDAOLogicV1.sol#L94](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L94)
- [NounsDAOLogicV1.sol#L97](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L97)
- [NounsDAOLogicV1.sol#L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L101)

- [NounsDAOLogicV2.sol#L59](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L59)
- [NounsDAOLogicV2.sol#L62](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L62)
- [NounsDAOLogicV2.sol#L65](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L65)
- [NounsDAOLogicV2.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L68)
- [NounsDAOLogicV2.sol#L71](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L71)
- [NounsDAOLogicV2.sol#L74](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L74)
- [NounsDAOLogicV2.sol#L77](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L77)
- [NounsDAOLogicV2.sol#L80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L80)
- [NounsDAOLogicV2.sol#L83](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L83)
- [NounsDAOLogicV2.sol#L86](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L86)
- [NounsDAOLogicV2.sol#L89](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L89)
- [NounsDAOLogicV2.sol#L92](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L92)
- [NounsDAOLogicV2.sol#L95](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L95)
- [NounsDAOLogicV2.sol#L98](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L98)
- [NounsDAOLogicV2.sol#L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L101)
- [NounsDAOLogicV2.sol#L105](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L105)
