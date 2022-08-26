  # Gas Optimizations

## Summary Of Findings:

  | Issue 
-- | -- 
1 | `propose` function in `NounsDAOLogicV1` and `NounsDAOLogicV2` contract
2 | Optimizing the `for` loops
3 | Placing emit statements before state-variable update to save gas
4 | Remove redundant `safe32` function.
5 | Use custom errors instead of revert strings

## Detailed Report on Gas Optimization Findings:

### 1. <ins>`propose` function in `NounsDAOLogicV1` and `NounsDAOLogicV2` contract</ins>
Several gas optimizations can be applied to the [propose](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L174-L268) function. 

 1. State variable `proposalCount` is accessed 3 times, which could be cached to save gas. Also incrementing of this variable could be converted to `++proposalCount` to save extra gas. 
```solidity
        uint256 cachedproposalCount = ++proposalCount;
        Proposal storage newProposal = proposals[cachedproposalCount];
        newProposal.id = cachedproposalCount;
```
 2. Several fields in the `Proposal` struct are written with their default values. These statements are redundant and can be removed to save gas.
```solidity
        newProposal.eta = 0;
        newProposal.forVotes = 0;
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0;
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
```
 3. [Line 237](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L237) could be rewritten to save local variables.
```solidity
        latestProposalIds[newProposal.proposer] = newProposal.id;
    //could be rewritten as:
        latestProposalIds[newProposal.proposer] = cachedproposalCount;
```
 4. The `emit` statements could use local variables, instead of the state variables.
```solidity
        emit ProposalCreated(
            cachedproposalCount,    //gas saving
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            temp.startBlock,    //gas saving
            temp.endBlock,    //gas saving
            description
        );

        /// @notice Updated event with `proposalThreshold` and `quorumVotes`
        emit ProposalCreatedWithRequirements(
            cachedproposalCount,    //gas saving
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            temp.startBlock,    //gas saving
            temp.endBlock,    //gas saving
            temp.proposalThreshold,    //gas saving
            newProposal.quorumVotes,
            description
        );
```
 5. The function is returning a recently written state variable, instead of which we could return the local variable.
```solidity
      return newProposal.id;
   // can be written as:
      return cachedproposalCount;
```

Hardhat gas report for  `propose` function:

  | Original Report | Optimized Report | Gas savings
-- | --   | -- | -- 
Min | 348312 | 336224 | 12088
Max | 599686 | 587598 | 12088      
Avg | 433213 | 421119  | 12094

We can see that we saved an average of 12094 gas.

As for `NounsDAOLogicV2` contract, we are using the [same function](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L184-L279) there as well, so we can account for a saving of 12000 gas there too.

Total Amount of gas saved so far = 12000 + 12000 = **24000**.

### 2. <ins>Optimizing the `for` loops</ins>

The `for` loops in the code base generally follow the following pattern:
```solidity
      for (uint256 i = 0; i < proposal.targets.length; i++) {
            // do something
        }
```
This can be optimized in 3 ways:
```solidity
      len = proposal.targets.length;  // 1. cache the array length
      for (uint256 i; i < len; ) {  // 2. remove redundant initialization of i
            // do something
            unchecked {  // 3. using unchecked block for incrementing the index
              ++i;   //use pre-increment instead of post-increment
            }
        }
```

The following instances could be optimized in this way:

 1. [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol)
```solidity
Line 292:        for (uint256 i = 0; i < proposal.targets.length; i++) 
Line 330:        for (uint256 i = 0; i < proposal.targets.length; i++)
Line 357:        for (uint256 i = 0; i < proposal.targets.length; i++)
Line 382:        for (uint256 i = 0; i < proposal.targets.length; i++)
```
 2. [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol) uses the same set of functions as NounsDAOLogicV2.
```solidity
Line 281:        for (uint256 i = 0; i < proposal.targets.length; i++) 
Line 319:        for (uint256 i = 0; i < proposal.targets.length; i++)
Line 346:        for (uint256 i = 0; i < proposal.targets.length; i++)
Line 371:        for (uint256 i = 0; i < proposal.targets.length; i++)
```

