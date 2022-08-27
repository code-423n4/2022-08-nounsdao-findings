## FINDINGS

### Using Constants on state variables that are never changed after (saves 4 SLOADs)

The compiler does not reserve a storage slot for these variables, and every occurrence is replaced by the respective value.
Compared to regular state variables, the gas costs of constant  variables are much lower. For a constant variable, the expression assigned to it is copied to all the places where it is accessed and also re-evaluated each time. This allows for local optimizations

File:NounsDAOInterfaces.sol  [Line 527](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L257)

```solidity
    uint256 public quorumVotesBPS;
```

The above variable is never initialized and as such takes the default value of uints, 0.  This value is then read 4 times in the following
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L929

```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
929:                     minQuorumVotesBPS: safe16(quorumVotesBPS),
930:                     maxQuorumVotesBPS: safe16(quorumVotesBPS),

942:                     minQuorumVotesBPS: safe16(quorumVotesBPS),
943:                     maxQuorumVotesBPS: safe16(quorumVotesBPS),
```

This means we have **4 SLOADS** . We can get rid of this SLOADS by declaring our **quorumVotesBPS** variable as a constant and initializing it.

**NB**: The variable in question is declared in the contract  [NounsDAOStorageV1Adjusted](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L243) which is then inherited by [NounsDAOStorageV2](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L347)
The **NounsDAOStorageV2** is then used in **NounsDAOLogicV2.sol**  [inherited here](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L57)

This is not to be confused with a similar variable declared in [NounsDAOStorageV1](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L143) as this is not the one used in **NounsDAOLogicV2.sol**

### Cache storage values in memory to minimize SLOADs
The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs.

NB: *Some functions have been truncated where neccessary to just show affected parts of the code*

FIle: NounsDAOLogicV2.sol [Line 184-279](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L184-L279)
### NounsDAOLogicV2.sol.propose(): proposalCount should be cached(saves 2 SLOADs)
```solidity
226:        proposalCount++; //@audit: proposalCount - SLOAD 1
227:        Proposal storage newProposal = _proposals[proposalCount];//@audit: proposalCount - SLOAD 2
228:        newProposal.id = proposalCount;//@audit: proposalCount - SLOAD 3
```

File: NounsDAOLogicV2.sol [Line 817-833](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L817-L833)
### NounsDAOLogicV2.sol.\_acceptAdmin(): pendingAdmin should be cached(Saves 2 sloads )
```solidity
    function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');//@audit: pendingAdmin - SLOAD 1
        // Save current values for inclusion in log
        address oldAdmin = admin;
        address oldPendingAdmin = pendingAdmin; //@audit: pendingAdmin - SLOAD 2

        // Store admin with value pendingAdmin
        admin = pendingAdmin; //@audit: pendingAdmin - SLOAD 3

        // Clear the pending value
        pendingAdmin = address(0);

        emit NewAdmin(oldAdmin, admin);
        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
    }
```

File: NounsDAOLogicV2.sol [Line 839-845](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L839-L845)
### NounsDAOLogicV2.sol.\_setVetoer: vetoer should be cached(Saves 1 SLOAD)
```solidity
839:    function _setVetoer(address newVetoer) public {
840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only'); //@audit: vetoer - SLOAD 1

842:        emit NewVetoer(vetoer, newVetoer); //@audit: vetoer - SLOAD 2

844:        vetoer = newVetoer;
845:    }
```

File: NounsDAOLogicV2.sol [Line 374-393](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L374-L393)
### NounsDAOLogicV2.sol.veto(): vetoer should be cached(saves 1 SLOADs)
```solidity
374:    function veto(uint256 proposalId) external {
375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned'); //@audit: vetoer - SLOAD 1
376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer'); //@audit: vetoer - SLOAD 2
```

File: NounsDAOLogicV1.sol [Line 216-219](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L216-L219)
### NounsDAOLogicV1.sol.propose(): proposalCount should be cached(saves 2 SLOADs)
```solidity
216:        proposalCount++;  //@audit: proposalCount - SLOAD 1
217:        Proposal storage newProposal = proposals[proposalCount]; //@audit: proposalCount - SLOAD 2

219:        newProposal.id = proposalCount; //@audit: proposalCount - SLOAD 3
```

