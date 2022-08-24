**Overview**

Risk Rating | Number of issues
--- | ---
Gas Issues | 14

**Table of Contents:**

- [1. Caching storage values in memory](#1-caching-storage-values-in-memory)
  - [1.1. contracts/governance/NounsDAOLogicV1.sol](#11-contractsgovernancenounsdaologicv1sol)
  - [1.2. contracts/governance/NounsDAOLogicV2.sol](#12-contractsgovernancenounsdaologicv2sol)
  - [1.3. contracts/governance/NounsDAOProxy.sol](#13-contractsgovernancenounsdaoproxysol)
- [2. Unchecking arithmetics operations that can't underflow/overflow](#2-unchecking-arithmetics-operations-that-cant-underflowoverflow)
- [3. Tightly pack `struct Proposal`](#3-tightly-pack-struct-proposal)
- [4. Tightly pack `struct ProposalCondensed`](#4-tightly-pack-struct-proposalcondensed)
- [5. Boolean comparisons](#5-boolean-comparisons)
- [6. Splitting `require()` statements that use `&&` saves gas](#6-splitting-require-statements-that-use--saves-gas)
- [7. Using private rather than public for constants saves gas](#7-using-private-rather-than-public-for-constants-saves-gas)
- [8. Use shift right/left instead of division/multiplication if possible](#8-use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
- [9. `<array>.length` should not be looked up in every loop of a `for-loop`](#9-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)
- [10. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)](#10-i-costs-less-gas-compared-to-i-or-i--1-same-for---i-vs-i---or-i---1)
- [11. Increments/decrements can be unchecked in for-loops](#11-incrementsdecrements-can-be-unchecked-in-for-loops)
- [12. It costs more gas to initialize variables with their default value than letting the default value be applied](#12-it-costs-more-gas-to-initialize-variables-with-their-default-value-than-letting-the-default-value-be-applied)
- [13. Upgrade pragma](#13-upgrade-pragma)
- [14. Use Custom Errors instead of Revert Strings to save Gas](#14-use-custom-errors-instead-of-revert-strings-to-save-gas)

## 1. Caching storage values in memory

The code can be optimized by minimizing the number of SLOADs.

SLOADs are expensive (100 gas after the 1st one) compared to MLOADs/MSTOREs (3 gas each). Storage values read multiple times should instead be cached in memory the first time (costing 1 SLOAD) and then read from this cache to avoid multiple SLOADs. See `@audit` tags for details:

### 1.1. contracts/governance/NounsDAOLogicV1.sol

- `nouns` (1 SLOAD)

```solidity
  183:         temp.totalSupply = nouns.totalSupply(); //@audit SLOAD (nouns)
  188:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold, //@audit SLOAD (nouns)
```

- `uint256 _proposalCount = ++proposalCount;` (5 SLOAD), existing `temp.startBlock` (2 SLOAD), existing `temp.endBlock` (2 SLOAD), existing `temp.proposalThreshold;` (1 SLOAD), `uint256 quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply)` (1 SLOAD)

```solidity
  216:         proposalCount++; //@audit should cache "++proposalCount" and use the cache instead of 5 SLOADs below
  219:         newProposal.id = proposalCount; //@audit should use proposalCount's cache
  222:         newProposal.quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply);//@audit SLOAD (should cache "bps2Uint(quorumVotesBPS, temp.totalSupply)")
  237:         latestProposalIds[newProposal.proposer] = newProposal.id; //@audit should use proposalCount's cache
  241:             newProposal.id, //@audit should use proposalCount's cache
  247:             newProposal.startBlock, //@audit SLOAD, should use temp.startBlock
  248:             newProposal.endBlock, //@audit SLOAD, should use temp.endBlock
  254:             newProposal.id, //@audit should use proposalCount's cache
  260:             newProposal.startBlock, //@audit SLOAD, should use ttBlock
  261:             newProposal.endBlock, //@audit SLOAD, should use emp.startemp.endBlock
  262:             newProposal.proposalThreshold, //@audit SLOAD, should use temp.proposalThreshold;
  263:             newProposal.quorumVotes,//@audit SLOAD (should use cached "bps2Uint(quorumVotesBPS, temp.totalSupply)")
  267:         return newProposal.id; //@audit should use proposalCount's cache
```

- `timelock` (1 SLOAD)

```solidity
  302:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))), //@audit SLOAD (timelock)
  305:         timelock.queueTransaction(target, value, signature, data, eta); //@audit SLOAD (timelock)
```

- `vetoer` (1 SLOAD)

```solidity
  364:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned'); //@audit SLOAD (vetoer)
  365:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer'); //@audit SLOAD (vetoer)
```

- `emit` should be called with existing variables before setting a variable in storage (4 SLOAD)

```solidity
  538:         emit VotingDelaySet(oldVotingDelay, votingDelay); //@audit SLOAD (should emit before assigning "votingDelay = newVotingDelay;")
  554:         emit VotingPeriodSet(oldVotingPeriod, votingPeriod); //@audit SLOAD (should emit before assigning "votingPeriod = newVotingPeriod;")
  572:         emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS); //@audit SLOAD (should emit before assigning "proposalThresholdBPS = newProposalThresholdBPS;")
  589:         emit QuorumVotesBPSSet(oldQuorumVotesBPS, quorumVotesBPS); //@audit SLOAD (should emit before assigning "quorumVotesBPS = newQuorumVotesBPS;")
```

- Should use existing memory variables (3 SLOAD)

```solidity
  624:         admin = pendingAdmin;  //@audit SLOAD (pendingAdmin, should use "oldPendingAdmin")
  629:         emit NewAdmin(oldAdmin, admin);//@audit SLOAD (admin, should use "oldPendingAdmin")
  630:         emit NewPendingAdmin(oldPendingAdmin, pendingAdmin); //@audit SLOAD (pendingAdmin, should use "address(0)")
```

- `vetoer` (1 SLOAD)

```solidity
  638:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only'); //@audit SLOAD (vetoer)
  640:         emit NewVetoer(vetoer, newVetoer); //@audit SLOAD (vetoer)
```

### 1.2. contracts/governance/NounsDAOLogicV2.sol

- `nouns` (1 SLOAD)

```solidity
  193:         temp.totalSupply = nouns.totalSupply(); //@audit SLOAD (nouns)
  198:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold, //@audit SLOAD (nouns)
```

- `uint256 _proposalCount = ++proposalCount;` (5 SLOAD), existing `temp.startBlock` (2 SLOAD), existing `temp.endBlock` (2 SLOAD), existing `temp.proposalThreshold;` (1 SLOAD)

```solidity
  226:         proposalCount++; //@audit should cache "++proposalCount" and use the cache instead of triple SLOAD below
  228:         newProposal.id = proposalCount; //@audit should use proposalCount's cache
  247:         latestProposalIds[newProposal.proposer] = newProposal.id; //@audit should use proposalCount's cache
  251:             newProposal.id, //@audit should use proposalCount's cache
  257:             newProposal.startBlock, //@audit SLOAD, should use temp.startBlock
  258:             newProposal.endBlock, //@audit SLOAD, should use temp.endBlock
  271:             newProposal.startBlock, //@audit SLOAD, should use temp.startBlock
  272:             newProposal.endBlock, //@audit SLOAD, should use temp.endBlock
  273:             newProposal.proposalThreshold, //@audit SLOAD, should use temp.proposalThreshold;
  278:         return newProposal.id;//@audit should use proposalCount's cache
```

- `timelock` (1 SLOAD)

```solidity
  313:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))), //@audit SLOAD (timelock)
  316:         timelock.queueTransaction(target, value, signature, data, eta); //@audit SLOAD (timelock)
```

- `vetoer` (1 SLOAD)

```solidity
  375:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned'); //@audit SLOAD (vetoer)
  376:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer'); //@audit SLOAD (vetoer)
```

- `emit` should be called with existing variables before setting a variable in storage (3 SLOAD)

```solidity
  630:         emit VotingDelaySet(oldVotingDelay, votingDelay);//@audit SLOAD (should emit before assigning "votingDelay = newVotingDelay;")
  646:         emit VotingPeriodSet(oldVotingPeriod, votingPeriod); //@audit SLOAD (should emit before assigning "votingPeriod = newVotingPeriod;")
  664:         emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS); //@audit SLOAD (should emit before assigning "proposalThresholdBPS = newProposalThresholdBPS;")
```

- Should use existing memory variables (3 SLOAD)

```solidity
  826:         admin = pendingAdmin; //@audit SLOAD (pendingAdmin, should use "oldPendingAdmin")
  831:         emit NewAdmin(oldAdmin, admin);//@audit SLOAD (admin, should use "oldPendingAdmin")
  832:         emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);//@audit SLOAD (pendingAdmin, should use "address(0)")```
```
- `vetoer` (1 SLOAD)
```solidity
  840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only'); //@audit SLOAD (vetoer)
  842:         emit NewVetoer(vetoer, newVetoer); //@audit SLOAD (vetoer)
```

- `quorumVotesBPS` (1 SLOAD)

```solidity
  942:                     minQuorumVotesBPS: safe16(quorumVotesBPS), //@audit SLOAD (quorumVotesBPS)
  943:                     maxQuorumVotesBPS: safe16(quorumVotesBPS), //@audit SLOAD (quorumVotesBPS)
```

### 1.3. contracts/governance/NounsDAOProxy.sol

- `emit` should be called with existing variables before setting a variable in storage (1 SLOAD)

```solidity
  85:         emit NewImplementation(oldImplementation, implementation); //@audit SLOAD (should emit before assigning "implementation = implementation_;")
```

## 2. Unchecking arithmetics operations that can't underflow/overflow

Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block: <https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic>

Consider wrapping with an `unchecked` block here (around **25 gas saved** per instance):

```solidity
base/ERC721Checkpointable.sol:184:            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
governance/NounsDAOLogicV2.sol:951:            uint256 center = upper - (upper - lower) / 2;
```

## 3. Tightly pack `struct Proposal`

Here, `struct Proposal` can be tightly packed:

```diff
     struct Proposal {
         /// @notice Unique id for looking up a proposal
         uint256 id;
-        /// @notice Creator of the proposal
-        address proposer;
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
+        /// @notice Creator of the proposal
+        address proposer; //@audit 20 bytes + bools that are 1 byte
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

Which would save 1 storage slot.

## 4. Tightly pack `struct ProposalCondensed`

Here, `struct ProposalCondensed` can be tightly packed:

```diff
    struct ProposalCondensed {
        /// @notice Unique id for looking up a proposal
        uint256 id;
-       /// @notice Creator of the proposal
-       address proposer;
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
+       /// @notice Creator of the proposal
+       address proposer; //@audit 20 bytes + bools that are 1 byte
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

Which would save 1 storage slot.

## 5. Boolean comparisons

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value.
Consider using `if(!directValue)` instead of `if(directValue == false)` here:

```solidity
governance/NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
governance/NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

## 6. Splitting `require()` statements that use `&&` saves gas

See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

Affected code (saving around **3 gas** per instance):

```solidity
governance/NounsDAOLogicV1.sol:127:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
governance/NounsDAOLogicV1.sol:131:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
governance/NounsDAOLogicV1.sol:135:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
governance/NounsDAOLogicV1.sol:139:            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
governance/NounsDAOLogicV1.sol:192:            targets.length == values.length &&
governance/NounsDAOLogicV1.sol:193:                targets.length == signatures.length &&
governance/NounsDAOLogicV1.sol:532:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
governance/NounsDAOLogicV1.sol:548:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
governance/NounsDAOLogicV1.sol:565:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
governance/NounsDAOLogicV1.sol:583:            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV2.sol:138:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
governance/NounsDAOLogicV2.sol:142:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
governance/NounsDAOLogicV2.sol:146:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
governance/NounsDAOLogicV2.sol:202:            targets.length == values.length &&
governance/NounsDAOLogicV2.sol:203:                targets.length == signatures.length &&
governance/NounsDAOLogicV2.sol:624:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
governance/NounsDAOLogicV2.sol:640:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
governance/NounsDAOLogicV2.sol:657:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
governance/NounsDAOLogicV2.sol:678:            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

## 7. Using private rather than public for constants saves gas

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table.

```solidity
governance/NounsDAOLogicV1.sol:70:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
governance/NounsDAOLogicV1.sol:73:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
governance/NounsDAOLogicV1.sol:76:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
governance/NounsDAOLogicV1.sol:79:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
governance/NounsDAOLogicV1.sol:82:    uint256 public constant MIN_VOTING_DELAY = 1;
governance/NounsDAOLogicV1.sol:85:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
governance/NounsDAOLogicV1.sol:88:    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
governance/NounsDAOLogicV1.sol:91:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
governance/NounsDAOLogicV1.sol:94:    uint256 public constant proposalMaxOperations = 10; // 10 actions
governance/NounsDAOLogicV2.sol:62:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
governance/NounsDAOLogicV2.sol:65:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
governance/NounsDAOLogicV2.sol:68:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
governance/NounsDAOLogicV2.sol:71:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
governance/NounsDAOLogicV2.sol:74:    uint256 public constant MIN_VOTING_DELAY = 1;
governance/NounsDAOLogicV2.sol:77:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
governance/NounsDAOLogicV2.sol:80:    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
governance/NounsDAOLogicV2.sol:83:    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
governance/NounsDAOLogicV2.sol:86:    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
governance/NounsDAOLogicV2.sol:89:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
governance/NounsDAOLogicV2.sol:92:    uint256 public constant proposalMaxOperations = 10; // 10 actions
governance/NounsDAOLogicV2.sol:95:    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
governance/NounsDAOLogicV2.sol:98:    uint256 public constant REFUND_BASE_GAS = 36000;
```

## 8. Use shift right/left instead of division/multiplication if possible

While the `DIV` / `MUL` opcode uses 5 gas, the `SHR` / `SHL` opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated, so the calculation can be unchecked in Solidity version `0.8+`

- Use `>> 1` instead of `/ 2`
- Use `>> 2` instead of `/ 4`
- Use `<< 3` instead of `* 8`

Affected code (saves around **2 gas + 20 for unchecked** per instance):

```solidity
base/ERC721Checkpointable.sol:184:            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
governance/NounsDAOLogicV2.sol:951:            uint256 center = upper - (upper - lower) / 2;
```

## 9. `<array>.length` should not be looked up in every loop of a `for-loop`

Reading array length at each iteration of the loop consumes more gas than necessary.
  
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around **3 gas** per iteration.
In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Here, consider storing the array's length in a variable before the for-loop, and use this new variable instead:

```solidity
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## 10. `++i` costs less gas compared to `i++` or `i += 1` (same for `--i` vs `i--` or `i -= 1`)

Pre-increments and pre-decrements are cheaper.

For a `uint256 i` variable, the following is true with the Optimizer enabled at 10k:

**Increment:**

- `i += 1` is the most expensive form
- `i++` costs 6 gas less than `i += 1`
- `++i` costs 5 gas less than `i++` (11 gas less than `i += 1`)

**Decrement:**

- `i -= 1` is the most expensive form
- `i--` costs 11 gas less than `i -= 1`
- `--i` costs 5 gas less than `i--` (16 gas less than `i -= 1`)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name *post-increment*:

```solidity
uint i = 1;  
uint j = 2;
require(j == i++, "This will be false as i is incremented after the comparison");
```
  
However, pre-increments (or pre-decrements) return the new value:
  
```solidity
uint i = 1;  
uint j = 2;
require(j == ++i, "This will be true as i is incremented before the comparison");
```
  
In the pre-increment case, the compiler has to create a temporary variable (when used) for returning `1` instead of `2`.  
  
Affected code:  

```solidity
governance/NounsDAOLogicV1.sol:216:        proposalCount++;
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:226:        proposalCount++;
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

Consider using pre-increments and pre-decrements where they are relevant (meaning: not where post-increments/decrements logic are relevant).

## 11. Increments/decrements can be unchecked in for-loops

In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.  
  
[ethereum/solidity#10695](https://github.com/ethereum/solidity/issues/10695)

Consider wrapping with an `unchecked` block here (around **25 gas saved** per instance):

```solidity
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

The change would be:  
  
```diff
- for (uint256 i; i < numIterations; i++) {
+ for (uint256 i; i < numIterations;) {
 // ...  
+   unchecked { ++i; }
}  
```

The same can be applied with decrements (which should use `break` when `i == 0`).

The risk of overflow is non-existent for `uint256` here.

## 12. It costs more gas to initialize variables with their default value than letting the default value be applied

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...). Explicitly initializing it with its default value is an anti-pattern and wastes gas (around **3 gas** per instance).

Affected code:

```solidity
base/ERC721Checkpointable.sol:41:    uint8 public constant decimals = 0;
base/ERC721Checkpointable.sol:181:        uint32 lower = 0;
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:948:        uint256 lower = 0;
```

Consider removing explicit initializations for default values.

## 13. Upgrade pragma

Using newer compiler versions and the optimizer give gas optimizations. Also, additional safety checks are available for free.

The advantages here are:

- **Contract existence checks** (>= 0.8.10): external calls skip contract existence checks if the external call has a return value

Consider upgrading here :

```solidity
base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
governance/NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
```

## 14. Use Custom Errors instead of Revert Strings to save Gas

Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

Additionally, custom errors can be used inside and outside of contracts (including interfaces and libraries).

Source: <https://blog.soliditylang.org/2021/04/21/custom-errors/>:
> Starting from [Solidity v0.8.4](https://github.com/ethereum/solidity/releases/tag/v0.8.4), there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., `revert("Insufficient funds.");`), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Consider replacing **all revert strings** with custom errors in the solution, and particularly those that have multiple occurrences:

```solidity
base/ERC721Checkpointable.sol:140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
base/ERC721Checkpointable.sol:141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
base/ERC721Checkpointable.sol:142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
base/ERC721Checkpointable.sol:164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
base/ERC721Checkpointable.sol:254:        require(n < 2**32, errorMessage);
base/ERC721Checkpointable.sol:259:        require(n < 2**96, errorMessage);
base/ERC721Checkpointable.sol:269:        require(c >= a, errorMessage);
base/ERC721Checkpointable.sol:278:        require(b <= a, errorMessage);
base/ERC721Enumerable.sol:62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
base/ERC721Enumerable.sol:77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
governance/NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
governance/NounsDAOLogicV1.sol:123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
governance/NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
governance/NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
governance/NounsDAOLogicV1.sol:126:        require(
governance/NounsDAOLogicV1.sol:130:        require(
governance/NounsDAOLogicV1.sol:134:        require(
governance/NounsDAOLogicV1.sol:138:        require(
governance/NounsDAOLogicV1.sol:187:        require(
governance/NounsDAOLogicV1.sol:191:        require(
governance/NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
governance/NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
governance/NounsDAOLogicV1.sol:203:            require(
governance/NounsDAOLogicV1.sol:207:            require(
governance/NounsDAOLogicV1.sol:275:        require(
governance/NounsDAOLogicV1.sol:301:        require(
governance/NounsDAOLogicV1.sol:313:        require(
governance/NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
governance/NounsDAOLogicV1.sol:339:        require(
governance/NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
governance/NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
governance/NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
governance/NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
governance/NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
governance/NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
governance/NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
governance/NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
governance/NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
governance/NounsDAOLogicV1.sol:531:        require(
governance/NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
governance/NounsDAOLogicV1.sol:547:        require(
governance/NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
governance/NounsDAOLogicV1.sol:564:        require(
governance/NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
governance/NounsDAOLogicV1.sol:582:        require(
governance/NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
governance/NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
governance/NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
governance/NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
governance/NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
governance/NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
governance/NounsDAOLogicV2.sol:137:        require(
governance/NounsDAOLogicV2.sol:141:        require(
governance/NounsDAOLogicV2.sol:145:        require(
governance/NounsDAOLogicV2.sol:197:        require(
governance/NounsDAOLogicV2.sol:201:        require(
governance/NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
governance/NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
governance/NounsDAOLogicV2.sol:213:            require(
governance/NounsDAOLogicV2.sol:217:            require(
governance/NounsDAOLogicV2.sol:286:        require(
governance/NounsDAOLogicV2.sol:312:        require(
governance/NounsDAOLogicV2.sol:324:        require(
governance/NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
governance/NounsDAOLogicV2.sol:350:        require(
governance/NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
governance/NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
governance/NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
governance/NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
governance/NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
governance/NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
governance/NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
governance/NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
governance/NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
governance/NounsDAOLogicV2.sol:623:        require(
governance/NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
governance/NounsDAOLogicV2.sol:639:        require(
governance/NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
governance/NounsDAOLogicV2.sol:656:        require(
governance/NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:677:        require(
governance/NounsDAOLogicV2.sol:682:        require(
governance/NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:705:        require(
governance/NounsDAOLogicV2.sol:709:        require(
governance/NounsDAOLogicV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
governance/NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
governance/NounsDAOLogicV2.sol:853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
governance/NounsDAOLogicV2.sol:1019:        require(n <= type(uint32).max, errorMessage);
governance/NounsDAOProxy.sol:79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
governance/NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```
