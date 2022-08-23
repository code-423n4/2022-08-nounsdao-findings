# [G-01] State variables only set in the constructor should be declared immutable (Avoids a Gsset (20000 gas)):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 128-134):

    `    address public admin;

    /// @notice Pending administrator for this contract
    address public pendingAdmin;

    /// @notice Active brains of Governor
    address public implementation;`       

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 145-166):

    `    address public vetoer;

    /// @notice The delay before voting on a proposal may take place, once proposed, in blocks
    uint256 public votingDelay;

    /// @notice The duration of voting on a proposal, in blocks
    uint256 public votingPeriod;

    /// @notice The basis point number of votes required in order for a voter to become a proposer. *DIFFERS from GovernerBravo
    uint256 public proposalThresholdBPS;

    /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo
    uint256 public quorumVotesBPS;

    /// @notice The total number of proposals
    uint256 public proposalCount;

    /// @notice The address of the Nouns DAO Executor NounsDAOExecutor
    INounsDAOExecutor public timelock;

    /// @notice The address of the Nouns tokens
    NounsTokenLike public nouns;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 245-266):

    `    address public vetoer;

    /// @notice The delay before voting on a proposal may take place, once proposed, in blocks
    uint256 public votingDelay;

    /// @notice The duration of voting on a proposal, in blocks
    uint256 public votingPeriod;

    /// @notice The basis point number of votes required in order for a voter to become a proposer. *DIFFERS from GovernerBravo
    uint256 public proposalThresholdBPS;

    /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo
    uint256 public quorumVotesBPS;

    /// @notice The total number of proposals
    uint256 public proposalCount;

    /// @notice The address of the Nouns DAO Executor NounsDAOExecutor
    INounsDAOExecutor public timelock;

    /// @notice The address of the Nouns tokens
    NounsTokenLike public nouns;`







# [G-02] State variables can be packed into fewer storage slots (If variables occupying the same slot are both written the same 
function or by the constructor, avoids a separate Gsset (20000 gas). 
Reads of the variables are also cheaper):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 128):

    'address public admin;'















# [G-03] `<array>.length` should not be looked up in every loop of a `forloop` (Even memory arrays incur the overhead of bit tests and bit shifts to 
calculate the array length. Storage array length checks incur an extra 
Gwarmaccess (100 gas) PER-LOOP.):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 281):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 319):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 346):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 371):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 292):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 330):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 382):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`










# [G-04] `++i/i++` should be `unchecked{++i}`/`unchecked{++i}` when it is not possible for them to overflow, as is the case when used in `for` and `while` loops:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 281):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 319):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 346):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 371):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 292):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 330):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 382):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`







# [G-05] `require()`/`revert()` strings longer than 32 bytes cost extra gas:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 122-141):

    `require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
        require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 187-198):

    `        require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
        require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 203-210):

    `           require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 275-278):

    `require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 301-304):

    `require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 313-316):

    `require(
            state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 336-343):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

        Proposal storage proposal = proposals[proposalId];
        require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 366):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');`    

10. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 485):

    `require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');`

11. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 501-505):

    `        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
        Proposal storage proposal = proposals[proposalId];
        Receipt storage receipt = proposal.receipts[voter];
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`   

12. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 530-534):

    `require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );`  

13. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 546-550):

    `require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`               

14. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 563-568):

    `require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`        

15. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 581-585):

    `require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
        require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`  

16. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 617):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');`

17. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 133-148):

    ` require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );`

18. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 197-220):

    `require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
        require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

        temp.latestProposalId = latestProposalIds[msg.sender];
        if (temp.latestProposalId != 0) {
            ProposalState proposersLatestProposalState = state(temp.latestProposalId);
            require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );`

19. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 286):

    `require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );` 

20. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 312-315):

    `require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );`       

21. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 324-327):

    `require(
            state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );`

22. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 347-354):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

        Proposal storage proposal = _proposals[proposalId];
        require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );`

23. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375-377):

    `require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');` 

24. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 593-597):

    `        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
        Proposal storage proposal = _proposals[proposalId];
        Receipt storage receipt = proposal.receipts[voter];
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`    

25. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 622-626):

    `require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );`

26. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 638-642):

    ` require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`   

27. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 655-660):

    `require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );`  

28. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 674-685):

    `require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
        require(
            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );`               

29. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 702-712):

    `require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
        require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );`        

30. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 727):

    `require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');`  

31. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 801):

    `require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');`                  

32. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 819):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');'  

33. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 79-80):

    `       require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');'

34. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 140-142):

    `        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');`  

35. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 164):

    `require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');`                  

36. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 62):

    `require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');`  

37. File: 
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 77):

    `require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');`    






# [G-05] Not using the named return variables when a function returns, wastes deployment gas:



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 122-141):

    `return _delegate(msg.sender, delegatee);`












# [G-06] It costs more gas to initialize variables to zero than to let the default of zero be applied:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 281):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 319):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 346):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 371):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 292):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 330):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 382):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

10. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 223):

    `newProposal.eta = 0;` 

11. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 230-232):

    `        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;`  

12. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 231):

    `newProposal.eta = 0;`

13. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 238-240):

    `        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;` 

14. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 948):

    `uint256 lower = 0;`              

15. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 41):

    `uint8 public constant decimals = 0;` 

16. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 181):

    `uint32 lower = 0;`     









# [G-07] `++i` costs less gas than `i++`, especially when it’s used in forloops `(--i/i-- too`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 281):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 319):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 346):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 371):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 292):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 330):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 382):

    `for (uint256 i = 0; i < proposal.targets.length; i++) {`
    


  




# [G-08] Splitting `require()` statements that use && Cost gas:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 126-141):

    `        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
        require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 191-196):

    ` require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 531-534):

    `        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 547-550):

    `require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 564-568):

    `require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 582-585):

    `require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 617):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 137-148):

    ` require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );`    

10. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 201-206):

    `require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );`

11. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 623-626):

    `require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );`   

12. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 639-642):

    `require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`  

13. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 656-660):

    `require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );`               

14. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 677-681):

    `require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );`        

15. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 819):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');`  








# [G-09] Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead (> When using elements that are smaller than 32 bytes, your 
contract’s gas usage may be higher. This is because the EVM operates on 
32 bytes at a time. Therefore, if the element is smaller than that, the 
EVM must use more operations in order to reduce the size of the element 
from 32 bytes to the desired size.
> 

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html)
Use a larger size then downcast where needed):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 462):

    `uint8 support,`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 474-475):

    `   uint8 support,
        uint8 v,`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 499):

    `uint8 support` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 508):

    `uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);`       

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 535):

    `uint8 support,`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 539):

    `uint96 votes = castVoteInternal(msg.sender, proposalId, support);` 

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 554):

    `uint8 support,`       

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 566-567):

    `   uint8 support,
        uint8 v,` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 591-592):

    `       uint8 support
    ) internal returns (uint96) {`

10. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 600):

    `uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));` 

11. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 673):

    `function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {`       

12. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 687):

    `uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;`

13. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 701):

    `function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {`

14. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 714):

    `uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;` 

15. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 726):

    `function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {`       

16. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 730):

    `uint32 oldQuorumCoefficient = params.quorumCoefficient;` 

17. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 749-751):

    `        uint16 newMinQuorumVotesBPS,
        uint16 newMaxQuorumVotesBPS,
        uint32 newQuorumCoefficient`       

18. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 923):

    `uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');`  

19. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 965):

    `uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');`                    

20. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1019-1020):

    `        require(n <= type(uint32).max, errorMessage);
        return uint32(n);`  

21. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1024):

    `if (n > type(uint16).max) {`

22. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1027):

    `return uint16(n);`  

23. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 111-117):

    `    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

    /// @notice Emitted when maxQuorumVotesBPS is set
    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

    /// @notice Emitted when quorumCoefficient is set
    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);`  

24. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 216-220):

    `       bool hasVoted;
        /// @notice Whether or not the voter supports the proposal or abstains
        uint8 support;
        /// @notice The number of votes the voter had, which were cast
        uint96 votes;`

25. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 322-324):

    `        uint8 support;
        /// @notice The number of votes the voter had, which were cast
        uint96 votes;`    

26. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 352-357):

    `        uint16 minQuorumVotesBPS;
        /// @notice The maximum basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed.
        uint16 maxQuorumVotesBPS;
        /// @notice The dynamic quorum coefficient
        /// @dev Assumed to be fixed point integer with 6 decimals, i.e 0.2 is represented as 0.2 * 1e6 = 200000
        uint32 quorumCoefficient;`

27. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 363):

    `uint32 fromBlock;`

28. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 41):

    ` uint8 public constant decimals = 0`

29. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 53-56):

    `mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

    /// @notice The number of checkpoints for each account
    mapping(address => uint32) public numCheckpoints;`   

30. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 79):

    `function votesToDelegate(address delegator) public view returns (uint96) {` 

31. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 130):

    `uint8 v,` 

32. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 151-152):

    `   function getCurrentVotes(address account) external view returns (uint96) {
        uint32 nCheckpoints = numCheckpoints[account];` 

33. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 166):

    `uint32 nCheckpoints = numCheckpoints[account];`  

34. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 181-184):

    `        uint32 lower = 0;
        uint32 upper = nCheckpoints - 1;
        while (upper > lower) {
            uint32 center = upper - (upper - lower) / 2;` 

35. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 205):

    `uint96 amount = votesToDelegate(delegator);`                                   
36. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 213):

    `uint96 amount` 

37. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 217-238):

    `                uint32 srcRepNum = numCheckpoints[srcRep];
                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
                _writeCheckpoint(srcRep, srcRepNum, srcRepOld, srcRepNew);
            }

            if (dstRep != address(0)) {
                uint32 dstRepNum = numCheckpoints[dstRep];
                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');
                _writeCheckpoint(dstRep, dstRepNum, dstRepOld, dstRepNew);
            }
        }
    }

    function _writeCheckpoint(
        address delegatee,
        uint32 nCheckpoints,
        uint96 oldVotes,
        uint96 newVotes
    ) internal {
        uint32 blockNumber = safe32(`

38. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 255):

    `return uint32(n);`

39. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 260):

    `return uint96(n);`   

40. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 264-268):

    `        uint96 a,
        uint96 b,
        string memory errorMessage
    ) internal pure returns (uint96) {
        uint96 c = a + b;` 

41. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 274-275):

    `       uint96 a,
        uint96 b,` 

42. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 277):

    `) internal pure returns (uint96) {` 







# [G-10] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`(See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 97-101):

    `    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 479-483):

    `        bytes32 domainSeparator = keccak256(
            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
        );
        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 101-105):

    `   bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 571-575):

    `        bytes32 domainSeparator = keccak256(
            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
        );
        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 59-65):

    ` bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the delegation struct used by the contract
    bytes32 public constant DELEGATION_TYPEHASH =
        keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');`

6. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 134-138):

    `        bytes32 domainSeparator = keccak256(
            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
        );
        bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));`    



  


# [G-11] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 709-712):

    `require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );`    

    






# [G-12] `require()` or `revert()` statements that check input arguments should be at the top of the function (Checks that involve constants should come before checks that involve state variables):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 122-125):

    `require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');'    

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 485):

    `require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');`


3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 617):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 135-136):

    `
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');''

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375):

    `require(vetoer != address(0), 'NounsDAO::veto: veto power burned');'                  

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 819):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');'  

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 80):

    `require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');'

8. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 140):

    `        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');''           









