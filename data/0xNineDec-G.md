## G-1 Return values of in-house created safe math ops can be unchecked

The usage of self made functions that check over-underflows as for solidity 0.8.x is pointless because the compiler comes with built in over-underflow checks. If those functions are meant to be implemented besides the fact of having built-in checks, unchecking their returns remove this built-in check and saves gas. Unchecked can be used because over/underflow is checked with require statements before performing the math op.


## G-2 Constants that are `private` instead of `public` save gas

Because constant variables should be declared an initialized as a one-liner, their value could be easily retrieved by reading directly the source code. Removing the `public` modifier and using `private` instead, does not create a function getter for the public constant saving around `3500 gas` on deployment.

_Found 23 times:_

    NounsDAOLogicV1.sol 
    /// @notice The minimum setable proposal threshold
    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

    /// @notice The maximum setable proposal threshold
    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

    /// @notice The minimum setable voting period
    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 1;

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

    /// @notice The minimum setable quorum votes basis points
    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%

    /// @notice The maximum setable quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

    /// @notice The maximum number of actions that can be included in a proposal
    uint256 public constant proposalMaxOperations = 10; // 10 actions


    NounsDAOLogicV2.sol 
    /// @notice The name of this contract
    string public constant name = 'Nouns DAO';

    /// @notice The minimum setable proposal threshold
    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

    /// @notice The maximum setable proposal threshold
    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

    /// @notice The minimum setable voting period
    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 1;

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

    /// @notice The lower bound of minimum quorum votes basis points
    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

    /// @notice The upper bound of minimum quorum votes basis points
    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

    /// @notice The upper bound of maximum quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

    /// @notice The maximum setable quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

    /// @notice The maximum number of actions that can be included in a proposal
    uint256 public constant proposalMaxOperations = 10; // 10 actions

    /// @notice The maximum priority fee used to cap gas refunds in `castRefundableVote`
    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

    /// @notice The vote refund gas overhead, including 7K for ETH transfer and 29K for general transaction overhead
    uint256 public constant REFUND_BASE_GAS = 36000;


## G-3 Use custom errors instead of revert strings

Custom errors available from `0.8.4` provide a clear and cheaper way of reverting saving around `50 gas` per reversal. Also they provide a cleaner feedback to the user if defined properly in a descriptive way. They can be triggered inside an `if statement`.


## G-4 Revert strings that exceed 32 bytes use more slots

Each memory slot has 32 bytes. If a revert string size exceeds this limit, more slots will be used incurring in an `MSTORE` (adding up `3 gas` consumption). It is advised also using custom errors as for solidity 0.8.x which provide a clear and cheap way of reverting.

_Found 47 times:_

    NounsDAOLogicV1.sol 
    'NounsDAO::propose: proposer votes below proposal threshold'
    'NounsDAO::propose: proposal function information arity mismatch'
    'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
    'NounsDAO::queue: proposal can only be queued if it is succeeded'
    'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
    'NounsDAO::execute: proposal can only be executed if it is queued'
    'NounsDAO::cancel: cannot cancel executed proposal'
    'NounsDAO::veto: cannot veto executed proposal'
    'NounsDAO::state: invalid proposal id'
    'NounsDAO::castVoteInternal: voting is closed'
    'NounsDAO::castVoteInternal: invalid vote type'
    'NounsDAO::castVoteInternal: voter already voted'
    'NounsDAO::_setVotingDelay: admin only'
    'NounsDAO::_setVotingPeriod: admin only'
    'NounsDAO::_setVotingPeriod: invalid voting period'
    'NounsDAO::castVoteBySig: invalid signature'
    'NounsDAO::_setProposalThresholdBPS: admin only'
    'NounsDAO::_setProposalThreshold: invalid proposal threshold'
    'NounsDAO::_setPendingAdmin: admin only'
    'NounsDAO::_acceptAdmin: pending admin only'
    'NounsDAO::_setVetoer: vetoer only'
    'NounsDAO::_burnVetoPower: vetoer only'

    NounsDAOLogicV2.sol 
    'NounsDAO::propose: proposer votes below proposal threshold'
    'NounsDAO::propose: proposal function information arity mismatch'
    'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
    'NounsDAO::queue: proposal can only be queued if it is succeeded'
    'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
    'NounsDAO::execute: proposal can only be executed if it is queued'
    'NounsDAO::cancel: cannot cancel executed proposal'
    'NounsDAO::veto: cannot veto executed proposal'
    'NounsDAO::veto: veto power burned'
    'NounsDAO::state: invalid proposal id'
    'NounsDAO::castVoteInternal: voting is closed'
    'NounsDAO::castVoteInternal: invalid vote type'
    'NounsDAO::castVoteInternal: voter already voted'
    'NounsDAO::_setVotingDelay: admin only'
    'NounsDAO::_setVotingPeriod: admin only'
    'NounsDAO::_setVotingPeriod: invalid voting period'
    'NounsDAO::castVoteBySig: invalid signature'
    'NounsDAO::_setProposalThresholdBPS: admin only'
    'NounsDAO::_setProposalThreshold: invalid proposal threshold'
    'NounsDAO::_setPendingAdmin: admin only'
    'NounsDAO::_acceptAdmin: pending admin only'
    'NounsDAO::_setVetoer: vetoer only'
    'NounsDAO::_burnVetoPower: vetoer only'
    'NounsDAO::_setQuorumCoefficient: admin only'
    'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits'

