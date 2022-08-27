* NounsDAOLogicV2.sol::86 Comment states 4_000 basis points but it’s really 6_000

* NounsDAOLogicV2.sol::263 Comment states minQuorumVotes is always zero but that doesn’t seem to be the case

Internal functions should begin with a preceding `_`:

- NounsDAOLogicV1::castVoteInternal
- NounsDAOLogicV1::bps2Uint
- NounsDAOLogicV1::getChainIdInternal
- NounsDAOLogicV2::queueOrRevertInternal
- NounsDAOLogicV2::castRefundableVoteInternal
- NounsDAOLogicV2::castVoteInternal
- NounsDAOLogicV2::proposalCreationBlock
- NounsDAOLogicV2::min
- NounsDAOLogicV2::bps2Uint
- NounsDAOLogicV2::getChainIdInternal
- NounsDAOLogicV2::safe32
- NounsDAOLogicV2::safe16

External functions should not follow internal naming conventions:

- NounsDAOLogicV1::_setVotingDelay
- NounsDAOLogicV1::_setVotingPeriod
- NounsDAOLogicV1::_setProposalThresholdBPS
- NounsDAOLogicV1::_setQuorumVotesBPS
- NounsDAOLogicV1::_setPendingAdmin
- NounsDAOLogicV1::_acceptAdmin
- NounsDAOLogicV1::_setVetoer
- NounsDAOLogicV1::_burnVetoPower
- NounsDAOLogicV2::_setVotingDelay
- NounsDAOLogicV2::_setVotingPeriod
- NounsDAOLogicV2::_setProposalThresholdBPS
- NounsDAOLogicV2::_setMinQuorumVotesBPS
- NounsDAOLogicV2::_setMaxQuorumVotesBPS
- NounsDAOLogicV2::_setQuorumCoefficient
- NounsDAOLogicV2::_setDynamicQuorumParams
- NounsDAOLogicV2::_withdraw
- NounsDAOLogicV2::_setPendingAdmin
- NounsDAOLogicV2::_acceptAdmin
- NounsDAOLogicV2::_setVetoer
- NounsDAOLogicV2::_burnVetoPower


Redundant boolean checks (the variable being checked is a boolean value itself, no need for comparison):

- NounsDAOLogicV1::505
- NounsDAOLogicV2::597

* Typo: NounsDAOLogicV1::46 `veteor`

* NounsDAOLogicV1.sol Can make an `onlyVetoer` modifier to avoid repeating require statements

* NounsDAOLogicV2.sol can replace all of the `require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');`
statements with the defined `AdminOnly` error or make a modifier to de-duplicate this logic.

* NounsDAOLogicV2.sol::89 `MAX_QUORUM_VOTES_BPS` is unused

Functions can be declared external:

- NounsDAOLogicV1::initialize
- NounsDAOLogicV1::propose
- NounsDAOLogicV1::_burnVetoPower
- NounsDAOLogicV1::proposalThreshold
- NounsDAOLogicV1::quorumVotes
- NounsDAOLogicV1::setVetoer
- NounsDAOLogicV2::initialize
- NounsDAOLogicV2::propose
- NounsDAOLogicV2::_burnVetoPower
- NounsDAOLogicV2::proposalThreshold
- NounsDAOLogicV2::quorumVotes
- NounsDAOLogicV2::setVetoer
- NounsDAOLogicV2::setVetoer
- ERC721CheckPointable::delegate


Centralization risk for the DAO logic V1 and V2:

* as soon as the lower bound for the required votes is reached, the executor may set the required quorum to this lower bound and immediately pass a vote.


