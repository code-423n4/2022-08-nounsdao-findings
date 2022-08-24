## gas optimization
### G01: custom error save more gas
#### problem
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained https://blog.soliditylang.org/2021/04/21/custom-errors/. 
Custom errors are defined using the error statement.
#### prof

base/ERC721Checkpointable.sol, 140, b"        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');"
base/ERC721Checkpointable.sol, 141, b"        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');"
base/ERC721Checkpointable.sol, 142, b"        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');"
base/ERC721Checkpointable.sol, 164, b"        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');"
base/ERC721Enumerable.sol, 62, b"        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');"
base/ERC721Enumerable.sol, 77, b"        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');"
governance/NounsDAOLogicV1.sol, 122, b"        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');"
governance/NounsDAOLogicV1.sol, 123, b"        require(msg.sender == admin, 'NounsDAO::initialize: admin only');"
governance/NounsDAOLogicV1.sol, 124, b"        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');"
governance/NounsDAOLogicV1.sol, 125, b"        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');"
governance/NounsDAOLogicV1.sol, 129, b"        require(\n            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,\n            'NounsDAO::initialize: invalid voting period'\n        );"
governance/NounsDAOLogicV1.sol, 133, b"        require(\n            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,\n            'NounsDAO::initialize: invalid voting delay'\n        );"
governance/NounsDAOLogicV1.sol, 137, b"        require(\n            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::initialize: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 141, b"        require(\n            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,\n            'NounsDAO::initialize: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 190, b"        require(\n            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,\n            'NounsDAO::propose: proposer votes below proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 196, b"        require(\n            targets.length == values.length &&\n                targets.length == signatures.length &&\n                targets.length == calldatas.length,\n            'NounsDAO::propose: proposal function information arity mismatch'\n        );"
governance/NounsDAOLogicV1.sol, 197, b"        require(targets.length != 0, 'NounsDAO::propose: must provide actions');"
governance/NounsDAOLogicV1.sol, 198, b"        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');"
governance/NounsDAOLogicV1.sol, 206, b"            require(\n                proposersLatestProposalState != ProposalState.Active,\n                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'\n            );"
governance/NounsDAOLogicV1.sol, 210, b"            require(\n                proposersLatestProposalState != ProposalState.Pending,\n                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'\n            );"
governance/NounsDAOLogicV1.sol, 278, b"        require(\n            state(proposalId) == ProposalState.Succeeded,\n            'NounsDAO::queue: proposal can only be queued if it is succeeded'\n        );"
governance/NounsDAOLogicV1.sol, 304, b"        require(\n            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),\n            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'\n        );"
governance/NounsDAOLogicV1.sol, 316, b"        require(\n            state(proposalId) == ProposalState.Queued,\n            'NounsDAO::execute: proposal can only be executed if it is queued'\n        );"
governance/NounsDAOLogicV1.sol, 336, b"        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');"
governance/NounsDAOLogicV1.sol, 343, b"        require(\n            msg.sender == proposal.proposer ||\n                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,\n            'NounsDAO::cancel: proposer above threshold'\n        );"
governance/NounsDAOLogicV1.sol, 364, b"        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');"
governance/NounsDAOLogicV1.sol, 365, b"        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');"
governance/NounsDAOLogicV1.sol, 366, b"        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');"
governance/NounsDAOLogicV1.sol, 422, b"        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');"
governance/NounsDAOLogicV1.sol, 485, b"        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');"
governance/NounsDAOLogicV1.sol, 501, b"        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');"
governance/NounsDAOLogicV1.sol, 502, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV1.sol, 505, b"        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');"
governance/NounsDAOLogicV1.sol, 530, b"        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');"
governance/NounsDAOLogicV1.sol, 534, b"        require(\n            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,\n            'NounsDAO::_setVotingDelay: invalid voting delay'\n        );"
governance/NounsDAOLogicV1.sol, 546, b"        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');"
governance/NounsDAOLogicV1.sol, 550, b"        require(\n            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,\n            'NounsDAO::_setVotingPeriod: invalid voting period'\n        );"
governance/NounsDAOLogicV1.sol, 563, b"        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');"
governance/NounsDAOLogicV1.sol, 568, b"        require(\n            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&\n                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 581, b"        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');"
governance/NounsDAOLogicV1.sol, 585, b"        require(\n            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 599, b"        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');"
governance/NounsDAOLogicV1.sol, 617, b"        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');"
governance/NounsDAOLogicV1.sol, 638, b"        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');"
governance/NounsDAOLogicV1.sol, 651, b"        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');"
governance/NounsDAOLogicV2.sol, 133, b"        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');"
governance/NounsDAOLogicV2.sol, 134, b"        require(msg.sender == admin, 'NounsDAO::initialize: admin only');"
governance/NounsDAOLogicV2.sol, 135, b"        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');"
governance/NounsDAOLogicV2.sol, 136, b"        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');"
governance/NounsDAOLogicV2.sol, 140, b"        require(\n            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,\n            'NounsDAO::initialize: invalid voting period'\n        );"
governance/NounsDAOLogicV2.sol, 144, b"        require(\n            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,\n            'NounsDAO::initialize: invalid voting delay'\n        );"
governance/NounsDAOLogicV2.sol, 148, b"        require(\n            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::initialize: invalid proposal threshold bps'\n        );"
governance/NounsDAOLogicV2.sol, 200, b"        require(\n            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,\n            'NounsDAO::propose: proposer votes below proposal threshold'\n        );"
governance/NounsDAOLogicV2.sol, 206, b"        require(\n            targets.length == values.length &&\n                targets.length == signatures.length &&\n                targets.length == calldatas.length,\n            'NounsDAO::propose: proposal function information arity mismatch'\n        );"
governance/NounsDAOLogicV2.sol, 207, b"        require(targets.length != 0, 'NounsDAO::propose: must provide actions');"
governance/NounsDAOLogicV2.sol, 208, b"        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');"
governance/NounsDAOLogicV2.sol, 216, b"            require(\n                proposersLatestProposalState != ProposalState.Active,\n                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'\n            );"
governance/NounsDAOLogicV2.sol, 220, b"            require(\n                proposersLatestProposalState != ProposalState.Pending,\n                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'\n            );"
governance/NounsDAOLogicV2.sol, 289, b"        require(\n            state(proposalId) == ProposalState.Succeeded,\n            'NounsDAO::queue: proposal can only be queued if it is succeeded'\n        );"
governance/NounsDAOLogicV2.sol, 315, b"        require(\n            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),\n            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'\n        );"
governance/NounsDAOLogicV2.sol, 327, b"        require(\n            state(proposalId) == ProposalState.Queued,\n            'NounsDAO::execute: proposal can only be executed if it is queued'\n        );"
governance/NounsDAOLogicV2.sol, 347, b"        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');"
governance/NounsDAOLogicV2.sol, 354, b"        require(\n            msg.sender == proposal.proposer ||\n                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,\n            'NounsDAO::cancel: proposer above threshold'\n        );"
governance/NounsDAOLogicV2.sol, 375, b"        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');"
governance/NounsDAOLogicV2.sol, 376, b"        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');"
governance/NounsDAOLogicV2.sol, 377, b"        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');"
governance/NounsDAOLogicV2.sol, 433, b"        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');"
governance/NounsDAOLogicV2.sol, 577, b"        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');"
governance/NounsDAOLogicV2.sol, 593, b"        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');"
governance/NounsDAOLogicV2.sol, 594, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV2.sol, 597, b"        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');"
governance/NounsDAOLogicV2.sol, 622, b"        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');"
governance/NounsDAOLogicV2.sol, 626, b"        require(\n            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,\n            'NounsDAO::_setVotingDelay: invalid voting delay'\n        );"
governance/NounsDAOLogicV2.sol, 638, b"        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');"
governance/NounsDAOLogicV2.sol, 642, b"        require(\n            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,\n            'NounsDAO::_setVotingPeriod: invalid voting period'\n        );"
governance/NounsDAOLogicV2.sol, 655, b"        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');"
governance/NounsDAOLogicV2.sol, 660, b"        require(\n            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&\n                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'\n        );"
governance/NounsDAOLogicV2.sol, 674, b"        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');"
governance/NounsDAOLogicV2.sol, 681, b"        require(\n            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&\n                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,\n            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'\n        );"
governance/NounsDAOLogicV2.sol, 685, b"        require(\n            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,\n            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'\n        );"
governance/NounsDAOLogicV2.sol, 702, b"        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');"
governance/NounsDAOLogicV2.sol, 708, b"        require(\n            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,\n            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'\n        );"
governance/NounsDAOLogicV2.sol, 712, b"        require(\n            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,\n            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'\n        );"
governance/NounsDAOLogicV2.sol, 727, b"        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');"
governance/NounsDAOLogicV2.sol, 801, b"        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');"
governance/NounsDAOLogicV2.sol, 819, b"        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');"
governance/NounsDAOLogicV2.sol, 840, b"        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');"
governance/NounsDAOLogicV2.sol, 853, b"        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');"
governance/NounsDAOProxy.sol, 79, b"        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');"
governance/NounsDAOProxy.sol, 80, b"        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');"



### G02: COMPARISONS WITH ZERO FOR UNSIGNED INTEGERS
#### problem
0 is less gas efficient than !0 if you enable the optimizer at 10k AND you’re in a require statement. Detailed explanation with the opcodes https://twitter.com/gzeon/status/1485428085885640706

#### prof
base/ERC721Checkpointable.sol, 153, b'        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;'
base/ERC721Checkpointable.sol, 215, b'        if (srcRep != dstRep && amount > 0) {'
base/ERC721Checkpointable.sol, 218, b'                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 225, b'                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 541, b'        if (votes > 0) {'
governance/NounsDAOLogicV2.sol, 967, b'        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {'


### G03: PREFIX INCREMENT SAVE MORE GAS
#### problem
prefix increment ++i is more cheaper than postfix i++
#### prof
base/ERC721Checkpointable.sol, 141, b"        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');"
governance/NounsDAOLogicV1.sol, 216, b'        proposalCount++;'
governance/NounsDAOLogicV1.sol, 281, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 319, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 346, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 371, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 226, b'        proposalCount++;'
governance/NounsDAOLogicV2.sol, 292, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 330, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 357, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 382, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'


### G04: resign the default value to the variables.
#### problem
 resign the default value to the variables will cost more gas.
#### prof
base/ERC721Checkpointable.sol, 181, b'        uint32 lower = 0;'
governance/NounsDAOLogicV1.sol, 281, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 319, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 346, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 371, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 292, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 330, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 357, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 382, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 948, b'        uint256 lower = 0;'

### G05: ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
#### problem
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
#### prof
base/ERC721Checkpointable.sol, 141, b"        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');"
governance/NounsDAOLogicV1.sol, 216, b'        proposalCount++;'
governance/NounsDAOLogicV1.sol, 281, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 319, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 346, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 371, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 226, b'        proposalCount++;'
governance/NounsDAOLogicV2.sol, 292, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 330, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 357, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 382, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'


### G06：ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
#### prof
base/ERC721Checkpointable.sol, 135, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))'
base/ERC721Checkpointable.sol, 137, b'        bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));'
governance/NounsDAOLogicV1.sol, 480, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))'
governance/NounsDAOLogicV1.sol, 482, b'        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));'
governance/NounsDAOLogicV2.sol, 572, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))'
governance/NounsDAOLogicV2.sol, 574, b'        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));'

