## Summary


### Low Risk Issues
| |Issue|Instances|
|-|:-|:-:|
| [L&#x2011;01] | Nouns will not be able to be transferred once the `block.number` passes `type(uint32).max` | 2 |
| [L&#x2011;02] | Unused/empty `receive()`/`fallback()` function | 1 |
| [L&#x2011;03] | Missing checks for `address(0x0)` when assigning values to `address` state variables | 5 |

Total: 8 instances over 3 issues

### Non-critical Issues
| |Issue|Instances|
|-|:-|:-:|
| [N&#x2011;01] | `public` functions not called by the contract should be declared `external` instead | 8 |
| [N&#x2011;02] | Non-assembly method available | 3 |
| [N&#x2011;03] | `2**<n> - 1` should be re-written as `type(uint<n>).max` | 2 |
| [N&#x2011;04] | `constant`s should be defined rather than using magic numbers | 8 |
| [N&#x2011;05] | Use a more recent version of solidity | 3 |
| [N&#x2011;06] | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 6 |
| [N&#x2011;07] | Constant redefined elsewhere | 11 |
| [N&#x2011;08] | Lines are too long | 7 |
| [N&#x2011;09] | Non-library/interface files should use fixed compiler versions, not floating ones | 4 |
| [N&#x2011;10] | Event is missing `indexed` fields | 21 |
| [N&#x2011;11] | Not using the named return variables anywhere in the function is confusing | 8 |
| [N&#x2011;12] | Typos | 4 |

Total: 85 instances over 12 issues



## Low Risk Issues

### [L&#x2011;01]  Nouns will not be able to be transferred once the `block.number` passes `type(uint32).max`
While this currently equates to ~1260 years, if there's a hard fork which makes block times much more frequent (e.g. to compete with Solana), then this limit may be reached much faster than expected, and transfers and delegations will remain stuck at their existing settings

*There are 2 instances of this issue:*
```solidity
File: /contracts/base/ERC721Checkpointable.sol

238          uint32 blockNumber = safe32(
239              block.number,
240              'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'
241:         );

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L238-L241

```solidity
File: /contracts/governance/NounsDAOLogicV2.sol

923:         uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L923

### [L&#x2011;02]  Unused/empty `receive()`/`fallback()` function
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. `require(msg.sender == address(weth))`). Having no access control on the function means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds

*There is 1 instance of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV2.sol

1030:     receive() external payable {}

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1030

### [L&#x2011;03]  Missing checks for `address(0x0)` when assigning values to `address` state variables

*There are 5 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

605:          pendingAdmin = newPendingAdmin;

642:          vetoer = newVetoer;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L605

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

807:          pendingAdmin = newPendingAdmin;

844:          vetoer = newVetoer;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L807

```solidity
File: contracts/governance/NounsDAOProxy.sol

71:           admin = admin_;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L71

## Non-critical Issues

### [N&#x2011;01]  `public` functions not called by the contract should be declared `external` instead
Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents' functions and change the visibility from `external` to `public`.

*There are 8 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

174       function propose(
175           address[] memory targets,
176           uint256[] memory values,
177           string[] memory signatures,
178           bytes[] memory calldatas,
179           string memory description
180:      ) public returns (uint256) {

649       function _burnVetoPower() public {
650           // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
651:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

660:      function proposalThreshold() public view returns (uint256) {

668:      function quorumVotes() public view returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174-L180

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

184       function propose(
185           address[] memory targets,
186           uint256[] memory values,
187           string[] memory signatures,
188           bytes[] memory calldatas,
189           string memory description
190:      ) public returns (uint256) {

851       function _burnVetoPower() public {
852           // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
853:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

862:      function proposalThreshold() public view returns (uint256) {

1002:     function maxQuorumVotes() public view returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L184-L190

### [N&#x2011;02]  Non-assembly method available 
`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`
There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary

*There are 3 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

285:              chainId := chainid()

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L285

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

679:              chainId := chainid()

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L679

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

1013:             chainId := chainid()

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1013

### [N&#x2011;03]  `2**<n> - 1` should be re-written as `type(uint<n>).max`
Earlier versions of solidity can use `uint<n>(-1)` instead. Expressions not including the `- 1` can often be re-written to accomodate the change (e.g. by using a `>` rather than a `>=`, which will also save some gas)

*There are 2 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

254:          require(n < 2**32, errorMessage);

259:          require(n < 2**96, errorMessage);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L254

### [N&#x2011;04]  `constant`s should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

*There are 8 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

/// @audit 32
254:          require(n < 2**32, errorMessage);

/// @audit 96
259:          require(n < 2**96, errorMessage);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L254

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit 10000
673:          return (number * bps) / 10000;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L673

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit 10000
908:          uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;

/// @audit 1e6
909:          uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;

/// @audit 10000
1007:         return (number * bps) / 10000;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L908

```solidity
File: contracts/governance/NounsDAOProxy.sol

/// @audit 0x20
98:                   revert(add(returnData, 0x20), returndatasize())

/// @audit 0x40
113:              let free_mem_ptr := mload(0x40)

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L98

### [N&#x2011;05]  Use a more recent version of solidity
Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`

*There are 3 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

35:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L35

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

61:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L61

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

53:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L53

### [N&#x2011;06]  Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

*There are 6 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

59        bytes32 public constant DOMAIN_TYPEHASH =
60:           keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

63        bytes32 public constant DELEGATION_TYPEHASH =
64:           keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L59-L60

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

97        bytes32 public constant DOMAIN_TYPEHASH =
98:           keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

101:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L97-L98

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

101       bytes32 public constant DOMAIN_TYPEHASH =
102:          keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

105:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L101-L102

### [N&#x2011;07]  Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

*There are 11 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
59:       string public constant name = 'Nouns DAO';

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
62:       uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
65:       uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
68:       uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
71:       uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
74:       uint256 public constant MIN_VOTING_DELAY = 1;

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
77:       uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
89:       uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
92:       uint256 public constant proposalMaxOperations = 10; // 10 actions

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
101       bytes32 public constant DOMAIN_TYPEHASH =
102:          keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

/// @audit seen in contracts/governance/NounsDAOLogicV1.sol 
105:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L59

### [N&#x2011;08]  Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over [164](https://github.com/aizatto/character-length) characters, the lines below should be split when they reach that length

*There are 7 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOInterfaces.sol

156:      /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo

181:          /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo

256:      /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo

281:          /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo

375:          /// @notice The minimum number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L156

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

507:          /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L507

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

599:          /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L599

### [N&#x2011;09]  Non-library/interface files should use fixed compiler versions, not floating ones

*There are 4 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOInterfaces.sol

33:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L33

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

61:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L61

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

53:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L53

```solidity
File: contracts/governance/NounsDAOProxy.sol

36:   pragma solidity ^0.8.6;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L36

### [N&#x2011;10]  Event is missing `indexed` fields
Index event fields make the field more quickly accessible to off-chain tools that parse events. However, note that each index field costs extra gas during emission, so it's not necessarily best to index the maximum allowed per event (three fields). Each `event` should use three `indexed` fields if there are three or more fields, and gas usage is not particularly of concern for the events in question. If there are fewer than three fields, all of the fields should be indexed.

*There are 21 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

73:       event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L73

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

37        event ProposalCreated(
38            uint256 id,
39            address proposer,
40            address[] targets,
41            uint256[] values,
42            string[] signatures,
43            bytes[] calldatas,
44            uint256 startBlock,
45            uint256 endBlock,
46            string description
47:       );

50        event ProposalCreatedWithRequirements(
51            uint256 id,
52            address proposer,
53            address[] targets,
54            uint256[] values,
55            string[] signatures,
56            bytes[] calldatas,
57            uint256 startBlock,
58            uint256 endBlock,
59            uint256 proposalThreshold,
60            uint256 quorumVotes,
61            string description
62:       );

70:       event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

73:       event ProposalCanceled(uint256 id);

76:       event ProposalQueued(uint256 id, uint256 eta);

79:       event ProposalExecuted(uint256 id);

82:       event ProposalVetoed(uint256 id);

85:       event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

88:       event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);

91:       event NewImplementation(address oldImplementation, address newImplementation);

94:       event ProposalThresholdBPSSet(uint256 oldProposalThresholdBPS, uint256 newProposalThresholdBPS);

97:       event QuorumVotesBPSSet(uint256 oldQuorumVotesBPS, uint256 newQuorumVotesBPS);

100:      event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

103:      event NewAdmin(address oldAdmin, address newAdmin);

106:      event NewVetoer(address oldVetoer, address newVetoer);

111:      event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

114:      event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

117:      event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);

120:      event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);

123:      event Withdraw(uint256 amount, bool sent);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L37-L47

### [N&#x2011;11]  Not using the named return variables anywhere in the function is confusing
Consider changing the variable to be an unnamed one

*There are 8 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit targets
/// @audit values
/// @audit signatures
/// @audit calldatas
392       function getActions(uint256 proposalId)
393           external
394           view
395           returns (
396               address[] memory targets,
397               uint256[] memory values,
398               string[] memory signatures,
399:              bytes[] memory calldatas

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L392-L399

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit targets
/// @audit values
/// @audit signatures
/// @audit calldatas
403       function getActions(uint256 proposalId)
404           external
405           view
406           returns (
407               address[] memory targets,
408               uint256[] memory values,
409               string[] memory signatures,
410:              bytes[] memory calldatas

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L403-L410

### [N&#x2011;12]  Typos

*There are 4 instances of this issue:*
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol

/// @audit contructor
104:      * @notice Used to initialize the contract during delegator contructor

/// @audit priviledges
646:      * @notice Burns veto priviledges

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L104

```solidity
File: /contracts/governance/NounsDAOLogicV2.sol

/// @audit contructor
115:      * @notice Used to initialize the contract during delegator contructor

/// @audit priviledges
848:      * @notice Burns veto priviledges

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L115