After the above `for` loops were optimized, the gas savings as per Hardhat were as follows:

<ins>Hardhat Gas report:</ins>
  | Contract |  Method  | Original Avg Gas Cost  | Optimized Avg Gas Cost  | Avg Gas Saved  
-- | :--:| :--:| :--:  | :--: | :--:    
1 | NounsDAOLogicV1 |  queue | 148150  | 147935 | 215
2 | NounsDAOLogicV1 |   execute | 170128  | 169958 | 170
3 | NounsDAOLogicV1 |  cancel | 99962  | 99921 | 41
4 | NounsDAOLogicV1 |  veto | 96416  | 96330 | 86

Total gas saved in the V1 contract alone is 512.

`NounsDAOLogicV2` implements the same set of functions, so the total gas saved in both of these contracts = 512 + 512 = **1024**.

### 3. <ins>Placing emit statements before state-variable update to save gas</ins>
The emit statements are generally placed at the end of a function. But sometimes, placing them before we update an admin variable can save gas. This is already done in the `_setVetoer` function in both [NounsDAOLogicV1](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L637) and [NounsDAOLogicV2](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L839) contracts:
```solidity
    function _setVetoer(address newVetoer) public {
        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
        emit NewVetoer(vetoer, newVetoer);  //emitting before updating state variable.
        vetoer = newVetoer;
    }
``` 

The same could be done in the following functions as well to save gas.
 1. [_setVotingDelay](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L529) function.
 2. [_setVotingPeriod](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L545) function.
 3. [_setProposalThresholdBPS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L562) function.
 4. [_setPendingAdmin](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L597) function.
 5. [_setVotingDelay](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L621) function.
 6. [_setVotingPeriod](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L637) function.
 7. [_setProposalThresholdBPS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L654) function.
 8. [_setPendingAdmin](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L799) function.
 9. `_acceptAdmin` function in [NounsDAOLogicV1](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L615) and [NounsDAOLogicV2](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L817) can do a few more optimizations as given below:

```solidity
    function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
        address cachedpendingAdmin = pendingAdmin;   //cache state variable into local storage
        emit NewAdmin(admin, cachedpendingAdmin);   //first emit
        emit NewPendingAdmin(cachedpendingAdmin, address(0));   //second emit

        // Store admin with value pendingAdmin
        admin = cachedpendingAdmin;    //update state variable from local storage

        // Clear the pending value
        pendingAdmin = address(0);    //update another state variable
    }
```

After the above rearrangements of `emit` statements, Hardhat showed a gas saving of 25 gas per function. Since we have 5 functions each in 2 of these contracts, we are able to save 25 * 5 * 2 = 250 gas in total.

### 4. <ins>Remove redundant `safe32` function</ins>
`safe32` function is used in `NounsDAOLogicV2` contract to make sure that the current `block.number` is less than 2 ^ 32. But this in an unnecessary concern as at the current rate at which blocks are processed, it will still take more than 1500 years to reach the limit. So we can just safely typecast it into an uint32. 
```solidity
    uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
//becomes
    uint32 blockNumber = uint32(block.number);
```
The saved gas was observed through the gas savings of `_setDynamicQuorumParams` method which uses `safe32` in one of its internal functions. The average gas savings was 280. 

