## Table of Contents
Total of 19 Issues Found.
- Remove Dead Code
- Should Use Unchecked Block where Over/Underflow is not Possible
- Minimize the Number of SLOADs by Caching State Variable
- Defined Variables Used Only Once
- Redundant Use of Variable
- Unchanging State Variable Should be Immutable
- Use require instead of &&
- Change Function Visibility Public to External
- Internal Function Called Only Once can be Inlined
- Use Bit Shifting Instead of Multiplication/Division of 2
- Boolean Comparisons
- Constant Value of a Call to keccak256() should Use Immutable
- Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas
- Unnecessary Default Value Initialization
- Store Array's Length as a Variable
- ++i Costs Less Gas than i++
- Empty Blocks Should Emit Something or be Removed
- Keep The Revert Strings of Error Messages within 32 Bytes
- Use Custom Errors to Save Gas

&ensp;
### Remove Dead Code

#### Issue
State variable of quorumVotesBPS is never initialized. 
Therefore this variable will remain 0 so it is better to remove it to save gas.

#### PoC
Total of 1 instance found.

NounsDAOStorageV1Adjusted.quorumVotesBPS
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L257
Used at NounsDAOLogicV2.getDynamicQuorumParamsAt()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L922-L946

#### Mitigation
Remove the variable to save gas.

&ensp;
### Should Use Unchecked Block where Over/Underflow is not Possible

#### Issue
Since Solidity 0.8.0, all arithmetic operations revert on overflow and underflow by default.
However in places where overflow and underflow is not possible, it is better to use unchecked block to reduce the gas usage.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#checked-or-unchecked-arithmetic

#### PoC
Total of 1 instance found.

1. ERC721Checkpointable.sol:sub96()
```
Wrap line 279 with unchecked since underflow is not possible due to line 278 check
278:        require(b <= a, errorMessage);
279:        return a - b;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L278-L279

#### Mitigation
Wrap the code with uncheck like described in above PoC. 

&ensp;
### Minimize the Number of SLOADs by Caching State Variable

#### Issue
SLOADs cost 100 gas where MLOADs/MSTOREs cost only 3 gas.
Whenever function reads storage value more than once, it should be cached to save gas.

#### PoC
Total of 11 instances found.

1. Cache vetoer of veto() of NounsDAOLogicV2.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L375-L376

2. Cache vetoer of _setVetoer() of NounsDAOLogicV2.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840-L842

3. Cache quorumVotesBPS of getDynamicQuorumParamsAt() of NounsDAOLogicV2.sol
4 SLOADs to 1 SLOAD, 1 MSTORE and 4 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L929-L930
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L942-L943

4. Cache proposalCount of propose() of NounsDAOLogicV2.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L227-L228

5. Cache timelock of queueOrRevertInternal() of NounsDAOLogicV2.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L313
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L316

6. Cache nouns of propose() of NounsDAOLogicV2.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L193
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L198

7. Cache vetoer of propose() of NounsDAOLogicV1.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L364-L365

8. Cache vetoer of _setVetoer() of NounsDAOLogicV1.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L638-L640

9. Cache proposalCount of propose() of NounsDAOLogicV1.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L217-L219

10. Cache timelock of queueOrRevertInternal() of NounsDAOLogicV1.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L302
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L305

11. Cache nouns of propose() of NounsDAOLogicV1.sol
2 SLOADs to 1 SLOAD, 1 MSTORE and 2 MLOAD
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L183
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L188

#### Mitigation
When certain state variable is read more than once, cache it to local variable to save gas.

&ensp;
### Defined Variables Used Only Once

#### Issue
Certain variables is defined even though they are used only once.
Remove these unnecessary variables to save gas.
For cases where it will reduce the readability, one can use comments to help describe
what the code is doing.

#### PoC
Total of 1 instance found.

1. Remove startGas variable of castRefundableVoteInternal() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L538
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L542
Mitigation:
Delete line 538 and replace line 542 like shown below
```
            _refundGas(gasleft());
