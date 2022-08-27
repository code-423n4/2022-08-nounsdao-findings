
## Avoid initialization with default values

If a variables is not initialized, it is assumed to contain default values (0, address(0), false), explicitly intialising with default values is not necessary

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L181

```
        uint32 lower = 0;   

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L223

```
        newProposal.eta = 0;
        ...
        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;   
        newProposal.abstainVotes = 0;
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L231

```
        newProposal.eta = 0;
        ...
        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L948

```
        uint256 lower = 0;  
```

## Unnecessary condition

### Proof of concept

`msg.sender` != `address(0)`

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L818

```
    require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

## Functions can be external 

Function that are not called within the contract can be external instead of public

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L54

```
    function supportsInterface(bytes4 interfaceId) public view virtual override(IERC165, ERC721) returns (bool) {
        return interfaceId == type(IERC721Enumerable).interfaceId || super.supportsInterface(interfaceId);
    }
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L61

```    
    function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {
        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds'); 
        return _ownedTokens[owner][index];
    }

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L112

```
    function delegate(address delegatee) public {
        if (delegatee == address(0)) delegatee = msg.sender;
        return _delegate(msg.sender, delegatee);
    }
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L126

```
    function delegateBySig(
        address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L163

```
    function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {

```

## Long revert strings

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met. Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L62

```
    require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds'); 

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L77

```
    require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds'); 
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L80

```        
    return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');   
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140

```
    require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');  
    require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
    require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L164

```
    require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined'); 

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L219

```
    uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows'); 

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L226

```
    uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows'); 

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L238

```
    uint32 blockNumber = safe32(
            block.number,
            'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'
        );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79

```
    require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only'); 
    require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address'); 
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133

```
    require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
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
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197

```
    require(
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
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L213

```
    require(
        proposersLatestProposalState != ProposalState.Active,
        'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
    );
    require(
        proposersLatestProposalState != ProposalState.Pending,
        'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L286

```
    require(
        state(proposalId) == ProposalState.Succeeded,
        'NounsDAO::queue: proposal can only be queued if it is succeeded'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L312

```
    require(
        !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
        'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L324

```
    require(
        state(proposalId) == ProposalState.Queued,
        'NounsDAO::execute: proposal can only be executed if it is queued'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347

```
    require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L350

```
    require(
        msg.sender == proposal.proposer ||
            nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
        'NounsDAO::cancel: proposer above threshold'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L375

```
    require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
    require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
    require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L433

```
    require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L577

```
    require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L593

```
    require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
    require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
    ...
    require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L622

```
    require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
    require(
        newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
        'NounsDAO::_setVotingDelay: invalid voting delay'
    );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L638

```
        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L655

```
        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L674

```
        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
        require(
            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L702

```
        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
        require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L727

```
        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L801

```
        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L819

```
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840

```
        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853

```
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

## Use custom errors

Custom errors from solidity 0.8.4 are more efficient than revert strings with cheaper deployment and runtime time costs when revert condition is met

Refer: https://blog.soliditylang.org/2021/04/21/custom-errors/](https://blog.soliditylang.org/2021/04/21/custom-errors/

## Add `unchecked` to save gas

In Solidity 0.8.4 and above there is a default underflow/overflow checks on unsigned integers. it is possible to avoid this check by using `unchecked`. If the expression does not cause overflow/underflow, it can be unchecked to save some gas. 

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L279

```
        require(b <= a, errorMessage);
        return a - b;

```

## Use function params instead of storage values in events

Storage reads in events can be replaced with function parameters and local variables to save gas

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L85

`implementation` can be replaced by `implementation_`

```
emit NewImplementation(oldImplementation, implementation);

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L240

`newProposal.startBlock`, `newProposal.endBlock` `newProposal.proposalThreshold` can be replaced by `temp.startblock`, `temp.endBlock`, `temp.proposalThreshold`

```
    emit ProposalCreated(
        newProposal.id,
        msg.sender,
        targets,
        values,
        signatures,
        calldatas,
        newProposal.startBlock,
        newProposal.endBlock,
        description
    );

    emit ProposalCreatedWithRequirements(
        newProposal.id,
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
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L538

Use `newVotingDelay` instead of `votingDelay` 

```
        emit VotingDelaySet(oldVotingDelay, votingDelay); 
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L554

Use `newVotingPeriod` instead of `votingPeriod`

```
        emit VotingPeriodSet(oldVotingPeriod, votingPeriod); 
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L572

Use `newProposalThresholdBPS` instead of `proposalThresholdBPS`

```
        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);  
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L589

Use `newQuorumVotesBPS` instead of `quorumVotesBPS`

```
        emit QuorumVotesBPSSet(oldQuorumVotesBPS, quorumVotesBPS); 
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L629

Use `oldPendingAdmin`, address(0) instead of `admin`, `pendingAdmin`

```
        emit NewAdmin(oldAdmin, admin); 
        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);  
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L640

Use `msg.sender` instead of `vetoer`

```
        emit NewVetoer(vetoer, newVetoer);
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L250

`newProposal.startBlock`, `newProposal.endBlock` `newProposal.proposalThreshold` can be replaced by `temp.startblock`, `temp.endBlock`, `temp.proposalThreshold`

```
        emit ProposalCreated(
            newProposal.id,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            description
        );
        ...
        emit ProposalCreatedWithRequirements(
            newProposal.id,
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

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L630

Use `newVotingDelay` instead of `votingDelay`

```
        emit VotingDelaySet(oldVotingDelay, votingDelay);
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L646

Use `newVotingPeriod` instead of `votingPeriod`

```
        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L664

Use `newProposalThresholdBPS` instead of `proposalThresholdBPS`

```
        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L831

Use `oldPendingAdmin`, address(0) instead of `admin`, `pendingAdmin`

```
        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
```

## Cache storage values and save gas

Storage values that are read multiple times in the same function can be cached to avoid expensive SLOAD and save gas

### Proof of concept

`proposalCount` can be cached in a variable 

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L216

```
        proposalCount++;
        Proposal storage newProposal = proposals[proposalCount];
        newProposal.id = proposalCount;
```

`proposalCount` can be cached in a variable 

```
        proposalCountTemp = ++proposalCount;
        Proposal storage newProposal = proposals[proposalCountTemp];
        newProposal.id = proposalCountTemp;
        ...
        return proposalCountTemp;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226

```
        proposalCount++;
        Proposal storage newProposal = _proposals[proposalCount];
        newProposal.id = proposalCount;
```
`proposalCount` can be cached in a variable 

```
        proposalCountTemp = ++proposalCount;
        Proposal storage newProposal = proposals[proposalCountTemp];
        newProposal.id = proposalCountTemp;
        ...
        return proposalCountTemp;
```

## Cache array length to save gas in for-loops 

Array length can be cached and used in the loop condition instead of reading it in every iteration and save gas

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L346

```
        for (uint256 i = 0; i < proposal.targets.length; i++) 
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371

```
        for (uint256 i = 0; i < proposal.targets.length; i++) 
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382

```
        for (uint256 i = 0; i < proposal.targets.length; i++)
```

## Boolean comparison with constant

Boolean comparison with a boolean constant is not necessary

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505

```
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');  
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597

```
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted'); 
```

## Shift right instead of divide by 2

Division by 2 can be performed by shifting right by 1 bit which costs 3 gas 

### Proof of concept

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L951

```
            uint256 center = upper - (upper - lower) / 2;
```

Replace `/` with `>>`

```
            uint256 center = upper - (upper - lower) >> 2;
```