### 5. <ins>Use custom errors instead of revert strings</ins>
Another major area in which we could save deployment cost would be in converting the `revert` strings into custom errors. If the function does revert, you can also save on dynamic gas cost. See this [example implementation](https://gist.github.com/El-Ku/dae597f37073673332ce02be2bbe731b) to understand the dynamics of custom errors. It shows that each `require` string converted into a custom error saves you around 11000 gas.

The codebase uses some custom errors already. The rest is listed below:

 1. [NounsDAOProxy.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol) (2 instances)
```solidity
Line 079:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
Line 080:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');     
```
 2. [ERC721Checkpointable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol) (4 instances)
```solidity
Line 140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
Line 141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
Line 142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
Line 164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');       
```
 3. [ERC721Enumerable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol) (2 instances) 
```solidity
Line 062:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
Line 077:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```
 4. [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol) (39 instances) 
```solidity
Line 122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
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
                 );    

Line 187:        require(
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
                 require(
                      proposersLatestProposalState != ProposalState.Active,
                      'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                 );
                 require(
                      proposersLatestProposalState != ProposalState.Pending,
                      'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                 );

Line 275:        require(
                      state(proposalId) == ProposalState.Succeeded,
                      'NounsDAO::queue: proposal can only be queued if it is succeeded'
                  );
Line 301:        require(
                    !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                    'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                );

Line 313:        require(
                    state(proposalId) == ProposalState.Queued,
                    'NounsDAO::execute: proposal can only be executed if it is queued'
                );

Line 336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

Line 339:        require(
                    msg.sender == proposal.proposer ||
                        nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                    'NounsDAO::cancel: proposer above threshold'
                );

Line 364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
                 require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
                 require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

Line 422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
Line 485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
Line 501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
Line 502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
Line 505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
Line 530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
Line 531:        require(
                    newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                    'NounsDAO::_setVotingDelay: invalid voting delay'
                );
Line 546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
Line 547:        require(
                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                      'NounsDAO::_setVotingPeriod: invalid voting period'
                  );
Line 563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
                 require(
                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                  );
Line 581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
                 require(
                    newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
                    'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                 );
Line 599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
Line 617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
Line 638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
Line 651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');       
```
 5. [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol) (43 instances) 
```solidity
Line 133:       require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
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
Line 197:       require(
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
Line 213:       require(
                    proposersLatestProposalState != ProposalState.Active,
                    'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                );
                require(
                    proposersLatestProposalState != ProposalState.Pending,
                    'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                );
Line 286:       require(
                    state(proposalId) == ProposalState.Succeeded,
                    'NounsDAO::queue: proposal can only be queued if it is succeeded'
                );
Line 312:       require(
                    !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                    'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                );
Line 324:       require(
                    state(proposalId) == ProposalState.Queued,
                    'NounsDAO::execute: proposal can only be executed if it is queued'
                );
Line 347:       require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
Line 350:       require(
                    msg.sender == proposal.proposer ||
                        nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                    'NounsDAO::cancel: proposer above threshold'
                );
Line 375:       require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
                require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
                require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
Line 433:       require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
Line 577:       require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
Line 593:       require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
                require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
Line 597:       require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
Line 622:       require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
                require(
                    newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                    'NounsDAO::_setVotingDelay: invalid voting delay'
                );
Line 638:       require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
                require(
                    newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                    'NounsDAO::_setVotingPeriod: invalid voting period'
                );
Line 655:       require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
                require(
                    newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                        newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                    'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
                );
Line 674:       require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
Line 677:       require(
                    newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                        newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
                    'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
                );
                require(
                    newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
                    'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
                );
Line 702:       require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
Line 705:       require(
                    newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
                    'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
                );
                require(
                    params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
                    'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
                );
Line 727:       require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
Line 801:       require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
Line 819:       require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
Line 840:       require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
Line 853:       require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');     
```

Total number of instances = 2 + 4 + 2 + 39 + 43 = 90.
Approximate deployment cost that can be saved = 90 * 11000 = 990,000.

## Conclusions:

The above 4 major changes saved a very significant amount of gas in the overall code base. The exact amounts are tabulated below:

  | Issue | Gas Saved
-- | -- | -- 
1 | `propose` function in `NounsDAOLogicV1` and `NounsDAOLogicV2` contract | 24000
2 | Optimizing the `for` loops | 1024
3 | Placing emit statements before state-variable update to save gas | 250
4 | Remove redundant `safe32` function | 280
5 | Use custom errors instead of revert strings | *
*saved deployment cost

### TOTAL GAS SAVED = 24000 + 1024 + 250 + 280 = <ins>25554</ins>.
### Total deployment gas saved = <ins>990000</ins> (approximately 1 million).