```

#### Mitigation
Don't define variable that is used only once.
Details are listed on above PoC.

&ensp;
### Redundant Use of Variable

#### Issue
Below has redundant use of variables. 
Instead of defining a new variable, emit the event first and then update the variable.

#### PoC
Total of 14 instances found.

1.  _setVotingDelay() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L621-L631
Change it to
```
    function _setVotingDelay(uint256 newVotingDelay) external {
        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
        emit VotingDelaySet(votingDelay, newVotingDelay);

        votingDelay = newVotingDelay;

    }
```

2.  _setVotingPeriod() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L637-L647
Change it to
```
    function _setVotingPeriod(uint256 newVotingPeriod) external {
        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
        emit VotingPeriodSet(votingPeriod, newVotingPeriod);

        votingPeriod = newVotingPeriod;
    }
```

3.  _setProposalThresholdBPS() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L654-L665
Change it to
```
    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {
        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
        emit ProposalThresholdBPSSet(proposalThresholdBPS, newProposalThresholdBPS);

        proposalThresholdBPS = newProposalThresholdBPS;
    }
```

4.  _setMinQuorumVotesBPS() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L673-L693
Change it to
```
    function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {
        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
        require(
            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
        );

        emit MinQuorumVotesBPSSet(params.minQuorumVotesBPS, newMinQuorumVotesBPS);

        params.minQuorumVotesBPS = newMinQuorumVotesBPS;

        _writeQuorumParamsCheckpoint(params);
    }
```

5.  _setMaxQuorumVotesBPS() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L701-L720
Change it to
```
    function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {
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

        emit MaxQuorumVotesBPSSet(params.maxQuorumVotesBPS, newMaxQuorumVotesBPS);

        params.maxQuorumVotesBPS = newMaxQuorumVotesBPS;

        _writeQuorumParamsCheckpoint(params);
    }
```

6.  _setQuorumCoefficient() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726-L736
Change it to
```
    function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {
        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
        DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);

        emit QuorumCoefficientSet(params.quorumCoefficient, newQuorumCoefficient);

        params.quorumCoefficient = newQuorumCoefficient;

        _writeQuorumParamsCheckpoint(params);
    }
```

7.  _setPendingAdmin() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L799-L811
Delete line 804 and emit event first.
```
    function _setPendingAdmin(address newPendingAdmin) external {
        // Check caller = admin
        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

        emit NewPendingAdmin(pendingAdmin, newPendingAdmin);

        pendingAdmin = newPendingAdmin;
    }
```

8.  _acceptAdmin() of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L817-L833
Delete line 822 and 823 and emit event first.
```
    function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ‚â† address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

        emit NewAdmin(admin, pendingAdmin);
        emit NewPendingAdmin(pendingAdmin, address(0));

        // Store admin with value pendingAdmin
        admin = pendingAdmin;

        // Clear the pending value
        pendingAdmin = address(0);
    }
```


9.  _setVotingDelay() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L529-L539
Change it to
```
    function _setVotingDelay(uint256 newVotingDelay) external {
        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
        emit VotingDelaySet(votingDelay, newVotingDelay);

        votingDelay = newVotingDelay;

    }
```

10.  _setVotingPeriod() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L545-L555
Change it to
```
    function _setVotingPeriod(uint256 newVotingPeriod) external {
        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
        require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
        emit VotingPeriodSet(votingPeriod, newVotingPeriod);

        votingPeriod = newVotingPeriod;
    }
```

11.  _setProposalThresholdBPS() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L562-L573
Change it to
```
    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {
        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
        emit ProposalThresholdBPSSet(proposalThresholdBPS, newProposalThresholdBPS);

        proposalThresholdBPS = newProposalThresholdBPS;
    }