File: NounsDAOLogicV1.sol [Line 363-382](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L363-L382)
### NounsDAOLogicV1.sol.veto(): vetoer should be cached(saves 1 SLOADs)

```solidity
363:    function veto(uint256 proposalId) external {
364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');//@audit: vetoer - SLOAD 1
365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');//@audit: vetoer - SLOAD 2
```

File: NounsDAOLogicV1.sol [Line 615-631](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L615-L631)
### NounsDAOLogicV1.sol.\_acceptAdmin(): pendingAdmin should be cached(saves 2 SLOADs)

```solidity
    function _acceptAdmin() external {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');//@audit: -pendingAdmin SLOAD 1
        // Save current values for inclusion in log
        address oldAdmin = admin;
        address oldPendingAdmin = pendingAdmin;//@audit: -pendingAdmin SLOAD 2
				...
    }
```

File: NounsDAOLogicV1.sol [Line 637-643](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L637-L643)
### NounsDAOLogicV1.sol.\_setVetoer(): vetoer should be cached(saves 1 SLOADs)
```solidity
637:    function _setVetoer(address newVetoer) public {
638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only'); //@audit: vetoer - SLOAD 1

640:        emit NewVetoer(vetoer, newVetoer);//@audit: vetoer - SLOAD 2

642:        vetoer = newVetoer;
643:    }
```

### Emitting storage values instead of the memory one.- Minimize SLOADs (Save 1 sload for each of the following)
Here, the values emitted shouldn’t be read from storage. The existing memory values should be used instead:
File: NounsDAOProxy.sol [Line 78-86](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L78-L86)
```solidity
File: /contracts/governance/NounsDAOProxy.sol
78:    function _setImplementation(address implementation_) public {
					   ...
83:        implementation = implementation_;
84:
85:        emit NewImplementation(oldImplementation, implementation);
86:    }
```
Should emit `implementation_` instead of `implementation`

File: NounsDAOLogicV2.sol [Line 621-631](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L621-L631)
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
621:    function _setVotingDelay(uint256 newVotingDelay) external {
            ...
628:        votingDelay = newVotingDelay;
629:
630:        emit VotingDelaySet(oldVotingDelay, votingDelay);
631:    }
```
votingDelay is a state variable, should emit newVotingDelay

File: NounsDAOLogicV2.sol [Line 646](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L646)
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
637:    function _setVotingPeriod(uint256 newVotingPeriod) external {
            ...
644:        votingPeriod = newVotingPeriod;
645:
646:        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
647:		}
```
Should emit `newVotingPeriod` instead of `votingPeriod`

File: NounsDAOLogicV2.sol [Line 654-665](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L654-L665)
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
654:    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {
						 ...
661:        uint256 oldProposalThresholdBPS = proposalThresholdBPS;
662:        proposalThresholdBPS = newProposalThresholdBPS;
663:
664:        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
665:		}
```
Should emit `newProposalThresholdBPS` instead of `proposalThresholdBPS`

File: NounsDAOLogicV1.sol [Line 529-539](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L529-L539)
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
529:    function _setVotingDelay(uint256 newVotingDelay) external {
            ...
535:        uint256 oldVotingDelay = votingDelay;
536:        votingDelay = newVotingDelay;
537:
538:        emit VotingDelaySet(oldVotingDelay, votingDelay);
539:    }
```
Should emit `newVotingDelay` instead of `votingDelay`

File: NounsDAOLogicV1.sol [Line 545-555](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L545-L555)
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
545:    function _setVotingPeriod(uint256 newVotingPeriod) external {
            ...
551:        uint256 oldVotingPeriod = votingPeriod;
552:        votingPeriod = newVotingPeriod;
553:
554:        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
555:    }
```
Should emit `newVotingPeriod` instead of `votingPeriod`

File: NounsDAOLogicV1.sol [Line 562-573](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L562-L573)
```solidity
562:    function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {
	          ...
569:        uint256 oldProposalThresholdBPS = proposalThresholdBPS;
570:        proposalThresholdBPS = newProposalThresholdBPS;
571:
572:        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
573:    }
```
Should emit `newProposalThresholdBPS` instead of `proposalThresholdBPS`

File: NounsDAOLogicV1.sol [Line 580-590](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L580-L590)
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
580:    function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external {
        ...
586:        uint256 oldQuorumVotesBPS = quorumVotesBPS;
587:        quorumVotesBPS = newQuorumVotesBPS;
588:
589:        emit QuorumVotesBPSSet(oldQuorumVotesBPS, quorumVotesBPS);
590:    }
```
Should emit `newQuorumVotesBPS` instead of `quorumVotesBPS`