# [G-13] Use custom errors rather than `revert()`/`require()` strings to save deployment gas:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 122-141):

    `require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
        require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 187-198):

    `        require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
        require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 203-210):

    `           require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );` 

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 275-278):

    `require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );`       

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 301-304):

    `require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 313-316):

    `require(
            state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 336-343):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

        Proposal storage proposal = proposals[proposalId];
        require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );` 

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 366):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');`    

10. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 485):

    `require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');`

11. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 501-505):

    `        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
        Proposal storage proposal = proposals[proposalId];
        Receipt storage receipt = proposal.receipts[voter];
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`   

12. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 530-534):

    `require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );`  

13. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 546-550):

    `require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`               

14. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 563-568):

    `require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`        

15. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 581-585):

    `require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
        require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );`  

16. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 617):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');`

17. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 133-148):

    ` require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );`

18. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 197-220):

    `require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
        require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

        temp.latestProposalId = latestProposalIds[msg.sender];
        if (temp.latestProposalId != 0) {
            ProposalState proposersLatestProposalState = state(temp.latestProposalId);
            require(
                proposersLatestProposalState != ProposalState.Active,
                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
            );
            require(
                proposersLatestProposalState != ProposalState.Pending,
                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
            );`

19. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 286):

    `require(
            state(proposalId) == ProposalState.Succeeded,
            'NounsDAO::queue: proposal can only be queued if it is succeeded'
        );` 

20. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 312-315):

    `require(
            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
        );`       

21. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 324-327):

    `require(
            state(proposalId) == ProposalState.Queued,
            'NounsDAO::execute: proposal can only be executed if it is queued'
        );`

22. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 347-354):

    `require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

        Proposal storage proposal = _proposals[proposalId];
        require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );`

23. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 375-377):

    `require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');` 

24. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 593-597):

    `        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
        Proposal storage proposal = _proposals[proposalId];
        Receipt storage receipt = proposal.receipts[voter];
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`    

25. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 622-626):

    `require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );`

26. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 638-642):

    ` require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );`   

27. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 655-660):

    `require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );`  

28. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 674-685):

    `require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
        require(
            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );`               

29. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 702-712):

    `require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
        require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );`        

30. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 727):

    `require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');`  

31. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 801):

    `require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');`                  

32. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 819):

    `require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');'  

33. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 79-80):

    `       require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');'

34. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 140-142):

    `        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');`  

35. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 164):

    `require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');`                  

36. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 62):

    `require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');`  

37. File: 
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 77):

    `require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');`    







# [G-14] Bitshift for divide by 2 (When multiply or dividing by a power of two, it is cheaper to bitshift than to use standard math operations.):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 951):

    `uint256 center = upper - (upper - lower) / 2;`

2. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 184):

    `uint32 center = upper - (upper - lower) / 2`    

### Recommended Mitigation Steps

Bitshift right by one bit instead of dividing by 2 to save gas.




# [G-15] Use a more recent version of solidity (Use a solidity version of at least 0.8.15 to have external calls skip
 contract existence checks if the external call has a return value):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 61):

    `pragma solidity ^0.8.6;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 53):

    `pragma solidity ^0.8.6;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 33):

    `pragma solidity ^0.8.6;` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 36):

    `pragma solidity ^0.8.6;`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 35):

    `pragma solidity ^0.8.6;`

6. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 28):

    `pragma solidity ^0.8.0;`    




# [G-16] Multiple `address` mappings can be combined into a single `mapping` of an `address` to a `struct`, where appropriate (Saves a storage slot for the mapping. Depending on the circumstances 
and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. 
Reads and subsequent writes can also be cheaper when a function requires
 both values and they both fit in the same storage slot):



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 53-56):

    `    mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

    /// @notice The number of checkpoints for each account
    mapping(address => uint32) public numCheckpoints;`