### G07: Splitting require() statements that use && saves gas
#### problem
See this issue(https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
#### prof:
governance/NounsDAOLogicV1.sol, 129, b"        require(\n            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,\n            'NounsDAO::initialize: invalid voting period'\n        );"
governance/NounsDAOLogicV1.sol, 133, b"        require(\n            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,\n            'NounsDAO::initialize: invalid voting delay'\n        );"
governance/NounsDAOLogicV1.sol, 137, b"        require(\n            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::initialize: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 141, b"        require(\n            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,\n            'NounsDAO::initialize: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 196, b"        require(\n            targets.length == values.length &&\n                targets.length == signatures.length &&\n                targets.length == calldatas.length,\n            'NounsDAO::propose: proposal function information arity mismatch'\n        );"
governance/NounsDAOLogicV1.sol, 343, b"        require(\n            msg.sender == proposal.proposer ||\n                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,\n            'NounsDAO::cancel: proposer above threshold'\n        );"
governance/NounsDAOLogicV1.sol, 534, b"        require(\n            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,\n            'NounsDAO::_setVotingDelay: invalid voting delay'\n        );"
governance/NounsDAOLogicV1.sol, 550, b"        require(\n            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,\n            'NounsDAO::_setVotingPeriod: invalid voting period'\n        );"
governance/NounsDAOLogicV1.sol, 568, b"        require(\n            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&\n                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 585, b"        require(\n            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold'\n        );"
governance/NounsDAOLogicV1.sol, 617, b"        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');"
governance/NounsDAOLogicV2.sol, 140, b"        require(\n            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,\n            'NounsDAO::initialize: invalid voting period'\n        );"
governance/NounsDAOLogicV2.sol, 144, b"        require(\n            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,\n            'NounsDAO::initialize: invalid voting delay'\n        );"
governance/NounsDAOLogicV2.sol, 148, b"        require(\n            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::initialize: invalid proposal threshold bps'\n        );"
governance/NounsDAOLogicV2.sol, 206, b"        require(\n            targets.length == values.length &&\n                targets.length == signatures.length &&\n                targets.length == calldatas.length,\n            'NounsDAO::propose: proposal function information arity mismatch'\n        );"
governance/NounsDAOLogicV2.sol, 354, b"        require(\n            msg.sender == proposal.proposer ||\n                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,\n            'NounsDAO::cancel: proposer above threshold'\n        );"
governance/NounsDAOLogicV2.sol, 626, b"        require(\n            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,\n            'NounsDAO::_setVotingDelay: invalid voting delay'\n        );"
governance/NounsDAOLogicV2.sol, 642, b"        require(\n            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,\n            'NounsDAO::_setVotingPeriod: invalid voting period'\n        );"
governance/NounsDAOLogicV2.sol, 660, b"        require(\n            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&\n                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,\n            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'\n        );"
governance/NounsDAOLogicV2.sol, 681, b"        require(\n            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&\n                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,\n            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'\n        );"

### G08: USING PRIVATE RATHER THAN PUBLIC FOR CONSTANTS, SAVES GAS
#### problem:
We can save getter function of public constants.
#### prof:
base/ERC721Checkpointable.sol, 41, b'    uint8 public constant decimals = 0;'
base/ERC721Checkpointable.sol, 60, b"    bytes32 public constant DOMAIN_TYPEHASH =\n        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');"
base/ERC721Checkpointable.sol, 64, b"    bytes32 public constant DELEGATION_TYPEHASH =\n        keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');"
governance/NounsDAOLogicV1.sol, 67, b"    string public constant name = 'Nouns DAO';"
governance/NounsDAOLogicV1.sol, 70, b'    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%'
governance/NounsDAOLogicV1.sol, 73, b'    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%'
governance/NounsDAOLogicV1.sol, 76, b'    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours'
governance/NounsDAOLogicV1.sol, 79, b'    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks'
governance/NounsDAOLogicV1.sol, 82, b'    uint256 public constant MIN_VOTING_DELAY = 1;'
governance/NounsDAOLogicV1.sol, 85, b'    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week'
governance/NounsDAOLogicV1.sol, 88, b'    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%'
governance/NounsDAOLogicV1.sol, 91, b'    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%'
governance/NounsDAOLogicV1.sol, 94, b'    uint256 public constant proposalMaxOperations = 10; // 10 actions'
governance/NounsDAOLogicV1.sol, 98, b"    bytes32 public constant DOMAIN_TYPEHASH =\n        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');"
governance/NounsDAOLogicV1.sol, 101, b"    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');"
governance/NounsDAOLogicV2.sol, 59, b"    string public constant name = 'Nouns DAO';"
governance/NounsDAOLogicV2.sol, 62, b'    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%'
governance/NounsDAOLogicV2.sol, 65, b'    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%'
governance/NounsDAOLogicV2.sol, 68, b'    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours'
governance/NounsDAOLogicV2.sol, 71, b'    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks'
governance/NounsDAOLogicV2.sol, 74, b'    uint256 public constant MIN_VOTING_DELAY = 1;'
governance/NounsDAOLogicV2.sol, 77, b'    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week'
governance/NounsDAOLogicV2.sol, 80, b'    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%'
governance/NounsDAOLogicV2.sol, 83, b'    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%'
governance/NounsDAOLogicV2.sol, 86, b'    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%'
governance/NounsDAOLogicV2.sol, 89, b'    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%'
governance/NounsDAOLogicV2.sol, 92, b'    uint256 public constant proposalMaxOperations = 10; // 10 actions'
governance/NounsDAOLogicV2.sol, 95, b'    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;'
governance/NounsDAOLogicV2.sol, 98, b'    uint256 public constant REFUND_BASE_GAS = 36000;'
governance/NounsDAOLogicV2.sol, 102, b"    bytes32 public constant DOMAIN_TYPEHASH =\n        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');"
governance/NounsDAOLogicV2.sol, 105, b"    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');"

### G09: USE A MORE RECENT VERSION OF SOLIDITY
#### problem
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

### G10:FUNCTIONS GUARANTEED TO REVERT WHEN CALLED BY NORMAL USERS CAN BE MARKED PAYABLE
#### problem
If a function modifier such as onlyOwner is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are CALLVALUE(2),DUP1(3),ISZERO(3),PUSH2(3),JUMPI(10),PUSH1(3),DUP1(3),REVERT(0),JUMPDEST(1),POP(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
#### prof
base/ERC721Enumerable.sol, 64, b'    function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) '
base/ERC721Enumerable.sol, 64, b'    function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) '
base/ERC721Enumerable.sol, 113, b'    function _beforeTokenTransfer(\n        address from,\n        address to,\n        uint256 tokenId\n    ) internal virtual override '
base/ERC721Enumerable.sol, 124, b'    function _addTokenToOwnerEnumeration(address to, uint256 tokenId) private '
base/ERC721Enumerable.sol, 124, b'    function _addTokenToOwnerEnumeration(address to, uint256 tokenId) private '
base/ERC721Enumerable.sol, 161, b'    function _removeTokenFromOwnerEnumeration(address from, uint256 tokenId) private '
base/ERC721Enumerable.sol, 161, b'    function _removeTokenFromOwnerEnumeration(address from, uint256 tokenId) private '
base/ERC721Enumerable.sol, 186, b'    function _removeTokenFromAllTokensEnumeration(uint256 tokenId) private '
governance/NounsDAOLogicV1.sol, 155, b'    function initialize(\n        address timelock_,\n        address nouns_,\n        address vetoer_,\n        uint256 votingPeriod_,\n        uint256 votingDelay_,\n        uint256 proposalThresholdBPS_,\n        uint256 quorumVotesBPS_\n    ) public virtual '
governance/NounsDAOLogicV1.sol, 539, b'    function _setVotingDelay(uint256 newVotingDelay) external '
governance/NounsDAOLogicV1.sol, 555, b'    function _setVotingPeriod(uint256 newVotingPeriod) external '
governance/NounsDAOLogicV1.sol, 573, b'    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external '
governance/NounsDAOLogicV1.sol, 590, b'    function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external '
governance/NounsDAOLogicV1.sol, 609, b'    function _setPendingAdmin(address newPendingAdmin) external '
governance/NounsDAOLogicV1.sol, 609, b'    function _setPendingAdmin(address newPendingAdmin) external '
governance/NounsDAOLogicV1.sol, 631, b'    function _acceptAdmin() external '
governance/NounsDAOLogicV1.sol, 631, b'    function _acceptAdmin() external '
governance/NounsDAOLogicV1.sol, 654, b'    function _burnVetoPower() public '
governance/NounsDAOLogicV2.sol, 165, b'    function initialize(\n        address timelock_,\n        address nouns_,\n        address vetoer_,\n        uint256 votingPeriod_,\n        uint256 votingDelay_,\n        uint256 proposalThresholdBPS_,\n        DynamicQuorumParams calldata dynamicQuorumParams_\n    ) public virtual '
governance/NounsDAOLogicV2.sol, 631, b'    function _setVotingDelay(uint256 newVotingDelay) external '
governance/NounsDAOLogicV2.sol, 647, b'    function _setVotingPeriod(uint256 newVotingPeriod) external '
governance/NounsDAOLogicV2.sol, 665, b'    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external '
governance/NounsDAOLogicV2.sol, 693, b'    function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external '
governance/NounsDAOLogicV2.sol, 720, b'    function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external '
governance/NounsDAOLogicV2.sol, 736, b'    function _setQuorumCoefficient(uint32 newQuorumCoefficient) external '
governance/NounsDAOLogicV2.sol, 781, b'    function _setDynamicQuorumParams(\n        uint16 newMinQuorumVotesBPS,\n        uint16 newMaxQuorumVotesBPS,\n        uint32 newQuorumCoefficient\n    ) public '
governance/NounsDAOLogicV2.sol, 792, b'    function _withdraw() external '
governance/NounsDAOLogicV2.sol, 811, b'    function _setPendingAdmin(address newPendingAdmin) external '
governance/NounsDAOLogicV2.sol, 811, b'    function _setPendingAdmin(address newPendingAdmin) external '
governance/NounsDAOLogicV2.sol, 833, b'    function _acceptAdmin() external '
governance/NounsDAOLogicV2.sol, 833, b'    function _acceptAdmin() external '
governance/NounsDAOLogicV2.sol, 856, b'    function _burnVetoPower() public '
governance/NounsDAOProxy.sol, 86, b'    function _setImplementation(address implementation_) public '

### G11: USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
#### problem
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
#### prof
base/ERC721Checkpointable.sol, 41, b'    uint8 public constant decimals = 0;'
base/ERC721Checkpointable.sol, 53, b'    mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;'
base/ERC721Checkpointable.sol, 56, b'    mapping(address => uint32) public numCheckpoints;'
base/ERC721Checkpointable.sol, 80, b"        return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');"
base/ERC721Checkpointable.sol, 80, b"        return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');"
base/ERC721Checkpointable.sol, 90, b'        return current == address(0) ? delegator : current;'
base/ERC721Checkpointable.sol, 102, b'        super._beforeTokenTransfer(from, to, tokenId);'
base/ERC721Checkpointable.sol, 105, b'        _moveDelegates(delegates(from), delegates(to), 1);'
base/ERC721Checkpointable.sol, 105, b'        _moveDelegates(delegates(from), delegates(to), 1);'
base/ERC721Checkpointable.sol, 113, b'        if (delegatee == address(0)) delegatee = msg.sender;'
base/ERC721Checkpointable.sol, 135, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))'
base/ERC721Checkpointable.sol, 135, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))'
base/ERC721Checkpointable.sol, 139, b'        address signatory = ecrecover(digest, v, r, s);'
base/ERC721Checkpointable.sol, 139, b'        address signatory = ecrecover(digest, v, r, s);'
base/ERC721Checkpointable.sol, 140, b"        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');"
base/ERC721Checkpointable.sol, 140, b"        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');"
base/ERC721Checkpointable.sol, 141, b"        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');"
base/ERC721Checkpointable.sol, 142, b"        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');"
base/ERC721Checkpointable.sol, 152, b'        uint32 nCheckpoints = numCheckpoints[account];'
base/ERC721Checkpointable.sol, 152, b'        uint32 nCheckpoints = numCheckpoints[account];'
base/ERC721Checkpointable.sol, 153, b'        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;'
base/ERC721Checkpointable.sol, 153, b'        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;'
base/ERC721Checkpointable.sol, 153, b'        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;'
base/ERC721Checkpointable.sol, 164, b"        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');"
base/ERC721Checkpointable.sol, 166, b'        uint32 nCheckpoints = numCheckpoints[account];'
base/ERC721Checkpointable.sol, 166, b'        uint32 nCheckpoints = numCheckpoints[account];'
base/ERC721Checkpointable.sol, 167, b'        if (nCheckpoints == 0) {'
base/ERC721Checkpointable.sol, 167, b'        if (nCheckpoints == 0) {'
base/ERC721Checkpointable.sol, 168, b'            return 0;'
base/ERC721Checkpointable.sol, 172, b'        if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {'
base/ERC721Checkpointable.sol, 172, b'        if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {'
base/ERC721Checkpointable.sol, 173, b'            return checkpoints[account][nCheckpoints - 1].votes;'
base/ERC721Checkpointable.sol, 173, b'            return checkpoints[account][nCheckpoints - 1].votes;'
base/ERC721Checkpointable.sol, 177, b'        if (checkpoints[account][0].fromBlock > blockNumber) {'
base/ERC721Checkpointable.sol, 177, b'        if (checkpoints[account][0].fromBlock > blockNumber) {'
base/ERC721Checkpointable.sol, 178, b'            return 0;'
base/ERC721Checkpointable.sol, 181, b'        uint32 lower = 0;'
base/ERC721Checkpointable.sol, 181, b'        uint32 lower = 0;'
base/ERC721Checkpointable.sol, 182, b'        uint32 upper = nCheckpoints - 1;'
base/ERC721Checkpointable.sol, 182, b'        uint32 upper = nCheckpoints - 1;'
base/ERC721Checkpointable.sol, 182, b'        uint32 upper = nCheckpoints - 1;'
base/ERC721Checkpointable.sol, 182, b'        uint32 upper = nCheckpoints - 1;'
base/ERC721Checkpointable.sol, 183, b'        while (upper > lower) {'
base/ERC721Checkpointable.sol, 183, b'        while (upper > lower) {'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 184, b'            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow'
base/ERC721Checkpointable.sol, 185, b'            Checkpoint memory cp = checkpoints[account][center];'
base/ERC721Checkpointable.sol, 185, b'            Checkpoint memory cp = checkpoints[account][center];'
base/ERC721Checkpointable.sol, 186, b'            if (cp.fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 186, b'            if (cp.fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 188, b'            } else if (cp.fromBlock < blockNumber) {'
base/ERC721Checkpointable.sol, 188, b'            } else if (cp.fromBlock < blockNumber) {'
base/ERC721Checkpointable.sol, 191, b'                upper = center - 1;'
base/ERC721Checkpointable.sol, 189, b'                lower = center;'
base/ERC721Checkpointable.sol, 187, b'                return cp.votes;'
base/ERC721Checkpointable.sol, 187, b'                return cp.votes;'
base/ERC721Checkpointable.sol, 194, b'        return checkpoints[account][lower].votes;'
base/ERC721Checkpointable.sol, 194, b'        return checkpoints[account][lower].votes;'
base/ERC721Checkpointable.sol, 205, b'        uint96 amount = votesToDelegate(delegator);'
base/ERC721Checkpointable.sol, 205, b'        uint96 amount = votesToDelegate(delegator);'
base/ERC721Checkpointable.sol, 205, b'        uint96 amount = votesToDelegate(delegator);'
base/ERC721Checkpointable.sol, 207, b'        _moveDelegates(currentDelegate, delegatee, amount);'
base/ERC721Checkpointable.sol, 207, b'        _moveDelegates(currentDelegate, delegatee, amount);'
base/ERC721Checkpointable.sol, 215, b'        if (srcRep != dstRep && amount > 0) {'
base/ERC721Checkpointable.sol, 215, b'        if (srcRep != dstRep && amount > 0) {'
base/ERC721Checkpointable.sol, 216, b'            if (srcRep != address(0)) {'
base/ERC721Checkpointable.sol, 217, b'                uint32 srcRepNum = numCheckpoints[srcRep];'
base/ERC721Checkpointable.sol, 217, b'                uint32 srcRepNum = numCheckpoints[srcRep];'
base/ERC721Checkpointable.sol, 218, b'                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 218, b'                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 218, b'                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 218, b'                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 219, b"                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');"
base/ERC721Checkpointable.sol, 220, b'                _writeCheckpoint(srcRep, srcRepNum, srcRepOld, srcRepNew);'
base/ERC721Checkpointable.sol, 220, b'                _writeCheckpoint(srcRep, srcRepNum, srcRepOld, srcRepNew);'
base/ERC721Checkpointable.sol, 220, b'                _writeCheckpoint(srcRep, srcRepNum, srcRepOld, srcRepNew);'
base/ERC721Checkpointable.sol, 220, b'                _writeCheckpoint(srcRep, srcRepNum, srcRepOld, srcRepNew);'
base/ERC721Checkpointable.sol, 223, b'            if (dstRep != address(0)) {'
base/ERC721Checkpointable.sol, 224, b'                uint32 dstRepNum = numCheckpoints[dstRep];'
base/ERC721Checkpointable.sol, 224, b'                uint32 dstRepNum = numCheckpoints[dstRep];'
base/ERC721Checkpointable.sol, 225, b'                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 225, b'                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 225, b'                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 225, b'                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;'
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 226, b"                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');"
base/ERC721Checkpointable.sol, 227, b'                _writeCheckpoint(dstRep, dstRepNum, dstRepOld, dstRepNew);'
base/ERC721Checkpointable.sol, 227, b'                _writeCheckpoint(dstRep, dstRepNum, dstRepOld, dstRepNew);'
base/ERC721Checkpointable.sol, 227, b'                _writeCheckpoint(dstRep, dstRepNum, dstRepOld, dstRepNew);'
base/ERC721Checkpointable.sol, 227, b'                _writeCheckpoint(dstRep, dstRepNum, dstRepOld, dstRepNew);'
base/ERC721Checkpointable.sol, 238, b'        uint32 blockNumber = safe32('
base/ERC721Checkpointable.sol, 241, b"        uint32 blockNumber = safe32(\n            block.number,\n            'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'\n        );"
base/ERC721Checkpointable.sol, 240, b"            'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'"
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 243, b'        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {'
base/ERC721Checkpointable.sol, 246, b'            checkpoints[delegatee][nCheckpoints] = Checkpoint(blockNumber, newVotes);'
base/ERC721Checkpointable.sol, 247, b'            numCheckpoints[delegatee] = nCheckpoints + 1;'
base/ERC721Checkpointable.sol, 244, b'            checkpoints[delegatee][nCheckpoints - 1].votes = newVotes;'
base/ERC721Checkpointable.sol, 254, b'        require(n < 2**32, errorMessage);'
base/ERC721Checkpointable.sol, 254, b'        require(n < 2**32, errorMessage);'
base/ERC721Checkpointable.sol, 254, b'        require(n < 2**32, errorMessage);'
base/ERC721Checkpointable.sol, 255, b'        return uint32(n);'
base/ERC721Checkpointable.sol, 255, b'        return uint32(n);'
base/ERC721Checkpointable.sol, 259, b'        require(n < 2**96, errorMessage);'
base/ERC721Checkpointable.sol, 259, b'        require(n < 2**96, errorMessage);'
base/ERC721Checkpointable.sol, 259, b'        require(n < 2**96, errorMessage);'
base/ERC721Checkpointable.sol, 260, b'        return uint96(n);'
base/ERC721Checkpointable.sol, 260, b'        return uint96(n);'
base/ERC721Checkpointable.sol, 268, b'        uint96 c = a + b;'
base/ERC721Checkpointable.sol, 268, b'        uint96 c = a + b;'
base/ERC721Checkpointable.sol, 268, b'        uint96 c = a + b;'
base/ERC721Checkpointable.sol, 268, b'        uint96 c = a + b;'
base/ERC721Checkpointable.sol, 269, b'        require(c >= a, errorMessage);'
base/ERC721Checkpointable.sol, 269, b'        require(c >= a, errorMessage);'
base/ERC721Checkpointable.sol, 270, b'        return c;'
base/ERC721Checkpointable.sol, 278, b'        require(b <= a, errorMessage);'
base/ERC721Checkpointable.sol, 278, b'        require(b <= a, errorMessage);'
base/ERC721Checkpointable.sol, 279, b'        return a - b;'
base/ERC721Checkpointable.sol, 279, b'        return a - b;'
base/ERC721Checkpointable.sol, 279, b'        return a - b;'
base/ERC721Enumerable.sol, 103, b'        if (from == address(0)) {'
base/ERC721Enumerable.sol, 108, b'        if (to == address(0)) {'
base/ERC721Enumerable.sol, 147, b'        uint256 lastTokenIndex = ERC721.balanceOf(from) - 1;'
base/ERC721Enumerable.sol, 172, b'        uint256 lastTokenIndex = _allTokens.length - 1;'
governance/NounsDAOInterfaces.sol, 348, b'    DynamicQuorumParamsCheckpoint[] public quorumParamsCheckpoints;'
governance/NounsDAOLogicV1.sol, 122, b"        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');"
governance/NounsDAOLogicV1.sol, 124, b"        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');"
governance/NounsDAOLogicV1.sol, 125, b"        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');"
governance/NounsDAOLogicV1.sol, 188, b'            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 188, b'            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 197, b"        require(targets.length != 0, 'NounsDAO::propose: must provide actions');"
governance/NounsDAOLogicV1.sol, 201, b'        if (temp.latestProposalId != 0) {'
governance/NounsDAOLogicV1.sol, 267, b'        return newProposal.id;'
governance/NounsDAOLogicV1.sol, 281, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 281, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 319, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 325, b'                proposal.eta'
governance/NounsDAOLogicV1.sol, 319, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 340, b'            msg.sender == proposal.proposer ||'
governance/NounsDAOLogicV1.sol, 341, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 341, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 341, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 341, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV1.sol, 346, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 352, b'                proposal.eta'
governance/NounsDAOLogicV1.sol, 346, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 364, b"        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');"
governance/NounsDAOLogicV1.sol, 371, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 377, b'                proposal.eta'
governance/NounsDAOLogicV1.sol, 371, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV1.sol, 424, b'        if (proposal.vetoed) {'
governance/NounsDAOLogicV1.sol, 426, b'        } else if (proposal.canceled) {'
governance/NounsDAOLogicV1.sol, 428, b'        } else if (block.number <= proposal.startBlock) {'
governance/NounsDAOLogicV1.sol, 430, b'        } else if (block.number <= proposal.endBlock) {'
governance/NounsDAOLogicV1.sol, 432, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/NounsDAOLogicV1.sol, 432, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/NounsDAOLogicV1.sol, 432, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/NounsDAOLogicV1.sol, 432, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {'
governance/NounsDAOLogicV1.sol, 434, b'        } else if (proposal.eta == 0) {'
governance/NounsDAOLogicV1.sol, 434, b'        } else if (proposal.eta == 0) {'
governance/NounsDAOLogicV1.sol, 436, b'        } else if (proposal.executed) {'
governance/NounsDAOLogicV1.sol, 438, b'        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {'
governance/NounsDAOLogicV1.sol, 480, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))'
governance/NounsDAOLogicV1.sol, 482, b'        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));'
governance/NounsDAOLogicV1.sol, 484, b'        address signatory = ecrecover(digest, v, r, s);'
governance/NounsDAOLogicV1.sol, 484, b'        address signatory = ecrecover(digest, v, r, s);'
governance/NounsDAOLogicV1.sol, 485, b"        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');"
governance/NounsDAOLogicV1.sol, 502, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV1.sol, 502, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV1.sol, 505, b"        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');"
governance/NounsDAOLogicV1.sol, 508, b'        uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);'
governance/NounsDAOLogicV1.sol, 508, b'        uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);'
governance/NounsDAOLogicV1.sol, 508, b'        uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);'
governance/NounsDAOLogicV1.sol, 510, b'        if (support == 0) {'
governance/NounsDAOLogicV1.sol, 510, b'        if (support == 0) {'
governance/NounsDAOLogicV1.sol, 512, b'        } else if (support == 1) {'
governance/NounsDAOLogicV1.sol, 512, b'        } else if (support == 1) {'
governance/NounsDAOLogicV1.sol, 514, b'        } else if (support == 2) {'
governance/NounsDAOLogicV1.sol, 514, b'        } else if (support == 2) {'
governance/NounsDAOLogicV1.sol, 519, b'        receipt.support = support;'
governance/NounsDAOLogicV1.sol, 520, b'        receipt.votes = votes;'
governance/NounsDAOLogicV1.sol, 522, b'        return votes;'
governance/NounsDAOLogicV1.sol, 617, b"        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');"
governance/NounsDAOLogicV1.sol, 653, b'        _setVetoer(address(0));'
governance/NounsDAOLogicV1.sol, 661, b'        return bps2Uint(proposalThresholdBPS, nouns.totalSupply());'
governance/NounsDAOLogicV1.sol, 661, b'        return bps2Uint(proposalThresholdBPS, nouns.totalSupply());'
governance/NounsDAOLogicV1.sol, 669, b'        return bps2Uint(quorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV1.sol, 669, b'        return bps2Uint(quorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV1.sol, 673, b'        return (number * bps) / 10000;'
governance/NounsDAOLogicV2.sol, 133, b"        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');"
governance/NounsDAOLogicV2.sol, 135, b"        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');"
governance/NounsDAOLogicV2.sol, 136, b"        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');"
governance/NounsDAOLogicV2.sol, 160, b'        _setDynamicQuorumParams('
governance/NounsDAOLogicV2.sol, 161, b'            dynamicQuorumParams_.minQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 162, b'            dynamicQuorumParams_.maxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 163, b'            dynamicQuorumParams_.quorumCoefficient'
governance/NounsDAOLogicV2.sol, 198, b'            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 198, b'            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 207, b"        require(targets.length != 0, 'NounsDAO::propose: must provide actions');"
governance/NounsDAOLogicV2.sol, 211, b'        if (temp.latestProposalId != 0) {'
governance/NounsDAOLogicV2.sol, 278, b'        return newProposal.id;'
governance/NounsDAOLogicV2.sol, 292, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 292, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 330, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 336, b'                proposal.eta'
governance/NounsDAOLogicV2.sol, 330, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 351, b'            msg.sender == proposal.proposer ||'
governance/NounsDAOLogicV2.sol, 352, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 352, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 352, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 352, b'                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 357, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 363, b'                proposal.eta'
governance/NounsDAOLogicV2.sol, 357, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 375, b"        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');"
governance/NounsDAOLogicV2.sol, 382, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 388, b'                proposal.eta'
governance/NounsDAOLogicV2.sol, 382, b'        for (uint256 i = 0; i < proposal.targets.length; i++) {'
governance/NounsDAOLogicV2.sol, 435, b'        if (proposal.vetoed) {'
governance/NounsDAOLogicV2.sol, 437, b'        } else if (proposal.canceled) {'
governance/NounsDAOLogicV2.sol, 439, b'        } else if (block.number <= proposal.startBlock) {'
governance/NounsDAOLogicV2.sol, 441, b'        } else if (block.number <= proposal.endBlock) {'
governance/NounsDAOLogicV2.sol, 443, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {'
governance/NounsDAOLogicV2.sol, 443, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {'
governance/NounsDAOLogicV2.sol, 443, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {'
governance/NounsDAOLogicV2.sol, 443, b'        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {'
governance/NounsDAOLogicV2.sol, 445, b'        } else if (proposal.eta == 0) {'
governance/NounsDAOLogicV2.sol, 445, b'        } else if (proposal.eta == 0) {'
governance/NounsDAOLogicV2.sol, 447, b'        } else if (proposal.executed) {'
governance/NounsDAOLogicV2.sol, 449, b'        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {'
governance/NounsDAOLogicV2.sol, 465, b'            ProposalCondensed({'
governance/NounsDAOLogicV2.sol, 466, b'                id: proposal.id,'
governance/NounsDAOLogicV2.sol, 467, b'                proposer: proposal.proposer,'
governance/NounsDAOLogicV2.sol, 468, b'                proposalThreshold: proposal.proposalThreshold,'
governance/NounsDAOLogicV2.sol, 469, b'                quorumVotes: quorumVotes(proposal.id),'
governance/NounsDAOLogicV2.sol, 470, b'                eta: proposal.eta,'
governance/NounsDAOLogicV2.sol, 471, b'                startBlock: proposal.startBlock,'
governance/NounsDAOLogicV2.sol, 472, b'                endBlock: proposal.endBlock,'
governance/NounsDAOLogicV2.sol, 473, b'                forVotes: proposal.forVotes,'
governance/NounsDAOLogicV2.sol, 474, b'                againstVotes: proposal.againstVotes,'
governance/NounsDAOLogicV2.sol, 475, b'                abstainVotes: proposal.abstainVotes,'
governance/NounsDAOLogicV2.sol, 476, b'                canceled: proposal.canceled,'
governance/NounsDAOLogicV2.sol, 477, b'                vetoed: proposal.vetoed,'
governance/NounsDAOLogicV2.sol, 478, b'                executed: proposal.executed,'
governance/NounsDAOLogicV2.sol, 479, b'                totalSupply: proposal.totalSupply,'
governance/NounsDAOLogicV2.sol, 480, b'                creationBlock: proposal.creationBlock'
governance/NounsDAOLogicV2.sol, 504, b"        castRefundableVoteInternal(proposalId, support, '');"
governance/NounsDAOLogicV2.sol, 523, b'        castRefundableVoteInternal(proposalId, support, reason);'
governance/NounsDAOLogicV2.sol, 539, b'        uint96 votes = castVoteInternal(msg.sender, proposalId, support);'
governance/NounsDAOLogicV2.sol, 539, b'        uint96 votes = castVoteInternal(msg.sender, proposalId, support);'
governance/NounsDAOLogicV2.sol, 539, b'        uint96 votes = castVoteInternal(msg.sender, proposalId, support);'
governance/NounsDAOLogicV2.sol, 541, b'        if (votes > 0) {'
governance/NounsDAOLogicV2.sol, 541, b'        if (votes > 0) {'
governance/NounsDAOLogicV2.sol, 572, b'            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))'
governance/NounsDAOLogicV2.sol, 574, b'        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));'
governance/NounsDAOLogicV2.sol, 576, b'        address signatory = ecrecover(digest, v, r, s);'
governance/NounsDAOLogicV2.sol, 576, b'        address signatory = ecrecover(digest, v, r, s);'
governance/NounsDAOLogicV2.sol, 577, b"        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');"
governance/NounsDAOLogicV2.sol, 594, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV2.sol, 594, b"        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');"
governance/NounsDAOLogicV2.sol, 597, b"        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');"
governance/NounsDAOLogicV2.sol, 600, b'        uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));'
governance/NounsDAOLogicV2.sol, 600, b'        uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));'
governance/NounsDAOLogicV2.sol, 600, b'        uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));'
governance/NounsDAOLogicV2.sol, 602, b'        if (support == 0) {'
governance/NounsDAOLogicV2.sol, 602, b'        if (support == 0) {'
governance/NounsDAOLogicV2.sol, 604, b'        } else if (support == 1) {'
governance/NounsDAOLogicV2.sol, 604, b'        } else if (support == 1) {'
governance/NounsDAOLogicV2.sol, 606, b'        } else if (support == 2) {'
governance/NounsDAOLogicV2.sol, 606, b'        } else if (support == 2) {'
governance/NounsDAOLogicV2.sol, 611, b'        receipt.support = support;'
governance/NounsDAOLogicV2.sol, 612, b'        receipt.votes = votes;'
governance/NounsDAOLogicV2.sol, 614, b'        return votes;'
governance/NounsDAOLogicV2.sol, 678, b'            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&'
governance/NounsDAOLogicV2.sol, 679, b'                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,'
governance/NounsDAOLogicV2.sol, 683, b'            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 683, b'            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 687, b'        uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 687, b'        uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 688, b'        params.minQuorumVotesBPS = newMinQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 706, b'            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,'
governance/NounsDAOLogicV2.sol, 710, b'            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 710, b'            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 714, b'        uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 714, b'        uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 715, b'        params.maxQuorumVotesBPS = newMaxQuorumVotesBPS;'
governance/NounsDAOLogicV2.sol, 730, b'        uint32 oldQuorumCoefficient = params.quorumCoefficient;'
governance/NounsDAOLogicV2.sol, 730, b'        uint32 oldQuorumCoefficient = params.quorumCoefficient;'
governance/NounsDAOLogicV2.sol, 731, b'        params.quorumCoefficient = newQuorumCoefficient;'
governance/NounsDAOLogicV2.sol, 757, b'            newMinQuorumVotesBPS < MIN_QUORUM_VOTES_BPS_LOWER_BOUND ||'
governance/NounsDAOLogicV2.sol, 758, b'            newMinQuorumVotesBPS > MIN_QUORUM_VOTES_BPS_UPPER_BOUND'
governance/NounsDAOLogicV2.sol, 762, b'        if (newMaxQuorumVotesBPS > MAX_QUORUM_VOTES_BPS_UPPER_BOUND) {'
governance/NounsDAOLogicV2.sol, 765, b'        if (newMinQuorumVotesBPS > newMaxQuorumVotesBPS) {'
governance/NounsDAOLogicV2.sol, 765, b'        if (newMinQuorumVotesBPS > newMaxQuorumVotesBPS) {'
governance/NounsDAOLogicV2.sol, 771, b'        DynamicQuorumParams memory params = DynamicQuorumParams({'
governance/NounsDAOLogicV2.sol, 772, b'            minQuorumVotesBPS: newMinQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 773, b'            maxQuorumVotesBPS: newMaxQuorumVotesBPS,'
governance/NounsDAOLogicV2.sol, 774, b'            quorumCoefficient: newQuorumCoefficient'
governance/NounsDAOLogicV2.sol, 819, b"        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');"
governance/NounsDAOLogicV2.sol, 855, b'        _setVetoer(address(0));'
governance/NounsDAOLogicV2.sol, 863, b'        return bps2Uint(proposalThresholdBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 863, b'        return bps2Uint(proposalThresholdBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 867, b'        if (proposal.creationBlock == 0) {'
governance/NounsDAOLogicV2.sol, 867, b'        if (proposal.creationBlock == 0) {'
governance/NounsDAOLogicV2.sol, 868, b'            return proposal.startBlock - votingDelay;'
governance/NounsDAOLogicV2.sol, 870, b'        return proposal.creationBlock;'
governance/NounsDAOLogicV2.sol, 879, b'        if (proposal.totalSupply == 0) {'
governance/NounsDAOLogicV2.sol, 879, b'        if (proposal.totalSupply == 0) {'
governance/NounsDAOLogicV2.sol, 880, b'            return proposal.quorumVotes;'
governance/NounsDAOLogicV2.sol, 885, b'                proposal.againstVotes,'
governance/NounsDAOLogicV2.sol, 886, b'                proposal.totalSupply,'
governance/NounsDAOLogicV2.sol, 887, b'                getDynamicQuorumParamsAt(proposal.creationBlock)'
governance/NounsDAOLogicV2.sol, 909, b'        uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;'
governance/NounsDAOLogicV2.sol, 910, b'        uint256 adjustedQuorumBPS = params.minQuorumVotesBPS + quorumAdjustmentBPS;'
governance/NounsDAOLogicV2.sol, 911, b'        uint256 quorumBPS = min(params.maxQuorumVotesBPS, adjustedQuorumBPS);'
governance/NounsDAOLogicV2.sol, 923, b"        uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');"
governance/NounsDAOLogicV2.sol, 923, b"        uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');"
governance/NounsDAOLogicV2.sol, 924, b'        uint256 len = quorumParamsCheckpoints.length;'
governance/NounsDAOLogicV2.sol, 926, b'        if (len == 0) {'
governance/NounsDAOLogicV2.sol, 928, b'                DynamicQuorumParams({'
governance/NounsDAOLogicV2.sol, 929, b'                    minQuorumVotesBPS: safe16(quorumVotesBPS),'
governance/NounsDAOLogicV2.sol, 930, b'                    maxQuorumVotesBPS: safe16(quorumVotesBPS),'
governance/NounsDAOLogicV2.sol, 931, b'                    quorumCoefficient: 0'
governance/NounsDAOLogicV2.sol, 935, b'        if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {'
governance/NounsDAOLogicV2.sol, 935, b'        if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {'
governance/NounsDAOLogicV2.sol, 935, b'        if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {'
governance/NounsDAOLogicV2.sol, 936, b'            return quorumParamsCheckpoints[len - 1].params;'
governance/NounsDAOLogicV2.sol, 939, b'        if (quorumParamsCheckpoints[0].fromBlock > blockNumber) {'
governance/NounsDAOLogicV2.sol, 939, b'        if (quorumParamsCheckpoints[0].fromBlock > blockNumber) {'
governance/NounsDAOLogicV2.sol, 939, b'        if (quorumParamsCheckpoints[0].fromBlock > blockNumber) {'
governance/NounsDAOLogicV2.sol, 941, b'                DynamicQuorumParams({'
governance/NounsDAOLogicV2.sol, 942, b'                    minQuorumVotesBPS: safe16(quorumVotesBPS),'
governance/NounsDAOLogicV2.sol, 943, b'                    maxQuorumVotesBPS: safe16(quorumVotesBPS),'
governance/NounsDAOLogicV2.sol, 944, b'                    quorumCoefficient: 0'
governance/NounsDAOLogicV2.sol, 948, b'        uint256 lower = 0;'
governance/NounsDAOLogicV2.sol, 949, b'        uint256 upper = len - 1;'
governance/NounsDAOLogicV2.sol, 951, b'            uint256 center = upper - (upper - lower) / 2;'
governance/NounsDAOLogicV2.sol, 952, b'            DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];'
governance/NounsDAOLogicV2.sol, 952, b'            DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];'
governance/NounsDAOLogicV2.sol, 953, b'            if (cp.fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 953, b'            if (cp.fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 953, b'            if (cp.fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 955, b'            } else if (cp.fromBlock < blockNumber) {'
governance/NounsDAOLogicV2.sol, 955, b'            } else if (cp.fromBlock < blockNumber) {'
governance/NounsDAOLogicV2.sol, 955, b'            } else if (cp.fromBlock < blockNumber) {'
governance/NounsDAOLogicV2.sol, 954, b'                return cp.params;'
governance/NounsDAOLogicV2.sol, 961, b'        return quorumParamsCheckpoints[lower].params;'
governance/NounsDAOLogicV2.sol, 965, b"        uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');"
governance/NounsDAOLogicV2.sol, 965, b"        uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');"
governance/NounsDAOLogicV2.sol, 966, b'        uint256 pos = quorumParamsCheckpoints.length;'
governance/NounsDAOLogicV2.sol, 967, b'        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 967, b'        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 967, b'        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 967, b'        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {'
governance/NounsDAOLogicV2.sol, 970, b'            quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));'
governance/NounsDAOLogicV2.sol, 970, b'            quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));'
governance/NounsDAOLogicV2.sol, 970, b'            quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));'
governance/NounsDAOLogicV2.sol, 970, b'            quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));'
governance/NounsDAOLogicV2.sol, 970, b'            quorumParamsCheckpoints.push(DynamicQuorumParamsCheckpoint({ fromBlock: blockNumber, params: params }));'
governance/NounsDAOLogicV2.sol, 977, b'            if (balance == 0) {'
governance/NounsDAOLogicV2.sol, 996, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).minQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 996, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).minQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 996, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).minQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 1003, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).maxQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 1003, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).maxQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 1003, b'        return bps2Uint(getDynamicQuorumParamsAt(block.number).maxQuorumVotesBPS, nouns.totalSupply());'
governance/NounsDAOLogicV2.sol, 1007, b'        return (number * bps) / 10000;'
governance/NounsDAOLogicV2.sol, 1019, b'        require(n <= type(uint32).max, errorMessage);'
governance/NounsDAOLogicV2.sol, 1019, b'        require(n <= type(uint32).max, errorMessage);'
governance/NounsDAOLogicV2.sol, 1019, b'        require(n <= type(uint32).max, errorMessage);'
governance/NounsDAOLogicV2.sol, 1020, b'        return uint32(n);'
governance/NounsDAOLogicV2.sol, 1020, b'        return uint32(n);'
governance/NounsDAOLogicV2.sol, 1024, b'        if (n > type(uint16).max) {'
governance/NounsDAOLogicV2.sol, 1024, b'        if (n > type(uint16).max) {'
governance/NounsDAOLogicV2.sol, 1024, b'        if (n > type(uint16).max) {'
governance/NounsDAOLogicV2.sol, 1027, b'        return uint16(n);'
governance/NounsDAOLogicV2.sol, 1027, b'        return uint16(n);'
governance/NounsDAOProxy.sol, 80, b"        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');"


### G12: <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
#### problem
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
#### prof
we should store the length number into memory variables.
NounsDAOLogicV1.sol,L281
NounsDAOLogicV1.sol,L319
NounsDAOLogicV1.sol,L346
NounsDAOLogicV1.sol,L371

### G13: external function should have let their argumentation declared as calldata types.
#### prof
NounsDAOLogicV1.sol,L450
NounsDAOLogicV1.sol,L464