```

12.  _setQuorumVotesBPS() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L580-L590
Change it to
```
    function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external {
        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
        require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
        emit QuorumVotesBPSSet(quorumVotesBPS, newQuorumVotesBPS);
        quorumVotesBPS = newQuorumVotesBPS;
    }
```

13.  _setPendingAdmin() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L597-L609
Change it to
```
    function _setPendingAdmin(address newPendingAdmin) external {
        // Check caller = admin
        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

        emit NewPendingAdmin(pendingAdmin, newPendingAdmin);

        pendingAdmin = newPendingAdmin;
    }
```

14.  _acceptAdmin() of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L615-L631
Change it to
```
    function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ‚Äö√¢‚Ä† address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

        emit NewAdmin(admin, pendingAdmin);
        emit NewPendingAdmin(pendingAdmin, address(0));

        // Store admin with value pendingAdmin
        admin = pendingAdmin;

        // Clear the pending value
        pendingAdmin = address(0);
    }
```


#### Mitigation
Instead of defining a new variable, emit the event first and then update the variable like shown in above PoC.

&ensp;
### Unchanging State Variable Should be Immutable

#### Issue
State variable that is only set in the constructor and can't be changed afterwards,
should be declared as immutable.

#### PoC
Total of 4 instances found.

1. timelock variable of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L263

2. nouns variable of NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L266

3. timelock variable of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L163

4. nouns variable of NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L166

#### Mitigation
Change to immutable.

&ensp;
### Use require instead of &&

#### Issue
When there are multiple conditions in require statement, break down the require statement into
multiple require statements instead of using && can save gas.

#### PoC
Total of 19 instances found.
```
./NounsDAOLogicV2.sol:137:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:141:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:145:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:201:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV2.sol:623:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:639:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:656:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:677:        require(
                                      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                                          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
                                      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
                                  );
./NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
./NounsDAOLogicV1.sol:126:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:130:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:134:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:138:        require(
                                      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:191:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV1.sol:531:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:547:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:564:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:582:        require(
                                      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

#### Mitigation
Break down into several require statement.
For example,
```
require(votingPeriod_ >= MIN_VOTING_PERIOD);
require(votingPeriod_ <= MAX_VOTING_PERIOD, 'NounsDAO::initialize: invalid voting period');
```

&ensp;
### Change Function Visibility Public to External

#### Issue
If the function is not called internally, it is cheaper to set your function visibility to external instead of public.

#### PoC
Total of 9 instances found.

1. NounsDAOLogicV2.sol:propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L184

2. NounsDAOLogicV2.sol:_burnVetoPower()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L851

3. NounsDAOLogicV2.sol:proposalThreshold()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L862

4. NounsDAOLogicV2.sol:maxQuorumVotes()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1002

5. NounsDAOLogicV1.sol:propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174

6. NounsDAOLogicV1.sol:_burnVetoPower()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L649

7. NounsDAOLogicV1.sol:proposalThreshold()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L660

8. NounsDAOLogicV1.sol:quorumVotes()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L668

9. ERC721Checkpointable.sol:delegateBySig()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L126

#### Mitigation
Change the function visibility to external.

&ensp;
### Internal Function Called Only Once Can be Inlined

#### Issue
Certain function is defined even though it is called only once.
Inline it instead to where it is called to avoid usage of extra gas.

#### PoC
Total of 11 instances found.

1. queueOrRevertInternal() of NounsDAOLogicV2.sol
This function called only once at line 293
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L305-L317
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L293

2. proposalCreationBlock() of NounsDAOLogicV2.sol
This function called only once at line 600
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L866-L871
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L600

3. _refundGas() of NounsDAOLogicV2.sol
This function called only once at line 542
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L974-L986
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L542

4. getChainIdInternal() of NounsDAOLogicV2.sol
This function called only once at line 572
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1010-L1016
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L572

5. queueOrRevertInternal() of NounsDAOLogicV1.sol
This function called only once at line 282
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L294-L306
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L282

6. getChainIdInternal() of NounsDAOLogicV1.sol
This function called only once at line 480
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L676-L682
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L480

7. safe32() of ERC721Checkpointable.sol
This function called only once at line 238
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L253-L256
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L238

8. safe96() of ERC721Checkpointable.sol
This function called only once at line 80
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L258-L261
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L80

9. add96() of ERC721Checkpointable.sol
This function called only once at line 226
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L263-L271
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L226

10. sub96() of ERC721Checkpointable.sol
This function called only once at line 219
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L273-L280
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L219

11. getChainId() of ERC721Checkpointable.sol
This function called only once at line 135
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L282-L288
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L135

#### Mitigation
I recommend to not define above functions and instead inline it at place it is called.

&ensp;
### Use Bit Shifting Instead of Multiplication/Division of 2

#### Issue
The MUL and DIV opcodes cost 5 gas but SHL and SHR only costs 3 gas.
Since MUL/DIV and SHL/SHR result the same, use cheaper bit shifting.

#### PoC
Total of 2 instances found.
```solidity
./NounsDAOLogicV2.sol:951:            uint256 center = upper - (upper - lower) / 2;
./ERC721Checkpointable.sol:184:            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```

#### Mitigation
Use bit shifting instead of multiplication/division.
Example:
```solidity
uint256 center = upper - (upper - lower) / 2; 
Good:
uint256 center = upper - (upper - lower) >> 2; 
```

&ensp;
### Boolean Comparisons

#### Issue
It is more gas expensive to compare boolean with "variable == true" or "variable == false" than 
directly checking the returned boolean value.

#### PoC
Total of 2 instances found.
```
./NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
./NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

#### Mitigation
Simply check by returned boolean value.
Change it to
```
require(!receipt.hasVoted)
```

&ensp;
### Constant Value of a Call to keccak256() should Use Immutable

#### Issue
When using constant it is expected that the value should be converted into a constant value at compile time.
However when using a call to keccak256(), the expression is re-calculated each time the constant is referenced.
Resulting in costing about 100 gas more on each access to this constant.
link for more details: https://github.com/ethereum/solidity/issues/9232

#### PoC
Total of 6 instances found.
```
./NounsDAOLogicV2.sol:101:    bytes32 public constant DOMAIN_TYPEHASH =
                                keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
./NounsDAOLogicV2.sol:105:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
./NounsDAOLogicV1.sol:97:    bytes32 public constant DOMAIN_TYPEHASH =
                                keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
./NounsDAOLogicV1.sol:101:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
./ERC721Checkpointable.sol:59:    bytes32 public constant DOMAIN_TYPEHASH =
                                    keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
./ERC721Checkpointable.sol:63:    bytes32 public constant DELEGATION_TYPEHASH =
                                    keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
```

#### Mitigation
Change the variable from constant to immutable.

&ensp;
### Using Elements Smaller than 32 bytes (256 bits) Might Use More Gas

#### Issue
Since EVM operates on 32 bytes at a time, if the element is smaller than that, the EVM must use more operations
in order to reduce the elements from 32 bytes to specified size.

Reference: https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html

#### PoC
Total of 60 instances found.
```
./NounsDAOLogicV2.sol:520:        uint8 support,
./NounsDAOLogicV2.sol:535:        uint8 support,
./NounsDAOLogicV2.sol:539:        uint96 votes = castVoteInternal(msg.sender, proposalId, support);
./NounsDAOLogicV2.sol:554:        uint8 support,
./NounsDAOLogicV2.sol:566:        uint8 support,
./NounsDAOLogicV2.sol:567:        uint8 v,
./NounsDAOLogicV2.sol:591:        uint8 support
./NounsDAOLogicV2.sol:592:    ) internal returns (uint96) {
./NounsDAOLogicV2.sol:600:        uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));
./NounsDAOLogicV2.sol:673:    function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {
./NounsDAOLogicV2.sol:687:        uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;
./NounsDAOLogicV2.sol:701:    function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {
./NounsDAOLogicV2.sol:714:        uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;
./NounsDAOLogicV2.sol:726:    function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {
./NounsDAOLogicV2.sol:730:        uint32 oldQuorumCoefficient = params.quorumCoefficient;
./NounsDAOLogicV2.sol:749:        uint16 newMinQuorumVotesBPS,
./NounsDAOLogicV2.sol:750:        uint16 newMaxQuorumVotesBPS,
./NounsDAOLogicV2.sol:751:        uint32 newQuorumCoefficient
./NounsDAOLogicV2.sol:923:        uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
./NounsDAOLogicV2.sol:965:        uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
./NounsDAOLogicV1.sol:462:        uint8 support,
./NounsDAOLogicV1.sol:474:        uint8 support,
./NounsDAOLogicV1.sol:475:        uint8 v,
./NounsDAOLogicV1.sol:499:        uint8 support
./NounsDAOLogicV1.sol:500:    ) internal returns (uint96) {
./NounsDAOLogicV1.sol:508:        uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);
./ERC721Checkpointable.sol:41:    uint8 public constant decimals = 0;
./ERC721Checkpointable.sol:53:    mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
./ERC721Checkpointable.sol:56:    mapping(address => uint32) public numCheckpoints;
./ERC721Checkpointable.sol:79:    function votesToDelegate(address delegator) public view returns (uint96) {
./ERC721Checkpointable.sol:130:        uint8 v,
./ERC721Checkpointable.sol:151:    function getCurrentVotes(address account) external view returns (uint96) {
./ERC721Checkpointable.sol:152:        uint32 nCheckpoints = numCheckpoints[account];
./ERC721Checkpointable.sol:166:        uint32 nCheckpoints = numCheckpoints[account];
./ERC721Checkpointable.sol:181:        uint32 lower = 0;
./ERC721Checkpointable.sol:182:        uint32 upper = nCheckpoints - 1;
./ERC721Checkpointable.sol:184:            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
./ERC721Checkpointable.sol:205:        uint96 amount = votesToDelegate(delegator);
./ERC721Checkpointable.sol:213:        uint96 amount
./ERC721Checkpointable.sol:217:                uint32 srcRepNum = numCheckpoints[srcRep];
./ERC721Checkpointable.sol:218:                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
./ERC721Checkpointable.sol:219:                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
./ERC721Checkpointable.sol:224:                uint32 dstRepNum = numCheckpoints[dstRep];
./ERC721Checkpointable.sol:225:                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
./ERC721Checkpointable.sol:226:                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');
./ERC721Checkpointable.sol:234:        uint32 nCheckpoints,
./ERC721Checkpointable.sol:235:        uint96 oldVotes,
./ERC721Checkpointable.sol:236:        uint96 newVotes
./ERC721Checkpointable.sol:238:        uint32 blockNumber = safe32(
./ERC721Checkpointable.sol:264:        uint96 a,
./ERC721Checkpointable.sol:265:        uint96 b,
./ERC721Checkpointable.sol:267:    ) internal pure returns (uint96) {
./ERC721Checkpointable.sol:268:        uint96 c = a + b;
./ERC721Checkpointable.sol:274:        uint96 a,
./ERC721Checkpointable.sol:275:        uint96 b,
./ERC721Checkpointable.sol:277:    ) internal pure returns (uint96) {
./NounsDAOInterfaces.sol:111:    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);
./NounsDAOInterfaces.sol:114:    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);
./NounsDAOInterfaces.sol:117:    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);
./NounsDAOInterfaces.sol:439:    function totalSupply() external view returns (uint96);
```

#### Mitigation
I suggest using uint256 instead of anything smaller or downcast where needed.

&ensp;
### Unnecessary Default Value Initialization

#### Issue
When variable is not initialized, it will have its default values.
For example, 0 for uint, false for bool and address(0) for address.
Reference: https://docs.soliditylang.org/en/v0.8.15/control-structures.html#scoping-and-declarations

#### PoC
Total of 11 instances found.
```
./NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:948:        uint256 lower = 0;
./NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./ERC721Checkpointable.sol:41:    uint8 public constant decimals = 0;
./ERC721Checkpointable.sol:181:        uint32 lower = 0;
```

#### Mitigation
I suggest removing default value initialization.
For example,
- uint256 lower;
- for (uint256 i; i < proposal.targets.length; i++) {

&ensp;
### Store Array's Length as a Variable 

#### Issue
By storing an array's length as a variable before the for-loop,
can save 3 gas per iteration.

#### PoC
Total of 8 instances found.
```
./NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
Store array's length as a variable before looping it.
For example, I suggest changing it to
```
uint256 length = proposal.targets.length
for (uint256 i = 0; i < length; i++) {
```

&ensp;
### ++i Costs Less Gas than i++

#### Issue
Prefix increments/decrements (++i or --i) costs cheaper gas than 
postfix increment/decrements (i++ or i--).

#### PoC
Total of 10 instances found.
```
./NounsDAOLogicV2.sol:226:        proposalCount++;
./NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:216:        proposalCount++;
./NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
Change it to postfix increments/decrements.
It saves 6 gas per loop.

&ensp;
### Empty Blocks Should Emit Something or be Removed

#### Issue
There are function with empty blocks. These should do something like emit an event or reverting.
If not it should be removed from the contract.

#### PoC
Total of 1 instance found.
```solidity
./NounsDAOLogicV2.sol:1030:    receive() external payable {}
```

#### Mitigation
Add emit or revert in the function block.

&ensp;
### Keep The Revert Strings of Error Messages within 32 Bytes

#### Issue
Since each storage slot is size of 32 bytes, error messages that is longer than this will need
extra storage slot leading to more gas cost.

#### PoC
Total of 93 instances found.
```
./NounsDAOProxy.sol:79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
./NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
./NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
./NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
./NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
./NounsDAOLogicV2.sol:137:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:141:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:145:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:197:        require(
                                      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
                                      'NounsDAO::propose: proposer votes below proposal threshold'
                                  );
./NounsDAOLogicV2.sol:201:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
./NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
./NounsDAOLogicV2.sol:213:            require(
                                          proposersLatestProposalState != ProposalState.Active,
                                          'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                                      );
./NounsDAOLogicV2.sol:217:            require(
                                          proposersLatestProposalState != ProposalState.Pending,
                                          'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                                      );
./NounsDAOLogicV2.sol:286:        require(
                                      state(proposalId) == ProposalState.Succeeded,
                                      'NounsDAO::queue: proposal can only be queued if it is succeeded'
                                  );
./NounsDAOLogicV2.sol:312:        require(
                                      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                                      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                                  );
./NounsDAOLogicV2.sol:324:        require(
                                      state(proposalId) == ProposalState.Queued,
                                      'NounsDAO::execute: proposal can only be executed if it is queued'
                                  );
./NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
./NounsDAOLogicV2.sol:350:        require(
                                      msg.sender == proposal.proposer ||
                                          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                                      'NounsDAO::cancel: proposer above threshold'
                                  );
./NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
./NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
./NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
./NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
./NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
./NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
./NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
./NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
./NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
./NounsDAOLogicV2.sol:623:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
./NounsDAOLogicV2.sol:639:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
./NounsDAOLogicV2.sol:656:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
./NounsDAOLogicV2.sol:677:        require(
                                      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                                          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
                                      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
                                  );
./NounsDAOLogicV2.sol:682:        require(
                                      newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
                                      'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
                                  );
./NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
./NounsDAOLogicV2.sol:705:        require(
                                      newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
                                      'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
                                  );
./NounsDAOLogicV2.sol:709:        require(
                                      params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
                                      'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
                                  );
./NounsDAOLocV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
./NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
./NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
./NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
./NounsDAOLogicV2.sol:923:       uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
./NounsDAOLogicV2.sol:965:       uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
./NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
./NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
./NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
./NounsDAOLogicV1.sol:126:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:130:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:134:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:138:        require(
                                      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:187:        require(
                                      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
                                      'NounsDAO::propose: proposer votes below proposal threshold'
                                  );
./NounsDAOLogicV1.sol:191:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
./NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
./NounsDAOLogicV1.sol:203:            require(
                                          proposersLatestProposalState != ProposalState.Active,
                                          'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                                      );
./NounsDAOLogicV1.sol:207:            require(
                                          proposersLatestProposalState != ProposalState.Pending,
                                          'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                                      );
./NounsDAOLogicV1.sol:275:        require(
                                      state(proposalId) == ProposalState.Succeeded,
                                      'NounsDAO::queue: proposal can only be queued if it is succeeded'
                                  );
./NounsDAOLogicV1.sol:301:        require(
                                      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                                      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                                  );
./NounsDAOLogicV1.sol:313:        require(
                                      state(proposalId) == ProposalState.Queued,
                                      'NounsDAO::execute: proposal can only be executed if it is queued'
                                  );
./NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
./NounsDAOLogicV1.sol:339:        require(
                                      msg.sender == proposal.proposer ||
                                          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                                      'NounsDAO::cancel: proposer above threshold'
                                  );
./NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
./NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
./NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
./NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
./NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
./NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
./NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
./NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
./NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
./NounsDAOLogicV1.sol:531:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
./NounsDAOLogicV1.sol:547:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
./NounsDAOLogicV1.sol:564:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
./NounsDAOLogicV1.sol:582:        require(
                                      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
./NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
./NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
./NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
./ERC721Checkpointable.sol:80:       return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');
./ERC721Checkpointable.sol:140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
./ERC721Checkpointable.sol:141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
./ERC721Checkpointable.sol:142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
./ERC721Checkpointable.sol:164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
./ERC721Checkpointable.sol:219:               uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
./ERC721Checkpointable.sol:226:               uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');
./ERC721Checkpointable.sol:238:       uint32 blockNumber = safe32(
                                          block.number,
                                          'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'
                                      );
./ERC721Enumerable.sol:62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
./ERC721Enumerable.sol:77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

#### Mitigation
Simply keep the error messages within 32 bytes to avoid extra storage slot cost.

&ensp;
### Use Custom Errors to Save Gas

#### Issue
Custom errors from Solidity 0.8.4 are cheaper than revert strings.
Details are explained here: https://blog.soliditylang.org/2021/04/21/custom-errors/

#### PoC
Total of 95 instances found.
```
./NounsDAOProxy.sol:80:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
./NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
./NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
./NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
./NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
./NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
./NounsDAOLogicV2.sol:137:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:141:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:145:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:197:        require(
                                      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
                                      'NounsDAO::propose: proposer votes below proposal threshold'
                                  );
./NounsDAOLogicV2.sol:201:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
./NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
./NounsDAOLogicV2.sol:213:            require(
                                          proposersLatestProposalState != ProposalState.Active,
                                          'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                                      );
./NounsDAOLogicV2.sol:217:            require(
                                          proposersLatestProposalState != ProposalState.Pending,
                                          'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                                      );
./NounsDAOLogicV2.sol:286:        require(
                                      state(proposalId) == ProposalState.Succeeded,
                                      'NounsDAO::queue: proposal can only be queued if it is succeeded'
                                  );
./NounsDAOLogicV2.sol:312:        require(
                                      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                                      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                                  );
./NounsDAOLogicV2.sol:324:        require(
                                      state(proposalId) == ProposalState.Queued,
                                      'NounsDAO::execute: proposal can only be executed if it is queued'
                                  );
./NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
./NounsDAOLogicV2.sol:350:        require(
                                      msg.sender == proposal.proposer ||
                                          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                                      'NounsDAO::cancel: proposer above threshold'
                                  );
./NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
./NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
./NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
./NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
./NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
./NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
./NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
./NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
./NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
./NounsDAOLogicV2.sol:623:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
./NounsDAOLogicV2.sol:639:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
./NounsDAOLogicV2.sol:656:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
                                  );
./NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
./NounsDAOLogicV2.sol:677:        require(
                                      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                                          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
                                      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
                                  );
./NounsDAOLogicV2.sol:682:        require(
                                      newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
                                      'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
                                  );
./NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
./NounsDAOLogicV2.sol:705:        require(
                                      newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
                                      'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
                                  );
./NounsDAOLogicV2.sol:709:        require(
                                      params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
                                      'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
                                  );
./NounsDAOLocV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
./NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
./NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
./NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
./NounsDAOLogicV2.sol:923:       uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
./NounsDAOLogicV2.sol:965:       uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
./NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
./NounsDAOLogicV1.sol:123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
./NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
./NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
./NounsDAOLogicV1.sol:126:        require(
                                      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
                                      'NounsDAO::initialize: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:130:        require(
                                      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
                                      'NounsDAO::initialize: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:134:        require(
                                      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:138:        require(
                                      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::initialize: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:187:        require(
                                      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
                                      'NounsDAO::propose: proposer votes below proposal threshold'
                                  );
./NounsDAOLogicV1.sol:191:        require(
                                      targets.length == values.length &&
                                          targets.length == signatures.length &&
                                          targets.length == calldatas.length,
                                      'NounsDAO::propose: proposal function information arity mismatch'
                                  );
./NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
./NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
./NounsDAOLogicV1.sol:203:            require(
                                          proposersLatestProposalState != ProposalState.Active,
                                          'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
                                      );
./NounsDAOLogicV1.sol:207:            require(
                                          proposersLatestProposalState != ProposalState.Pending,
                                          'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
                                      );
./NounsDAOLogicV1.sol:275:        require(
                                      state(proposalId) == ProposalState.Succeeded,
                                      'NounsDAO::queue: proposal can only be queued if it is succeeded'
                                  );
./NounsDAOLogicV1.sol:301:        require(
                                      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                                      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
                                  );
./NounsDAOLogicV1.sol:313:        require(
                                      state(proposalId) == ProposalState.Queued,
                                      'NounsDAO::execute: proposal can only be executed if it is queued'
                                  );
./NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
./NounsDAOLogicV1.sol:339:        require(
                                      msg.sender == proposal.proposer ||
                                          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
                                      'NounsDAO::cancel: proposer above threshold'
                                  );
./NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
./NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
./NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
./NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
./NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
./NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
./NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
./NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
./NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
./NounsDAOLogicV1.sol:531:        require(
                                      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
                                      'NounsDAO::_setVotingDelay: invalid voting delay'
                                  );
./NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
./NounsDAOLogicV1.sol:547:        require(
                                      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
                                      'NounsDAO::_setVotingPeriod: invalid voting period'
                                  );
./NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
./NounsDAOLogicV1.sol:564:        require(
                                      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                                          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
./NounsDAOLogicV1.sol:582:        require(
                                      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
                                      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
                                  );
./NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
./NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
./NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
./NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
./ERC721Checkpointable.sol:80:       return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');
./ERC721Checkpointable.sol:140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
./ERC721Checkpointable.sol:141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
./ERC721Checkpointable.sol:142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
./ERC721Checkpointable.sol:164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
./ERC721Checkpointable.sol:219:               uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
./ERC721Checkpointable.sol:226:               uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');
./ERC721Checkpointable.sol:238:       uint32 blockNumber = safe32(
                                          block.number,
                                          'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'
                                      );
./ERC721Enumerable.sol:62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
./ERC721Enumerable.sol:77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

#### Mitigation
I suggest implementing custom errors to save gas.

&ensp;