# Gas Report

## Table of Contents

- [Address mappings can be combined in a single mapping](#address-mappings-can-be-combined-in-a-single-mapping)
- [Arithmetic operations revert by default](#arithmetic-operations-revert-by-default)
- [Array length should not be looked up in every iteration](#array-length-should-not-be-looked-up-in-every-iteration)
- [Bools for storage are expensive](#bool-for-storage-are-expensive)
- [Boolean expressions](#boolean-expressions)
- [Bytes constant are cheaper than string constants](#bytes-constants-are-cheaper-than-string-constants)
- [Caching storage variables in local variables to save gas](#caching-storage-variables-in-local-variables-to-save-gas)
- [Calldata instead of memory for RO function parameters](#calldata-instead-of-memory-for-ro-function-parameters)
- [Constant expressions](#constant-expressions)
- [Constants can be private](#constants-can-be-private)
- [Custom errors](#custom-errors)
- [Event emitting of local variable](#event-emitting-of-local-variable)
- [Immutable variables save storage](#immutable-variables-save-storage)
- [Prefix increments](#prefix-increments)
- [Require instead of AND](#require-instead-of-and)
- [Revert strings length](#revert-strings-length)
- [Tight variable packing](#tight-variable-packing)
- [unchecked arithmetic](#unchecked-arithmetic)
- [unnecessary storage initialization of zero](#unnecessary-storage-of-zero)
- [upgrade Solidity compiler version](#upgrade-solidity-compiler-version)



# Address mappings can be combined in a single mapping

## IMPACT

Combining mappings of `address` into a single mapping of `address` to a `struct` can save a `Gssset` (20000 gas) operation per mapping combined. 
This also makes it cheaper for functions reading and writing several of these mappings by saving a `Gkeccak256` operation- 30 gas. 

## PROOF OF CONCEPT

Instances:

### ERC721Checkpointable.sol

```c
ERC721Checkpointable.sol:43:      mapping(address => address) private _delegates;
ERC721Checkpointable.sol:52:      mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
ERC721Checkpointable.sol:55:      mapping(address => uint32) public numCheckpoints;
ERC721Checkpointable.sol:66:      mapping(address => uint256) public nonces;
```

## TOOLS USED

Manual Analysis

## MITIGATION

Combine the `address` mappings aforementionned in a single `address` => `struct` mapping

# Arithmetic operations revert by default

## IMPACT

Since Solidity `0.8.0`, overflow and underflow checks are performed by default by the compiler. This means `ERC721Checkpointable.add96` and `ERC721Checkpointable.sub96` functions are not only  redundant, but add an extra step of checks, which cost extra gas.


## TOOLS USED

Manual Analysis

## MITIGATION

Remove these functions and use Solidity's native arithmetic functions instead

# Array length should not be looked up in every iteration

## IMPACT

It wastes gas to read an array's length in every iteration of a `for` loop, even if it is a memory or calldata array: `3` gas per read.


## PROOF OF CONCEPT

8 instances:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:281:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:319:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:346:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:371:          for (uint256 i = 0; i < proposal.targets.length; i++)
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:292:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:330:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:357:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:382:          for (uint256 i = 0; i < proposal.targets.length; i++)
```


## TOOLS USED

Manual Analysis

## MITIGATION

Caching the length in a variable before the `for` loop

# Bools for storage are expensive

## IMPACT

Booleans are more expensive than uint256: each write operation emits an extra `SLOAD` to first read the slot's contents, replace the bits taken up by the boolean, and then write back. 
Compared to using `uint256`, this costs an extra `100` gas, even one Gsset `20000` gas when changing from 'false' to 'true' - if this is not the first time setting it to `true`.

## PROOF OF CONCEPT

11 instances:

### NounsDAOInterfaces.sol

```c
NounsDAOInterfaces.sol:204:          bool canceled;
NounsDAOInterfaces.sol:206:          bool vetoed;
NounsDAOInterfaces.sol:208:          bool executed;
NounsDAOInterfaces.sol:216:          bool hasVoted;
NounsDAOInterfaces.sol:304:          bool canceled;
NounsDAOInterfaces.sol:306:          bool vetoed;
NounsDAOInterfaces.sol:308:          bool executed;
NounsDAOInterfaces.sol:320:          bool hasVoted;
NounsDAOInterfaces.sol:390:          bool canceled;
NounsDAOInterfaces.sol:392:          bool vetoed;
NounsDAOInterfaces.sol:394:          bool executed;
```


## TOOLS USED

Manual Analysis

## MITIGATION

Use `uint256(1)` and `int256(2)` instead of `true` `false` for the booleans of the `Proposal` structs. This can mean very interesting savings as they are written on every `propose`, `queue` and `execute` call on the logic contracts.

# Boolean expressions

## IMPACT

Comparing to a constant (`true` or `false`) is a bit more expensive than directly checking the returned boolean value

## PROOF OF CONCEPT

1 instance:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:504:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted')
```

## TOOLS USED

Manual Analysis

## MITIGATION

Replace `receipt.hasVoted == false` with `!receipt.hasVoted`

# Bytes constant are cheaper than string constants

## IMPACT

If the string can fit into 32 bytes, then `bytes32` is cheaper than `string`. `string`  is a dynamically sized-type, which has current limitations in Solidity compared to a statically sized variable.



## PROOF OF CONCEPT

Instances:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:67: string public constant name = 'Nouns DAO'
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:59: string public constant name = 'Nouns DAO'
```

## TOOLS USED

Manual Analysis

## MITIGATION

Replace `string constant` with `bytes(1..32) constant`

# Caching storage variables in local variables to save gas

## IMPACT

Anytime you are reading from storage more than once, it is cheaper in gas cost to cache the variable: a SLOAD cost 100gas, while MLOAD and MSTORE cost 3 gas.

## PROOF OF CONCEPT

3 instances:

### NounsDAOLogicV2.sol

scope: `propose()`

- `nouns` is read twice

```c
NounsDAOLogicV2.sol:193:         temp.totalSupply = nouns.totalSupply();
NounsDAOLogicV2.sol:198: nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold
```

- `proposalCount` is read twice

```c
NounsDAOLogicV2.sol:227:         Proposal storage newProposal = _proposals[proposalCount];
NounsDAOLogicV2.sol:228:         newProposal.id = proposalCount;
```

- `newProposal.id` is read twice

```c
NounsDAOLogicV2.sol:247: latestProposalIds[newProposal.proposer] = newProposal.id;
NounsDAOLogicV2.sol:278:         return newProposal.id;
```

you can also cache here `newProposal.proposer` with `msg.sender`


## TOOLS USED

Manual Analysis

## MITIGATION

cache these storage variables using local variables.

# Calldata instead of memory for RO function parameters

## PROBLEM

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory.
Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory,but it alleviates the compiler from the `abi.decode()` step that copies each index of the calldata to the memory index, each iteration costing `60` gas.


## PROOF OF CONCEPT

29 instances:

### ERC721Checkpointable.sol

```c
ERC721Checkpointable.sol:252:      function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32)
ERC721Checkpointable.sol:257:      function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96)
ERC721Checkpointable.sol:265:          string memory errorMessage,
ERC721Checkpointable.sol:275:          string memory errorMessage
```

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:175:          address[] memory targets
NounsDAOLogicV1.sol:176:          uint256[] memory values
NounsDAOLogicV1.sol:177:          string[] memory signatures
NounsDAOLogicV1.sol:178:          bytes[] memory calldatas
NounsDAOLogicV1.sol:179:          string memory description
NounsDAOLogicV1.sol:181:          ProposalTemp memory temp;
NounsDAOLogicV1.sol:297:          string memory signature
NounsDAOLogicV1.sol:298:          bytes memory data
NounsDAOLogicV1.sol:396:              address[] memory targets
NounsDAOLogicV1.sol:397:              uint256[] memory values
NounsDAOLogicV1.sol:398:              string[] memory signatures
NounsDAOLogicV1.sol:399:              bytes[] memory calldatas
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:185:          address[] memory targets
NounsDAOLogicV2.sol:186:          uint256[] memory values
NounsDAOLogicV2.sol:187:          string[] memory signatures
NounsDAOLogicV2.sol:188:          bytes[] memory calldatas
NounsDAOLogicV2.sol:189:          string memory description
NounsDAOLogicV2.sol:191:          ProposalTemp memory temp;
NounsDAOLogicV2.sol:308:          string memory signature
NounsDAOLogicV2.sol:309:          bytes memory data
NounsDAOLogicV2.sol:407:              address[] memory targets
NounsDAOLogicV2.sol:408:              uint256[] memory values
NounsDAOLogicV2.sol:409:              string[] memory signatures
NounsDAOLogicV2.sol:410:              bytes[] memory calldatas 
NounsDAOLogicV2.sol:536:          string memory reason
```

## TOOLS USED

Manual Analysis

## MITIGATION

Replace `memory` with `calldata`

# Constant expressions

## IMPACT

Constant expressions are [re-calculated each time they are in use](https://github.com/ethereum/solidity/issues/9232), costing an extra `97` gas than a constant every time they are called. 

## PROOF OF CONCEPT

6 instances include:

### ERC721Checkpointable.sol

```c
ERC721Checkpointable.sol:59: bytes32 public constant DOMAIN_TYPEHASH =
         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)')
ERC721Checkpointable.sol:63: bytes32 public constant DELEGATION_TYPEHASH =
         keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
```

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:97: bytes32 public constant DOMAIN_TYPEHASH =
         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
NounsDAOLogicV1.sol:101:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:101: bytes32 public constant DOMAIN_TYPEHASH =
         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
NounsDAOLogicV2.sol:105:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

## TOOLS USED

Manual Analysis

## MITIGATION

Mark these as `immutable` instead of `constant`

# Constants can be private

## IMPACT

Marking constants as `private` save gas upon deployment, as the compiler does not have to create getter functions for these variables. It is worth noting that a `private` variable can still be read using either the verified contract source code or the bytecode.

## PROOF OF CONCEPT

23 instances:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:70:      uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%,
NounsDAOLogicV1.sol:73:      uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%,
NounsDAOLogicV1.sol:76:      uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours,
NounsDAOLogicV1.sol:79:      uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks,
NounsDAOLogicV1.sol:82:      uint256 public constant MIN_VOTING_DELAY = 1;
NounsDAOLogicV1.sol:85:      uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week,
NounsDAOLogicV1.sol:88:      uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%,
NounsDAOLogicV1.sol:91:      uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%,
NounsDAOLogicV1.sol:94:      uint256 public constant proposalMaxOperations = 10; // 10 actions
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:59:      string public constant name = 'Nouns DAO'
NounsDAOLogicV2.sol:62:      uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%,
NounsDAOLogicV2.sol:65:      uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%,
NounsDAOLogicV2.sol:68:      uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours,
NounsDAOLogicV2.sol:71:      uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks,
NounsDAOLogicV2.sol:74:      uint256 public constant MIN_VOTING_DELAY = 1;,
NounsDAOLogicV2.sol:77:      uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week,
NounsDAOLogicV2.sol:80:      uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%,
NounsDAOLogicV2.sol:83:      uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%,
NounsDAOLogicV2.sol:86:      uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%,
NounsDAOLogicV2.sol:89:      uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%,
NounsDAOLogicV2.sol:92:      uint256 public constant proposalMaxOperations = 10; // 10 actions,
NounsDAOLogicV2.sol:95:      uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
NounsDAOLogicV2.sol:98:      uint256 public constant REFUND_BASE_GAS = 36000;
```

## TOOLS USED

Manual Analysis

## MITIGATION

Make the constants `private` instead of `public`

# Custom Errors

## IMPACT

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained [here](https://blog.soliditylang.org/2021/04/21/custom-errors/)

Custom errors are defined using the error statement

## PROOF OF CONCEPT

115 instances:

### ERC721Checkpointable.sol

```c
ERC721Checkpointable.sol:139:          require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
ERC721Checkpointable.sol:140:          require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
ERC721Checkpointable.sol:141:          require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
ERC721Checkpointable.sol:163:          require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
ERC721Checkpointable.sol:253:          require(n < 2**32, errorMessage);
ERC721Checkpointable.sol:258:          require(n < 2**96, errorMessage);
ERC721Checkpointable.sol:268:          require(c >= a, errorMessage);
ERC721Checkpointable.sol:277:          require(b <= a, errorMessage);
```

### ERC721Enumerable.sol

```c
ERC721Enumerable.sol:61:          require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
ERC721Enumerable.sol:76:          require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:122:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
NounsDAOLogicV1.sol:123:          require(msg.sender == admin, 'NounsDAO::initialize: admin only');
NounsDAOLogicV1.sol:124:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
NounsDAOLogicV1.sol:125:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
NounsDAOLogicV1.sol:126:          require(
NounsDAOLogicV1.sol:127:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:128:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV1.sol:129:          );
NounsDAOLogicV1.sol:130:          require(
NounsDAOLogicV1.sol:131:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:132:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV1.sol:133:          );
NounsDAOLogicV1.sol:134:          require(
NounsDAOLogicV1.sol:135:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:136:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:137:          );
NounsDAOLogicV1.sol:138:          require(
NounsDAOLogicV1.sol:139:              quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:140:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:141:          );
NounsDAOLogicV1.sol:187:          require(
NounsDAOLogicV1.sol:188:              nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
NounsDAOLogicV1.sol:189:              'NounsDAO::propose: proposer votes below proposal threshold'
NounsDAOLogicV1.sol:190:          );
NounsDAOLogicV1.sol:191:          require(
NounsDAOLogicV1.sol:192:              targets.length == values.length &&
NounsDAOLogicV1.sol:193:                  targets.length == signatures.length &&
NounsDAOLogicV1.sol:194:                  targets.length == calldatas.length,
NounsDAOLogicV1.sol:195:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV1.sol:196:          );
NounsDAOLogicV1.sol:197:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');
NounsDAOLogicV1.sol:198:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
NounsDAOLogicV1.sol:203:              require(
NounsDAOLogicV1.sol:204:                  proposersLatestProposalState != ProposalState.Active,
NounsDAOLogicV1.sol:205:                  'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
NounsDAOLogicV1.sol:206:              );
NounsDAOLogicV1.sol:207:              require(
NounsDAOLogicV1.sol:208:                  proposersLatestProposalState != ProposalState.Pending,
NounsDAOLogicV1.sol:209:                  'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
NounsDAOLogicV1.sol:210:              );
NounsDAOLogicV1.sol:275:          require(
NounsDAOLogicV1.sol:276:              state(proposalId) == ProposalState.Succeeded,
NounsDAOLogicV1.sol:277:              'NounsDAO::queue: proposal can only be queued if it is succeeded'
NounsDAOLogicV1.sol:278:          );
NounsDAOLogicV1.sol:301:          require(
NounsDAOLogicV1.sol:302:              !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
NounsDAOLogicV1.sol:303:              'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
NounsDAOLogicV1.sol:304:          );
NounsDAOLogicV1.sol:313:          require(
NounsDAOLogicV1.sol:314:              state(proposalId) == ProposalState.Queued,
NounsDAOLogicV1.sol:315:              'NounsDAO::execute: proposal can only be executed if it is queued'
NounsDAOLogicV1.sol:316:          );
NounsDAOLogicV1.sol:336:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
NounsDAOLogicV1.sol:339:          require(
NounsDAOLogicV1.sol:340:              msg.sender == proposal.proposer ||
NounsDAOLogicV1.sol:341:                  nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
NounsDAOLogicV1.sol:342:              'NounsDAO::cancel: proposer above threshold'
NounsDAOLogicV1.sol:343:          );
NounsDAOLogicV1.sol:364:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
NounsDAOLogicV1.sol:365:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
NounsDAOLogicV1.sol:366:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
NounsDAOLogicV1.sol:422:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
NounsDAOLogicV1.sol:485:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
NounsDAOLogicV1.sol:501:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
NounsDAOLogicV1.sol:502:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
NounsDAOLogicV1.sol:505:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
NounsDAOLogicV1.sol:530:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
NounsDAOLogicV1.sol:531:          require(
NounsDAOLogicV1.sol:532:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:533:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV1.sol:534:          );
NounsDAOLogicV1.sol:546:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
NounsDAOLogicV1.sol:547:          require(
NounsDAOLogicV1.sol:548:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:549:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV1.sol:550:          );
NounsDAOLogicV1.sol:563:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
NounsDAOLogicV1.sol:564:          require(
NounsDAOLogicV1.sol:565:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV1.sol:566:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:567:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:568:          );
NounsDAOLogicV1.sol:581:          require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
NounsDAOLogicV1.sol:582:          require(
NounsDAOLogicV1.sol:583:              newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:584:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:585:          );
NounsDAOLogicV1.sol:599:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
NounsDAOLogicV1.sol:617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
NounsDAOLogicV1.sol:638:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
NounsDAOLogicV1.sol:651:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:133:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
NounsDAOLogicV2.sol:134:          require(msg.sender == admin, 'NounsDAO::initialize: admin only');
NounsDAOLogicV2.sol:135:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
NounsDAOLogicV2.sol:136:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
NounsDAOLogicV2.sol:137:          require(
NounsDAOLogicV2.sol:138:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:139:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV2.sol:140:          );
NounsDAOLogicV2.sol:141:          require(
NounsDAOLogicV2.sol:142:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:143:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV2.sol:144:          );
NounsDAOLogicV2.sol:145:          require(
NounsDAOLogicV2.sol:146:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:147:              'NounsDAO::initialize: invalid proposal threshold bps'
NounsDAOLogicV2.sol:148:          );
NounsDAOLogicV2.sol:197:          require(
NounsDAOLogicV2.sol:198:              nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
NounsDAOLogicV2.sol:199:              'NounsDAO::propose: proposer votes below proposal threshold'
NounsDAOLogicV2.sol:200:          );
NounsDAOLogicV2.sol:201:          require(
NounsDAOLogicV2.sol:202:              targets.length == values.length &&
NounsDAOLogicV2.sol:203:                  targets.length == signatures.length &&
NounsDAOLogicV2.sol:204:                  targets.length == calldatas.length,
NounsDAOLogicV2.sol:205:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV2.sol:206:          );
NounsDAOLogicV2.sol:207:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');
NounsDAOLogicV2.sol:208:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
NounsDAOLogicV2.sol:213:              require(
NounsDAOLogicV2.sol:214:                  proposersLatestProposalState != ProposalState.Active,
NounsDAOLogicV2.sol:215:                  'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
NounsDAOLogicV2.sol:216:              );
NounsDAOLogicV2.sol:217:              require(
NounsDAOLogicV2.sol:218:                  proposersLatestProposalState != ProposalState.Pending,
NounsDAOLogicV2.sol:219:                  'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
NounsDAOLogicV2.sol:220:              );
NounsDAOLogicV2.sol:286:          require(
NounsDAOLogicV2.sol:287:              state(proposalId) == ProposalState.Succeeded,
NounsDAOLogicV2.sol:288:              'NounsDAO::queue: proposal can only be queued if it is succeeded'
NounsDAOLogicV2.sol:289:          );
NounsDAOLogicV2.sol:312:          require(
NounsDAOLogicV2.sol:313:              !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
NounsDAOLogicV2.sol:314:              'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
NounsDAOLogicV2.sol:315:          );
NounsDAOLogicV2.sol:324:          require(
NounsDAOLogicV2.sol:325:              state(proposalId) == ProposalState.Queued,
NounsDAOLogicV2.sol:326:              'NounsDAO::execute: proposal can only be executed if it is queued'
NounsDAOLogicV2.sol:327:          );
NounsDAOLogicV2.sol:347:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
NounsDAOLogicV2.sol:350:          require(
NounsDAOLogicV2.sol:351:              msg.sender == proposal.proposer ||
NounsDAOLogicV2.sol:352:                  nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
NounsDAOLogicV2.sol:353:              'NounsDAO::cancel: proposer above threshold'
NounsDAOLogicV2.sol:354:          );
NounsDAOLogicV2.sol:375:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
NounsDAOLogicV2.sol:376:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
NounsDAOLogicV2.sol:377:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
NounsDAOLogicV2.sol:433:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
NounsDAOLogicV2.sol:577:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
NounsDAOLogicV2.sol:593:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
NounsDAOLogicV2.sol:594:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
NounsDAOLogicV2.sol:597:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
NounsDAOLogicV2.sol:622:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
NounsDAOLogicV2.sol:623:          require(
NounsDAOLogicV2.sol:624:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:625:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV2.sol:626:          );
NounsDAOLogicV2.sol:638:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
NounsDAOLogicV2.sol:639:          require(
NounsDAOLogicV2.sol:640:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:641:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV2.sol:642:          );
NounsDAOLogicV2.sol:655:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
NounsDAOLogicV2.sol:656:          require(
NounsDAOLogicV2.sol:657:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV2.sol:658:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:659:              'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
NounsDAOLogicV2.sol:660:          );
NounsDAOLogicV2.sol:674:          require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
NounsDAOLogicV2.sol:677:          require(
NounsDAOLogicV2.sol:678:              newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
NounsDAOLogicV2.sol:679:                  newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
NounsDAOLogicV2.sol:680:              'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
NounsDAOLogicV2.sol:681:          );
NounsDAOLogicV2.sol:682:          require(
NounsDAOLogicV2.sol:683:              newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
NounsDAOLogicV2.sol:684:              'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
NounsDAOLogicV2.sol:685:          );
NounsDAOLogicV2.sol:702:          require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
NounsDAOLogicV2.sol:705:          require(
NounsDAOLogicV2.sol:706:              newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
NounsDAOLogicV2.sol:707:              'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
NounsDAOLogicV2.sol:708:          );
NounsDAOLogicV2.sol:709:          require(
NounsDAOLogicV2.sol:710:              params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
NounsDAOLogicV2.sol:711:              'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
NounsDAOLogicV2.sol:712:          );
NounsDAOLogicV2.sol:727:          require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
NounsDAOLogicV2.sol:801:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
NounsDAOLogicV2.sol:819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
NounsDAOLogicV2.sol:840: require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only')
NounsDAOLogicV2.sol:853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only')
NounsDAOLogicV2.sol:1019: require(n <= type(uint32).max, errorMessage)
```

## TOOLS USED

Manual Analysis

## MITIGATION

Replace require and revert statements with custom errors.


# Event emitting of local variable

## PROBLEM

When emitting an event, using a local variable instead of a storage variable saves gas.

## PROOF OF CONCEPT

Instances:

### NounsDAOLogicV2.sol

```c
250: emit ProposalCreated(
251:             newProposal.id,
252:             msg.sender,
253:             targets,
254:             values,
255:             signatures,
256:             calldatas,
257:             newProposal.startBlock,
258:             newProposal.endBlock,
259:             description
260:         );
264:         emit ProposalCreatedWithRequirements(
265:             newProposal.id,
266:             msg.sender,
267:             targets,
268:             values,
269:             signatures,
270:             calldatas,
271:             newProposal.startBlock,
272:             newProposal.endBlock,
273:             newProposal.proposalThreshold,
274:             minQuorumVotes(),
275:             description
276:         );
```

All the `newProposal` members can be cached with the values written to them earlier in the function


## TOOLS USED

Manual Analysis


# Immutable variables save storage

## PROBLEM

If a variable is set in the constructor and never modified afterwrds, marking it as `immutable` can save a storage slot - `20,000` gas. This also saves `97` gas on every read access of the variable.
As the `nouns` contract address is known and will not change, set it as `immutable` to save a storage slot

## PROOF OF CONCEPT

### NounsDAOInterfaces.sol

```c
NounsDAOInterfaces.sol:266: NounsTokenLike public nouns;
```

## TOOLS USED

Manual Analysis


# Prefix increments

## IMPACT

Prefix increments are cheaper than postfix increments - `6` gas. This can mean interesting savings in `for` loops.

## PROOF OF CONCEPT

10 instances:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:216:          proposalCount++;
NounsDAOLogicV1.sol:281:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:319:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:346:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:371:          for (uint256 i = 0; i < proposal.targets.length; i++)
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:226:          proposalCount++;
NounsDAOLogicV2.sol:292:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:330:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:357:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:382:          for (uint256 i = 0; i < proposal.targets.length; i++)
```

## TOOLS USED

Manual Analysis

## MITIGATION

change `variable++` to `++variable`.

# Require instead of AND

## IMPACT

Require statements including conditions with the `&&` operator can be broken down in multiple require statements to save gas upon function calls. This does cost more upon deployment, meaning there is a trade-off to be decided by the team.

## PROOF OF CONCEPT

20 instances:


### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:126:          require(
NounsDAOLogicV1.sol:127:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:128:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV1.sol:129:          );
NounsDAOLogicV1.sol:130:          require(
NounsDAOLogicV1.sol:131:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:132:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV1.sol:133:          );
NounsDAOLogicV1.sol:134:          require(
NounsDAOLogicV1.sol:135:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:136:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:137:          );
NounsDAOLogicV1.sol:138:          require(
NounsDAOLogicV1.sol:139:              quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:140:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:141:          );
NounsDAOLogicV1.sol:191:          require(
NounsDAOLogicV1.sol:192:              targets.length == values.length &&
NounsDAOLogicV1.sol:193:                  targets.length == signatures.length &&
NounsDAOLogicV1.sol:194:                  targets.length == calldatas.length,
NounsDAOLogicV1.sol:195:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV1.sol:531:          require(
NounsDAOLogicV1.sol:532:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:533:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV1.sol:534:          );
NounsDAOLogicV1.sol:546:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
NounsDAOLogicV1.sol:547:          require(
NounsDAOLogicV1.sol:548:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:549:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV1.sol:550:          );
NounsDAOLogicV1.sol:564:          require(
NounsDAOLogicV1.sol:565:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV1.sol:566:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:567:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:568:          );
NounsDAOLogicV1.sol:582:          require(
NounsDAOLogicV1.sol:583:              newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:584:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:585:          );
NounsDAOLogicV1.sol:617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:137:          require(
NounsDAOLogicV2.sol:138:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:139:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV2.sol:140:          );
NounsDAOLogicV2.sol:141:          require(
NounsDAOLogicV2.sol:142:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:143:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV2.sol:144:          );
NounsDAOLogicV2.sol:145:          require(
NounsDAOLogicV2.sol:146:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:147:              'NounsDAO::initialize: invalid proposal threshold bps'
NounsDAOLogicV2.sol:148:          );
NounsDAOLogicV2.sol:201:          require(
NounsDAOLogicV2.sol:202:              targets.length == values.length &&
NounsDAOLogicV2.sol:203:                  targets.length == signatures.length &&
NounsDAOLogicV2.sol:204:                  targets.length == calldatas.length,
NounsDAOLogicV2.sol:205:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV2.sol:206:          );
NounsDAOLogicV2.sol:623:          require(
NounsDAOLogicV2.sol:624:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:625:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV2.sol:626:          );
NounsDAOLogicV2.sol:639:          require(
NounsDAOLogicV2.sol:640:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:641:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV2.sol:642:          );
NounsDAOLogicV2.sol:656:          require(
NounsDAOLogicV2.sol:657:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV2.sol:658:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:659:              'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
NounsDAOLogicV2.sol:660:          );
NounsDAOLogicV2.sol:677:          require(
NounsDAOLogicV2.sol:678:              newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
NounsDAOLogicV2.sol:679:                  newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
NounsDAOLogicV2.sol:680:              'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
NounsDAOLogicV2.sol:681:          );
NounsDAOLogicV2.sol:819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

## TOOLS USED

Manual Analysis

## MITIGATION

Break down each statement into multiple `require` statements. For further savings, do consider using [custom errors](#custom-errors).

# Revert strings length

## IMPACT

Revert strings cost more gas to deploy if the string is larger than 32 bytes.

## PROOF OF CONCEPT

Revert strings exceeding 32 bytes include:

### ERC721Checkpointable.sol

```c
ERC721Checkpointable.sol:139:          require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
ERC721Checkpointable.sol:140:          require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
ERC721Checkpointable.sol:141:          require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
ERC721Checkpointable.sol:163:          require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```

### ERC721Enumerable.sol

```c
ERC721Enumerable.sol:61:          require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
ERC721Enumerable.sol:76:          require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```


### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:122:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
NounsDAOLogicV1.sol:124:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
NounsDAOLogicV1.sol:125:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
NounsDAOLogicV1.sol:126:          require(
NounsDAOLogicV1.sol:127:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:128:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV1.sol:129:          );
NounsDAOLogicV1.sol:130:          require(
NounsDAOLogicV1.sol:131:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:132:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV1.sol:133:          );
NounsDAOLogicV1.sol:134:          require(
NounsDAOLogicV1.sol:135:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:136:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:137:          );
NounsDAOLogicV1.sol:138:          require(
NounsDAOLogicV1.sol:139:              quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:140:              'NounsDAO::initialize: invalid proposal threshold'
NounsDAOLogicV1.sol:141:          );
NounsDAOLogicV1.sol:187:          require(
NounsDAOLogicV1.sol:188:              nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
NounsDAOLogicV1.sol:189:              'NounsDAO::propose: proposer votes below proposal threshold'
NounsDAOLogicV1.sol:190:          );
NounsDAOLogicV1.sol:191:          require(
NounsDAOLogicV1.sol:192:              targets.length == values.length &&
NounsDAOLogicV1.sol:193:                  targets.length == signatures.length &&
NounsDAOLogicV1.sol:194:                  targets.length == calldatas.length,
NounsDAOLogicV1.sol:195:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV1.sol:196:          );
NounsDAOLogicV1.sol:197:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');
NounsDAOLogicV1.sol:198:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
NounsDAOLogicV1.sol:203:              require(
NounsDAOLogicV1.sol:204:                  proposersLatestProposalState != ProposalState.Active,
NounsDAOLogicV1.sol:205:                  'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
NounsDAOLogicV1.sol:206:              );
NounsDAOLogicV1.sol:207:              require(
NounsDAOLogicV1.sol:208:                  proposersLatestProposalState != ProposalState.Pending,
NounsDAOLogicV1.sol:209:                  'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
NounsDAOLogicV1.sol:210:              );
NounsDAOLogicV1.sol:275:          require(
NounsDAOLogicV1.sol:276:              state(proposalId) == ProposalState.Succeeded,
NounsDAOLogicV1.sol:277:              'NounsDAO::queue: proposal can only be queued if it is succeeded'
NounsDAOLogicV1.sol:278:          );
NounsDAOLogicV1.sol:301:          require(
NounsDAOLogicV1.sol:302:              !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
NounsDAOLogicV1.sol:303:              'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
NounsDAOLogicV1.sol:304:          );
NounsDAOLogicV1.sol:313:          require(
NounsDAOLogicV1.sol:314:              state(proposalId) == ProposalState.Queued,
NounsDAOLogicV1.sol:315:              'NounsDAO::execute: proposal can only be executed if it is queued'
NounsDAOLogicV1.sol:316:          );
NounsDAOLogicV1.sol:336:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
NounsDAOLogicV1.sol:339:          require(
NounsDAOLogicV1.sol:340:              msg.sender == proposal.proposer ||
NounsDAOLogicV1.sol:341:                  nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
NounsDAOLogicV1.sol:342:              'NounsDAO::cancel: proposer above threshold'
NounsDAOLogicV1.sol:343:          );
NounsDAOLogicV1.sol:364:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
NounsDAOLogicV1.sol:365:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
NounsDAOLogicV1.sol:366:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
NounsDAOLogicV1.sol:422:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
NounsDAOLogicV1.sol:485:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
NounsDAOLogicV1.sol:501:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
NounsDAOLogicV1.sol:502:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
NounsDAOLogicV1.sol:505:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
NounsDAOLogicV1.sol:530:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
NounsDAOLogicV1.sol:531:          require(
NounsDAOLogicV1.sol:532:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV1.sol:533:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV1.sol:534:          );
NounsDAOLogicV1.sol:546:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
NounsDAOLogicV1.sol:547:          require(
NounsDAOLogicV1.sol:548:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV1.sol:549:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV1.sol:550:          );
NounsDAOLogicV1.sol:564:          require(
NounsDAOLogicV1.sol:565:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV1.sol:566:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV1.sol:567:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:568:          );
NounsDAOLogicV1.sol:582:          require(
NounsDAOLogicV1.sol:583:              newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
NounsDAOLogicV1.sol:584:              'NounsDAO::_setProposalThreshold: invalid proposal threshold'
NounsDAOLogicV1.sol:585:          );
NounsDAOLogicV1.sol:599:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
NounsDAOLogicV1.sol:617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
NounsDAOLogicV1.sol:638:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
NounsDAOLogicV1.sol:651:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:133:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
NounsDAOLogicV2.sol:135:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
NounsDAOLogicV2.sol:136:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
NounsDAOLogicV2.sol:137:          require(
NounsDAOLogicV2.sol:138:              votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:139:              'NounsDAO::initialize: invalid voting period'
NounsDAOLogicV2.sol:140:          );
NounsDAOLogicV2.sol:141:          require(
NounsDAOLogicV2.sol:142:              votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:143:              'NounsDAO::initialize: invalid voting delay'
NounsDAOLogicV2.sol:144:          );
NounsDAOLogicV2.sol:145:          require(
NounsDAOLogicV2.sol:146:              proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:147:              'NounsDAO::initialize: invalid proposal threshold bps'
NounsDAOLogicV2.sol:148:          );
NounsDAOLogicV2.sol:197:          require(
NounsDAOLogicV2.sol:198:              nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
NounsDAOLogicV2.sol:199:              'NounsDAO::propose: proposer votes below proposal threshold'
NounsDAOLogicV2.sol:200:          );
NounsDAOLogicV2.sol:201:          require(
NounsDAOLogicV2.sol:202:              targets.length == values.length &&
NounsDAOLogicV2.sol:203:                  targets.length == signatures.length &&
NounsDAOLogicV2.sol:204:                  targets.length == calldatas.length,
NounsDAOLogicV2.sol:205:              'NounsDAO::propose: proposal function information arity mismatch'
NounsDAOLogicV2.sol:206:          );
NounsDAOLogicV2.sol:207:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');
NounsDAOLogicV2.sol:208:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
NounsDAOLogicV2.sol:213:              require(
NounsDAOLogicV2.sol:214:                  proposersLatestProposalState != ProposalState.Active,
NounsDAOLogicV2.sol:215:                  'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
NounsDAOLogicV2.sol:216:              );
NounsDAOLogicV2.sol:217:              require(
NounsDAOLogicV2.sol:218:                  proposersLatestProposalState != ProposalState.Pending,
NounsDAOLogicV2.sol:219:                  'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
NounsDAOLogicV2.sol:220:              );
NounsDAOLogicV2.sol:286:          require(
NounsDAOLogicV2.sol:287:              state(proposalId) == ProposalState.Succeeded,
NounsDAOLogicV2.sol:288:              'NounsDAO::queue: proposal can only be queued if it is succeeded'
NounsDAOLogicV2.sol:289:          );
NounsDAOLogicV2.sol:312:          require(
NounsDAOLogicV2.sol:313:              !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
NounsDAOLogicV2.sol:314:              'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
NounsDAOLogicV2.sol:315:          );
NounsDAOLogicV2.sol:324:          require(
NounsDAOLogicV2.sol:325:              state(proposalId) == ProposalState.Queued,
NounsDAOLogicV2.sol:326:              'NounsDAO::execute: proposal can only be executed if it is queued'
NounsDAOLogicV2.sol:327:          );
NounsDAOLogicV2.sol:347:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
NounsDAOLogicV2.sol:350:          require(
NounsDAOLogicV2.sol:351:              msg.sender == proposal.proposer ||
NounsDAOLogicV2.sol:352:                  nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
NounsDAOLogicV2.sol:353:              'NounsDAO::cancel: proposer above threshold'
NounsDAOLogicV2.sol:354:          );
NounsDAOLogicV2.sol:375:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
NounsDAOLogicV2.sol:377:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
NounsDAOLogicV2.sol:433:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
NounsDAOLogicV2.sol:577:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
NounsDAOLogicV2.sol:593:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
NounsDAOLogicV2.sol:594:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
NounsDAOLogicV2.sol:597:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
NounsDAOLogicV2.sol:623:          require(
NounsDAOLogicV2.sol:624:              newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
NounsDAOLogicV2.sol:625:              'NounsDAO::_setVotingDelay: invalid voting delay'
NounsDAOLogicV2.sol:626:          );
NounsDAOLogicV2.sol:638:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
NounsDAOLogicV2.sol:639:          require(
NounsDAOLogicV2.sol:640:              newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
NounsDAOLogicV2.sol:641:              'NounsDAO::_setVotingPeriod: invalid voting period'
NounsDAOLogicV2.sol:642:          );
NounsDAOLogicV2.sol:655:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
NounsDAOLogicV2.sol:656:          require(
NounsDAOLogicV2.sol:657:              newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
NounsDAOLogicV2.sol:658:                  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
NounsDAOLogicV2.sol:659:              'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
NounsDAOLogicV2.sol:660:          );
NounsDAOLogicV2.sol:674:          require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
NounsDAOLogicV2.sol:677:          require(
NounsDAOLogicV2.sol:678:              newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
NounsDAOLogicV2.sol:679:                  newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
NounsDAOLogicV2.sol:680:              'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
NounsDAOLogicV2.sol:681:          );
NounsDAOLogicV2.sol:682:          require(
NounsDAOLogicV2.sol:683:              newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
NounsDAOLogicV2.sol:684:              'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
NounsDAOLogicV2.sol:685:          );
NounsDAOLogicV2.sol:702:          require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
NounsDAOLogicV2.sol:705:          require(
NounsDAOLogicV2.sol:706:              newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
NounsDAOLogicV2.sol:707:              'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
NounsDAOLogicV2.sol:708:          );
NounsDAOLogicV2.sol:709:          require(
NounsDAOLogicV2.sol:710:              params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
NounsDAOLogicV2.sol:711:              'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
NounsDAOLogicV2.sol:712:          );
NounsDAOLogicV2.sol:727:          require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
NounsDAOLogicV2.sol:801:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
NounsDAOLogicV2.sol:819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
NounsDAOLogicV2.sol:840: require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only')
NounsDAOLogicV2.sol:853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only')
NounsDAOLogicV2.sol:1019: require(n <= type(uint32).max, errorMessage)
```

## TOOLS USED

Manual Analysis

## MITIGATION

Write the error strings so that they do not exceed 32 bytes. For further gas savings, consider also using [custom errors](#custom-errors).


# Tight Variable Packing

## PROBLEM

Solidity contracts have contiguous 32 bytes (256 bits) slots used in storage.
By arranging the variables, it is possible to minimize the number of slots used within a contract's storage and therefore reduce deployment costs.

address type variables are each of 20 bytes size (way less than 32 bytes). However, they here take up a whole 32 bytes slot (they are contiguous).

As bool type variables are of size 1 byte, there's a slot here that can get saved by moving them closer to an address

## PROOF OF CONCEPT

4 instances :

### NounsDAOInterfaces.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L174-L211
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L274-L315
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L368-L399
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L274-L315
```c
174:     struct Proposal {
175:         /// @notice Unique id for looking up a proposal
176:         uint256 id;
177:         /// @notice Creator of the proposal
178:         address proposer;
179:         /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
180:         uint256 proposalThreshold;
181:         /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
182:         uint256 quorumVotes;
183:         /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
184:         uint256 eta;
185:         /// @notice the ordered list of target addresses for calls to be made
186:         address[] targets;
187:         /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
188:         uint256[] values;
189:         /// @notice The ordered list of function signatures to be called
190:         string[] signatures;
191:         /// @notice The ordered list of calldata to be passed to each call
192:         bytes[] calldatas;
193:         /// @notice The block at which voting begins: holders must delegate their votes prior to this block
194:         uint256 startBlock;
195:         /// @notice The block at which voting ends: votes must be cast prior to this block
196:         uint256 endBlock;
197:         /// @notice Current number of votes in favor of this proposal
198:         uint256 forVotes;
199:         /// @notice Current number of votes in opposition to this proposal
200:         uint256 againstVotes;
201:         /// @notice Current number of votes for abstaining for this proposal
202:         uint256 abstainVotes;
203:         /// @notice Flag marking whether the proposal has been canceled
204:         bool canceled;
205:         /// @notice Flag marking whether the proposal has been vetoed
206:         bool vetoed;
207:         /// @notice Flag marking whether the proposal has been executed
208:         bool executed;
209:         /// @notice Receipts of ballots for the entire set of voters
210:         mapping(address => Receipt) receipts;
211:     }
```

In each struct, put `proposer` before `canceled` to save a slot - ie pack the `address` variable with the `bool` ones.

## TOOLS USED

Manual Analysis



# Unchecked arithmetic

## IMPACT

The default "checked" behavior costs more gas when adding/diving/multiplying, because under-the-hood those checks are implemented as a series of opcodes that, prior to performing the actual arithmetic, check for under/overflow and revert if it is detected.

if it can statically be determined there is no possible way for your arithmetic to under/overflow (such as a condition in an if statement), surrounding the arithmetic in an `unchecked` block will save gas

## PROOF OF CONCEPT

8 instances:

### NounsDAOLogicV1.sol

```c
NounsDAOLogicV1.sol:281:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:319:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:346:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV1.sol:371:          for (uint256 i = 0; i < proposal.targets.length; i++)
```

### NounsDAOLogicV2.sol

```c
NounsDAOLogicV2.sol:292:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:330:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:357:          for (uint256 i = 0; i < proposal.targets.length; i++)
NounsDAOLogicV2.sol:382:          for (uint256 i = 0; i < proposal.targets.length; i++)
```


## TOOLS USED

Manual Analysis

## MITIGATION

Place these `i++` in an `unchecked` block

# unnecessary storage initialization of zero

## IMPACT

`uint256` default value is `0`. It is redundant and wastes gas to initialize storage variables to their default value

## PROOF OF CONCEPT

In `propose`, `newProposal.forVotes`, `newProposal.againstVotes` and `newProposal.abstainVotes` do not need to be initialized.

```c
NounsDAOLogicV2.sol:238: newProposal.forVotes = 0;
NounsDAOLogicV2.sol:239:         newProposal.againstVotes = 0;
NounsDAOLogicV2.sol:240:         newProposal.abstainVotes = 0;
```


## TOOLS USED

Manual Analysis

## MITIGATION

Remove initialization of these struct members.

# Upgrade Solidity compiler version

## IMPACT

- 0.8.10 removes contract existence checks if the external call has a return value - `700` gas

## PROOF OF CONCEPT

All the contracts in scope currently have a floating pragma set to `^0.8.6`


## TOOLS USED

Manual Analysis

## MITIGATION

Upgrade these contracts compiler versions to `^0.8.10`.