# [G-17] Remove unused local variable:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 789):

    `(bool sent, ) = msg.sender.call{ value: amount }('');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 983):

    `(bool refundSent, ) = msg.sender.call{ value: refundAmount }('');`    




# [G-18] Use calldata instead of memory for function parameters (Use calldata instead of memory for function parameters. Having function arguments use calldata instead of memory can save gas.):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 174-180):

    `   function propose(
        address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    ) public returns (uint256) {`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 294-300):

    `  function queueOrRevertInternal(
        address target,
        uint256 value,
        string memory signature,
        bytes memory data,
        uint256 eta
    ) internal {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 184-190):

    `    function propose(
        address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    ) public returns (uint256) {` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 305-311):

    `   function queueOrRevertInternal(
        address target,
        uint256 value,
        string memory signature,
        bytes memory data,
        uint256 eta
    ) internal {`       

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 533-537):

    ` function castRefundableVoteInternal(
        uint256 proposalId,
        uint8 support,
        string memory reason
    ) internal {`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 903-907):

    `  function dynamicQuorumVotes(
        uint256 againstVotes,
        uint256 totalSupply,
        DynamicQuorumParams memory params
    ) public pure returns (uint256) {`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1018):

    `function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 94):

    `function delegateTo(address callee, bytes memory data) internal {` 

9. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 253):

    `function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {`

10. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 258):

    `function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {`

11. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 263-267):

    `function add96(
        uint96 a,
        uint96 b,
        string memory errorMessage
    ) internal pure returns (uint96) {`

12. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 273-277):

    `    function sub96(
        uint96 a,
        uint96 b,
        string memory errorMessage
    ) internal pure returns (uint96) {`           

### Recommended Mitigation Steps

Change function arguments from memory to calldata.









# [G-19] Using bools for storage incurs overhead:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 789):

    `(bool sent, ) = msg.sender.call{ value: amount }('');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 983):

    `(bool refundSent, ) = msg.sender.call{ value: refundAmount }('');`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 204-208):

    `        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 216):

    `bool hasVoted;`

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 304-308):

    `        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 320):

    `bool hasVoted;`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 390-394):

    `        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;`

            






# [G-20] Using `private` rather than `public` for constants, saves gas:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 67-101):

    ` string public constant name = 'Nouns DAO';

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

    /// @notice The EIP-712 typehash for the contract's domain
    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 59-105):

    `    string public constant name = 'Nouns DAO';

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

    /// @notice The EIP-712 typehash for the contract's domain
    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');`

3. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 41):

    `uint8 public constant decimals = 0;`

4. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 59-64):

    `   bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the delegation struct used by the contract
    bytes32 public constant DELEGATION_TYPEHASH =
        keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');`





# [G-21] Empty blocks should be removed or emit something (The code should be refactored such that they no longer exist, or the 
block should do something useful, such as emitting an event or 
reverting. If the contract is meant to be extended, the contract should 
be `abstract` and the function signatures be added without 
any default implementation. If the block is an empty if-statement block 
to avoid doing subsequent checks in the else-if/else conditions, the 
else-if/else conditions should be nested under the negation of the 
if-statement, because they involve different classes of checks, which 
may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1030):

    `receive() external payable {}`





# [G-22] Don’t compare boolean expressions to boolean literals (`if (<x> == true) `=> `if (<x>)`, `if (<x> == false)` => `if (!<x>)`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 505):

    ` require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 597):

    `require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');`







# [G-23] Optimize names to save gas (`public`/`external` function names and public member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9)
 link for an example of how it works. Below are the interfaces/abstract 
contracts that can be optimized so that the most frequently-called 
functions use the least amount of gas possible during method lookup. 
Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)):



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 39):

    `abstract contract ERC721Checkpointable is ERC721Enumerable {`

2. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 38):

    `abstract contract ERC721Enumerable is ERC721, IERC721Enumerable {`

