GAS FINDINGS

## [G-1] Title: Using calldata pointer to store array and string params

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L185-L189

Using `calldata` pointer for read only var can save gas


RECOMMENDED MITIGATION STEP
```
    function propose(
        address[] calldata targets,
        uint256[] calldata values,
        string[] calldata signatures,
        bytes[] calldata calldatas,
        string calldata description
    ) public returns (uint256) {
```


## [G-2] Title: Using < 32 bytes string

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L197-L220
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L288
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L314

Error message > 32 bytes consume more gas. Use custom error or shorten the string


## [G-3] Title: Tight variable packing in `Proposal` struct

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L174-L211

Grouping all data types that are supposed to go together into one 32 byte slot, and declare them one after another in your code can is more efficient. We can place all boolean next to `proposer` (address(20 bytes size)). By doing this, we can save 1 slot at the storage

RECOMMENDED MITIGATION STEP
```
    struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 proposalThreshold;
        /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 quorumVotes;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
        /// @notice the ordered list of target addresses for calls to be made
        address[] targets;
        /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
        uint256[] values;
        /// @notice The ordered list of function signatures to be called
        string[] signatures;
        /// @notice The ordered list of calldata to be passed to each call
        bytes[] calldatas;
        /// @notice The block at which voting begins: holders must delegate their votes prior to this block
        uint256 startBlock;
        /// @notice The block at which voting ends: votes must be cast prior to this block
        uint256 endBlock;
        /// @notice Current number of votes in favor of this proposal
        uint256 forVotes;
        /// @notice Current number of votes in opposition to this proposal
        uint256 againstVotes;
        /// @notice Current number of votes for abstaining for this proposal
        uint256 abstainVotes;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }
```


## [G-4] Title: Unnecessary SSTORE in `propose()` function

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L238-L243
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L231

Without setting the value to false or 0, the value is remain the same since `proposalCount` is always increasing and the caller will only propose() the new proposal with all the value were default

RECOMMENDED MITIGATION STEP
Remove all the lines were mentioned


## [G-5] Avoid `proposalCount` SLOAD

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226-L228

We can cache the `proposalCount` to memory to save gas

RECOMMENDED MITIGATION STEP
```
        uint256 _proposalCount = proposalCount++; // @audit: cache it to memory
        Proposal storage newProposal = _proposals[_proposalCount];
        newProposal.id = _proposalCount;
```


## [G-6] Title: Using prefix increment and unchecked on for() loop

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330

We can save gas by:
```
        for (uint256 i = 0; i < proposal.targets.length;) {
            queueOrRevertInternal(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                eta
            );
	    unchecked{++i}
        }
```


## [G-7] Unnecessary vetoer validation check

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375

Checking vetoer != address(0) while also validating msg.sender == vetoer is not effective and consuming gas (2x SLOAD).


## [G-8] Avoid SLOAD to emit event `NewAdmin()`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L94
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L86
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L104

We can use local var to emit event instead of storage var

RECOMMENDED MITIGATION STEP
```
	emit NewDelay(delay_);

	emit NewAdmin(msg.sender); // L94
	
	emit NewPendingAdmin(pendingAdmin_); // L104

        
```