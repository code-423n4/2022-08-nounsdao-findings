1)++i costs less gas than i++, especially when it’s used in for-loops (--i/i-- too)

Saves 6 gas PER LOOP

  
  
File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  281,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  319,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  346,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  371,58:         for (uint256 i = 0; i < proposal.targets.length; i++) { 

2)It costs more gas to initialize variables to zero than to let the default of zero be applied

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {  
    
File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  281,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  319,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  346,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  371,58:         for (uint256 i = 0; i < proposal.targets.length; i++) { 

3)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
  
File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  281,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  319,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  346,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  371,58:         for (uint256 i = 0; i < proposal.targets.length; i++) { 
  

4) ++i/i++ should be unchecked{++i}/unchecked{++i} when it is not possible for them to overflow,
as is the case when used in for- and while-loops  
  
File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  281,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  319,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  346,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  371,58:         for (uint256 i = 0; i < proposal.targets.length; i++) { 

5)Use custom errors rather than revert()/require() strings to save gas  


File: 2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol
  140,9:         require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
  141,9:         require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
  142,9:         require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
  164,9:         require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');



File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  122,9:         require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
  123,9:         require(msg.sender == admin, 'NounsDAO::initialize: admin only');
  124,9:         require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
  125,9:         require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
  126,9:         require( votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
  130,9:         require( votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
  134,9:         require( proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
  138,9:         require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
  187,9:         require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
  191,9:         require( 
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
  197,9:         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
  198,9:         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
  203,13:             require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
  207,13:             require( 
                 proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );
  275,9:         require(
             state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );
  301,9:         require( 
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );
  313,9:         require( 
             state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );
     
  336,9:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
  339,9:         require(
                msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );
  364,9:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
  365,9:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
  366,9:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
  422,9:         require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
  485,9:         require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
  501,9:         require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
  502,9:         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
  505,9:         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
  530,9:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
  531,9:         require( 
             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
  546,9:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
  547,9:         require(
             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
    
  563,9:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
  564,9:         require(  
              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
  581,9:         require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
  582,9:         require( 
             newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
  
  599,9:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
  617,9:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
  638,9:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
  651,9:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');


File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol
  133,9:         require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
  134,9:         require(msg.sender == admin, 'NounsDAO::initialize: admin only');
  135,9:         require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
  136,9:         require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
  137,9:         require( votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
  141,9:         require( votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
  145,9:         require( proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );
  197,9:         require(   nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
  201,9:         require(  targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
  207,9:         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
  208,9:         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
  213,13:             require(proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
  217,13:             require( proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );
  286,9:         require(state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );
  312,9:         require( !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );
  324,9:         require( state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );
  347,9:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
  350,9:         require( msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );
  375,9:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
  376,9:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
  377,9:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
  433,9:         require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
  577,9:         require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
  593,9:         require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
  594,9:         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
  597,9:         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
  622,9:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
  623,9:         require( newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
  638,9:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
  639,9:         require(  newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
  655,9:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
  656,9:         require(  newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
  674,9:         require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
  677,9:         require(newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
  682,9:         require( newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );

  702,9:         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
  705,9:         require( newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
  709,9:         require(  params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );
  727,9:         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
  801,9:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
  819,9:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
  840,9:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
  853,9:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');



File: 2022-08-nounsdao\contracts\governance\NounsDAOProxy.sol
  79,9:         require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
  80,9:         require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

6)++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{++I} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS  
	
File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  281,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  319,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  346,58:         for (uint256 i = 0; i < proposal.targets.length; i++) {
  371,58:         for (uint256 i = 0; i < proposal.targets.length; i++) { 
  
7) USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value


File: 2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol
  35,1: pragma solidity ^0.8.6;

File: 2022-08-nounsdao\contracts\base\ERC721Enumerable.sol
  28,1: pragma solidity ^0.8.0;

File: 2022-08-nounsdao\contracts\governance\NounsDAOInterfaces.sol
  33,1: pragma solidity ^0.8.6;

File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  61,1: pragma solidity ^0.8.6;

File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol
  53,1: pragma solidity ^0.8.6;

File: 2022-08-nounsdao\contracts\governance\NounsDAOProxy.sol
  36,1: pragma solidity ^0.8.6; 

8)Using private rather than public for constants, saves gas

If needed, the value can be read from the verified contract source code.
Savings are due to the compiler not having to create non-payable getter
functions for deployment calldata, and not adding another entry to the method ID table

File: 2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol
  41,11:     uint8 public constant decimals = 0;
  59,13:     bytes32 public constant DOMAIN_TYPEHASH =
  63,13:     bytes32 public constant DELEGATION_TYPEHASH =

File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol
  67,12:     string public constant name = 'Nouns DAO';
  70,13:     uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
  73,13:     uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
  76,13:     uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
  79,13:     uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
  82,13:     uint256 public constant MIN_VOTING_DELAY = 1;
  85,13:     uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
  88,13:     uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
  91,13:     uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
  94,13:     uint256 public constant proposalMaxOperations = 10; // 10 actions
  97,13:     bytes32 public constant DOMAIN_TYPEHASH =
  101,13:     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol
  59,12:     string public constant name = 'Nouns DAO';
  62,13:     uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
  65,13:     uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
  68,13:     uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
  71,13:     uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
  74,13:     uint256 public constant MIN_VOTING_DELAY = 1;
  77,13:     uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
  80,13:     uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
  83,13:     uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
  86,13:     uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
  89,13:     uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
  92,13:     uint256 public constant proposalMaxOperations = 10; // 10 actions
  95,13:     uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
  98,13:     uint256 public constant REFUND_BASE_GAS = 36000;
  101,13:     bytes32 public constant DOMAIN_TYPEHASH =
  105,13:     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');  
 


9)STATE VARIABLES CAN BE PACKED INTO FEWER STORAGE SLOTS
If variables occupying the same slot are both written the same function or by the constructor,
avoids a separate Gsset (20000 gas). Reads of the variables are also cheaper

File: 2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol
  
           		
127,9	address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
		
		Variable ordering with 5 slots instead of the current 6:
		uint8(1):v,address(20):delegatee,uint256(32):nonce,uint256(32):expiry, 
		bytes32(32):r,bytes32(32):s
		

File: 2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol
589,9 	address voter,
        uint256 proposalId,
        uint8 support
		
		Variable ordering with 2 slots instead of the current 3:
		uint8(1): support,address(20):voter,uint256(32):proposalId   
  