### Internal/Private functions only called once can be inlined to save gas
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

**Affected code:**

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L120
```solidity
File: /contracts/base/ERC721Enumerable.sol
120:    function _addTokenToOwnerEnumeration(address to, uint256 tokenId) private {

143:    function _removeTokenFromOwnerEnumeration(address from, uint256 tokenId) private {

168:    function _removeTokenFromAllTokensEnumeration(uint256 tokenId) private {     
}
```


https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L305-L311
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
305:    function queueOrRevertInternal(
306:        address target,
307:        uint256 value,
308:        string memory signature,
309:        bytes memory data,
310:        uint256 eta
311:    ) internal {
```
The above function is only called once on [Line 293](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L293)

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L866
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
    function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
```
The above function is only called once on [Line 600](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L600)

## Use calldata instead of memory for function parameters

### Flagging this here as we can Change function to external and most importantly data to calldata 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174-L268
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
174:     function propose(
175:        address[] memory targets,
176:        uint256[] memory values,
177:        string[] memory signatures,
178:        bytes[] memory calldatas,
179:        string memory description
180:    ) public returns (uint256) {
```
All arguments in the above function should use **Calldata** instead of **memory** as they are readonly in this function.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L184-L279
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
184:     function propose(
185:        address[] memory targets,
186:        uint256[] memory values,
187:        string[] memory signatures,
188:        bytes[] memory calldatas,
189:        string memory description
190:    ) public returns (uint256) {
```
All arguments in the above function should use **Calldata** instead of **memory**  as they are readonly in this function.

### Tighly Pack structs to save storage slots

When defining a struct, pack the values so that the data types are in ascending order. This will make sure that data types that can be put into the same slot are packed together instead of each variable having a separate storage slot.
Each slot saved can avoid an extra Gsset (20000 gas) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings.

In the following  we can save one storage slot by moving the bools next to **address proposer**.
There are 3 instances of this.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L174-L211

```solidity
File: /contracts/governance/NounsDAOInterfaces.sol
    struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
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
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
    }
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L274-L315
```solidity
File: /contracts/governance/NounsDAOInterfaces.sol
    struct Proposal {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
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
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice Receipts of ballots for the entire set of voters
        mapping(address => Receipt) receipts;
        /// @notice The total supply at the time of proposal creation
        uint256 totalSupply;
        /// @notice The block at which this proposal was created
        uint256 creationBlock;
    }
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L368-L399
```solidity
File: /contracts/governance/NounsDAOInterfaces.sol
    struct ProposalCondensed {
        /// @notice Unique id for looking up a proposal
        uint256 id;
        /// @notice Creator of the proposal
        address proposer;
        /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 proposalThreshold;
        /// @notice The minimum number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 quorumVotes;
        /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
        uint256 eta;
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
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
        /// @notice The total supply at the time of proposal creation
        uint256 totalSupply;
        /// @notice The block at which this proposal was created
        uint256 creationBlock;
    }
```

### Using private rather than public for constants, saves gas
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L67-L94

```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
67:  string public constant name = 'Nouns DAO';

      /// @notice The minimum setable proposal threshold
70:  uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

       /// @notice The maximum setable proposal threshold
73:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

       /// @notice The minimum setable voting period
76:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

       /// @notice The max setable voting period
79:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

       /// @notice The min setable voting delay
82:    uint256 public constant MIN_VOTING_DELAY = 1;

       /// @notice The max setable voting delay
85:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

       /// @notice The minimum setable quorum votes basis points
88:    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%

       /// @notice The maximum setable quorum votes basis points
91:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

       /// @notice The maximum number of actions that can be included in a proposal
94:    uint256 public constant proposalMaxOperations = 10; // 10 actions
```

**Other instance** 
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L59-L98

### Use Shift Right/Left instead of Division/Multiplication
While the DIV / MUL opcode uses 5 gas, the SHR / SHL opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version 0.8+

[relevant source](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md/#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)

File: ERC721Checkpointable.sol [Line 184](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L184)
```solidity
            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```

The above should be modified to 
```solidity
            uint32 center = upper - (upper - lower) >> 1; // ceil, avoiding overflow
```

**Other instances:**
File: NounsDAOLogicV2.sol [Line 951](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L951)
```solidity
            uint256 center = upper - (upper - lower) / 2;
```

### Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[Use a larger size then downcast where needed](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) 

File: NounsDAOLogicV2.sol [Line 518-524](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L518-L524)
`  uint8 support`
```solidity
    function castRefundableVoteWithReason(
        uint256 proposalId,
        uint8 support,
        string calldata reason
    ) external {
        castRefundableVoteInternal(proposalId, support, reason);
    }
```

File: NounsDAOLogicV2.sol [Line 533-537](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L533-L537)
`  uint8 support`
```solidity
    function castRefundableVoteInternal(
        uint256 proposalId,
        uint8 support,
        string memory reason
    ) internal {
```

File: NounsDAOLogicV2.sol [Line 552-556](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L552-L556)
`  uint8 support`
```solidity
    function castVoteWithReason(
        uint256 proposalId,
        uint8 support,
        string calldata reason
    ) external {
```

File: NounsDAOLogicV2.sol [Line 673](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L673)
```solidity
    function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {
```

File: NounsDAOLogicV2.sol [Line 726](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L726)
```solidity
    function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {
```

File: NounsDAOLogicV2.sol [Line 748-752](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L748-L752)
```solidity
    function _setDynamicQuorumParams(
        uint16 newMinQuorumVotesBPS,
        uint16 newMaxQuorumVotesBPS,
        uint32 newQuorumCoefficient
    ) public {
```

FIle: NounsDAOLogicV1.sol [Line 450](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L450)
**uint8 support**
```solidity
    function castVote(uint256 proposalId, uint8 support) external {
```

File: NounsDAOLogicV1.sol [Line 460-464](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L460-L464)
**uint8 support**
```solidity
    function castVoteWithReason(
        uint256 proposalId,
        uint8 support,
        string calldata reason
    ) external {
```
File: NounsDAOLogicV1.sol [Line 496-500](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L496-L500)
**uint8 support**

```solidity
    function castVoteInternal(
        address voter,
        uint256 proposalId,
        uint8 support
    ) internal returns (uint96) {
```

### Expressions for constant values such as a call to keccak256(), should use immutable rather than constant(proof given)

Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

If the variable was immutable instead: the calculation would only be done once at deploy time (in the constructor), and then the result would be saved and read directly at runtime rather than being recalculated.

**consequences:**
-    Each usage of a "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)

-    Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library )
 
See: ethereum/solidity#9232

File: ERC721Checkpointable.sol#L59-L60 [Line 59-60](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L59-L60)
```solidity
    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
```

File: ERC721Checkpointable.sol [Line 63-64](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L63-L64)
```solidity
    bytes32 public constant DELEGATION_TYPEHASH =
        keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
```

File: NounsDAOLogicV2.sol [Line 101](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L101)
```solidity
    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
```

File: NounsDAOLogicV2.sol [Line 105](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L105)
```solidity
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

File: NounsDAOLogicV1.sol [Line 97](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L97)
```solidity
    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
```

File: NounsDAOLogicV1.sol [Line 101](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L101)
```sollidity
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

**Proof: can be tested on remix also, showing results for foundry** 
```solidity
pragma solidity ^0.8.6;
contract Constant{
    bytes32 constant noteDenom = keccak256(bytes("NOTE"));
    function doConstant() external view returns(bytes32){
        return noteDenom;
    }
}
contract Immutable{
    bytes32 immutable noteDenom = keccak256(bytes("NOTE"));
    function doImmutable() external view returns(bytes32){
        return noteDenom;
    }
}
```

**Results from :** `forge test --gas-report --optimize`

```solidity
Running 1 test for test/Constant.t.sol:ConstantTest
[PASS] testdoThing() (gas: 5416)
Test result: ok. 1 passed; 0 failed; finished in 420.08µs

Running 1 test for test/Immutable.t.sol:ImmutableTest
[PASS] testdoThing() (gas: 5356)
Test result: ok. 1 passed; 0 failed; finished in 389.91µs
```

## Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L53
```solidity
File: /contracts/base/ERC721Checkpointable.sol
53:     mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

56:     mapping(address => uint32) public numCheckpoints;

```

### Duplicated require()/revert() checks should be refactored to a modifier or function
This saves deployement gas.
File: NounsDAOLogicV2.sol [Line 753](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L753-L755)

```solidity
    if (msg.sender != admin) {
            revert AdminOnly();
        }
```
The above  has been repeated on [Line 784](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L784-L786)

**Other instances:**

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L134

```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
134:         require(msg.sender == admin, 'NounsDAO::initialize: admin only');

622:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

638:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

655:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

674:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

702:         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

727:         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L376
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
376:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```

### Cache the length of arrays in loops (saves ~6 gas per iteration)
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

The solidity compiler will always read the length of the array during each iteration. That is,

   1.if it is a storage array, this is an extra sload operation (100 additional extra gas (EIP-2929 2) for each iteration except for the first),
   2.if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first),
   3.if it is a calldata array, this is an extra calldataload operation (3 additional gas for each iteration except for the first)

This extra costs can be avoided by caching the array length (in stack):
 When reading the length of an array,  **sload** or **mload** or **calldataload** operation is only called once and subsequently replaced by a cheap **dupN** instruction. Even though mload , calldataload and dupN have the same gas cost, mload and calldataload needs an additional dupN to put the offset in the stack, i.e., an extra 3 gas. which brings this to 6 gas
 
Here, I suggest storing the array’s length in a variable before the for-loop, and use it instead:

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
        uint256 eta = block.timestamp + timelock.delay();
        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
 **The above should be modified to**
```solidity
        uint256 eta = block.timestamp + timelock.delay();
		    uint256 length = proposal.targets.length;
        for (uint256 i = 0; i < length; i++) {
		
```


**Other instances to modify**
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
281:         for (uint256 i = 0; i < proposal.targets.length; i++) {

319:         for (uint256 i = 0; i < proposal.targets.length; i++) {

346:         for (uint256 i = 0; i < proposal.targets.length; i++) {

371:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```


### ++i costs less gas compared to i++ or i += 1  (~5 gas per iteration)

++i costs less gas compared to i++ or i += 1 for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

i++ increments i and returns the initial value of i. Which means:

```solidity
uint i = 1;  
i++; // == 1 but i == 2  
```

But ++i returns the actual incremented value:

```solidity
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```

In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

Instances include:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L226
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
226:        proposalCount++;

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```


https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
216:			proposalCount++;

281:         for (uint256 i = 0; i < proposal.targets.length; i++) {

319:         for (uint256 i = 0; i < proposal.targets.length; i++) {

346:         for (uint256 i = 0; i < proposal.targets.length; i++) {

371:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```

### Splitting require() statements that use && saves gas - (saves 8 gas per &&)

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 8 GAS per &&
The gas difference would only be realized if the revert condition is realized(met).

File: NounsDAOLogicV2.sol [Line 137-140](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L137-L140)
```solidity
        require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
```

File: NounsDAOLogicV2.sol [Line 141-144](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L141-L144)
```solidity
        require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
```

File: NounsDAOLogicV2.sol [Line 145-148](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L145-L148)
```solidity
        require(proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,'NounsDAO::initialize: invalid proposal threshold bps');
```

File: NounsDAOLogicV2.sol [Line 201](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L201)
```solidity
        require(targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,'NounsDAO::propose: proposal function information arity mismatch');
```

File: NounsDAOLogicV2.sol [Line 623](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L623)
```solidity
        require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
```

File: NounsDAOLogicV2.sol [Line 656](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L656)
```solidity
        require(newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS && newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,'NounsDAO::_setProposalThreshold: invalid proposal threshold bps');
```

File: NounsDAOLogicV2.sol [Line 677](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L677)
```solidity
        require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
```

File: NounsDAOLogicV2.sol [Line 819](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L819)
```solidity
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

File: NounsDAOLogicV1.sol [Line 126](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L126)
```solidity
        require( votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,'NounsDAO::initialize: invalid voting period');
```

File: NounsDAOLogicV1.sol [Line 130](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L130)
```solidity
        require(votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,'NounsDAO::initialize: invalid voting delay' );
```

File: NounsDAOLogicV1.sol [Line 134](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L134)
```solidity
        require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,'NounsDAO::initialize: invalid proposal threshold' );
```

File: NounsDAOLogicV1.sol [Line 138](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L138)
```solidity
        require( quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold');
```

File: NounsDAOLogicV1.sol [Line 531](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L531)
```solidity
        require(newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay' );
```

File: NounsDAOLogicV1.sol [Line 547](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L547)
```solidity
        require( newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period' );
```

File: NounsDAOLogicV1.sol [Line 564](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L564)
```solidity
        require( newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,  'NounsDAO::_setProposalThreshold: invalid proposal threshold'  );
```
File: NounsDAOLogicV1.sol [Line 582](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L582)
```solidity
        require(newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold');
```
File: NounsDAOLogicV1.sol [Line 617](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L617)
```solidity
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

**Proof**
**The following tests were carried out in remix with both optimization turned on and off**

```solidity
function multiple (uint a) public pure returns (uint){
	require ( a > 1 && a < 5, "Initialized");
	return  a + 2;
}
```
**Execution cost**
21617 with optimization and using &&
21976 without optimization and using &&


After splitting the require statement
```solidity
function multiple(uint a) public pure returns (uint){
	require (a > 1 ,"Initialized");
	require (a < 5 , "Initialized");
	return a + 2;
}
```
**Execution cost**
21609 with optimization and split require
21968 without optimization and using split require


### Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value.
I suggest using if(directValue) instead of if(directValue == true) and if(!directValue) instead of if(directValue == false) here:

File: NounsDAOLogicV1.sol [Line 505](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505)
```solidity
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
		
```

File: NounsDAOLogicV2.sol [Line 597](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597)
```solidity
        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
		
```

### Using unchecked blocks to save gas
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn’t possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an unchecked block
[see resource](https://github.com/ethereum/solidity/issues/10695)

File: ERC721Checkpointable.sol [Line 153](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L153)
```solidity
        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
```

The operation `nCheckpoints - 1` cannot underflow due to the condition `nCheckpoints > 0` 

File: ERC721Checkpointable.sol [Line 172 & 173](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L172-173)
```solidity
        if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {
            return checkpoints[account][nCheckpoints - 1].votes;
        }
```
The operation `nCheckpoints - 1` cannot underflow due to the condition on [Line 167](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L167) that ensures that `nCheckpoints` is not equal to zero

File: ERC721Checkpointable.sol [Line 182](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L182)
```solidity
        uint32 upper = nCheckpoints - 1;
```

File: ERC721Checkpointable.sol [Line 184](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L184)
```solidity
            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```
The operation `upper - lower ` cannot underflow due to the check on [line 183](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L183) that ensures that `upper` is greater than `lower`

File: ERC721Checkpointable.sol [Line 218](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L218)
```solidity
                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
```

The operation `srcRepNum - 1` cannot underflow due to the condition `srcRepNum > 0` that ensures that `srcRepNum` is greater than 0.

File: ERC721Checkpointable.sol [Line 225](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L225)
```solidity
                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
```
The operation `dstRepNum  - 1` cannot underflow due to the condition `dstRepNum > 0` that ensures that `dstRepNum` is greater than 0.

File: ERC721Checkpointable.sol [Line 243-244](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L243-L244)
```solidity
        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
            checkpoints[delegatee][nCheckpoints - 1].votes = newVotes;
```

The operation `nCheckpoints - 1` cannot underflow due to the condition `nCheckpoints > 0`

File: NounsDAOLogicV2.sol [Line 951](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L951)
```solidity
            uint256 center = upper - (upper - lower) / 2;
			
```

The operation `upper -lower` cannot underflow due to the check on [Line 950](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L950) which ensures that `upper` is greater than `lower`

File: NounsDAOLogicV2.sol [Line 967-968](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L967-L968)
```solidity
        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
            quorumParamsCheckpoints[pos - 1].params = params;
			
```
The operation `pos - 1` cannot underflow due to the check `pos > 0`


### Using unchecked blocks to save gas - Increments in for loop can be unchecked  ( save 30-40 gas per loop iteration)
The majority of Solidity for loops increment a uint256 variable that starts at 0. These increment operations never need to be checked for over/underflow because the variable will never reach the max number of uint256 (will run out of gas long before that happens). The default over/underflow check wastes gas in every iteration of virtually every for loop . eg.

e.g Let's work with a sample loop below.

```solidity
for(uint256 i; i < 10; i++){
//doSomething
}

```
can be written as shown below.
```solidity
for(uint256 i; i < 10;) {
  // loop logic
  unchecked { i++; }
}
```

We can also write  it as an inlined function like below.

```solidity
function inc(i) internal pure returns (uint256) {
  unchecked { return i + 1; }
}
for(uint256 i; i < 10; i = inc(i)) {
  // doSomething
}
```

**Affected code**

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
281:         for (uint256 i = 0; i < proposal.targets.length; i++) {

319:         for (uint256 i = 0; i < proposal.targets.length; i++) {

346:         for (uint256 i = 0; i < proposal.targets.length; i++) {

371:         for (uint256 i = 0; i < proposal.targets.length; i++) {

```


[see resource](https://github.com/ethereum/solidity/issues/10695)
### use shorter revert strings(less than 32 bytes) 
Every reason string takes at least 32 bytes so make sure your string fits in 32 bytes or it will become more expensive.Each extra chunk of byetes past the original 32 incurs an MSTORE which costs 3 gas

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L62
```solidity
File: /contracts/base/ERC721Enumerable.sol
62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
77:					require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L140-L142
```solidity
File: /contracts/base/ERC721Checkpointable.sol
140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

```

File: NounsDAOLogicV2.sol [Line 133](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L133)
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L133
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
133:    require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
135:    require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136:    require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
137:    require(votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,'NounsDAO::initialize: invalid voting period');
141:    require( votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,'NounsDAO::initialize: invalid voting delay');
145:    require(proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,'NounsDAO::initialize: invalid proposal threshold bps');

197:    require(nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'NounsDAO::propose: proposer votes below proposal threshold');
201:    require(targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,'NounsDAO::propose: proposal function information arity mismatch');
207:    require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208:    require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
213:    require( proposersLatestProposalState != ProposalState.Active,'NounsDAO::propose: one live proposal per proposer, found an already active proposal');
217:    require(proposersLatestProposalState != ProposalState.Pending,'NounsDAO::propose: one live proposal per proposer, found an already pending proposal');

286:    require(state(proposalId) == ProposalState.Succeeded,'NounsDAO::queue: proposal can only be queued if it is succeeded');
312:    require( !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
 'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta' );
702:   require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only'); 
727:   require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only'); 
819:   require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
840:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L79
```solidity
File: /contracts/governance/NounsDAOProxy.sol
79:			require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:    require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

```

I suggest shortening the revert strings to fit in 32 bytes, or using custom errors.

### Use Custom Errors instead of Revert Strings to save Gas(~50 gas)
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)
Custom errors save ~50 gas each time they’re hit by avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
see [Source](https://blog.soliditylang.org/2021/04/21/custom-errors/)

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L140

```solidity
File: /contracts/base/ERC721Checkpointable.sol
140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L133
```solidity
File:/contracts/governance/NounsDAOLogicV2.sol
133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
197:        require(nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,'NounsDAO::propose: proposer votes below proposal threshold');
201:        require(targets.length == values.length && targets.length == signatures.length && targets.length == calldatas.length,'NounsDAO::propose: proposal function information arity mismatch');
207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
213:        require( proposersLatestProposalState != ProposalState.Active,'NounsDAO::propose: one live proposal per proposer, found an already active proposal');
217:        require(proposersLatestProposalState != ProposalState.Pending,'NounsDAO::propose: one live proposal per proposer, found an already pending proposal');
286:        require(state(proposalId) == ProposalState.Succeeded,'NounsDAO::queue: proposal can only be queued if it is succeeded');
312:        require( !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
 'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta' );
727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L79
```solidity
File: /contracts/governance/NounsDAOProxy.sol
79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:       require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```

