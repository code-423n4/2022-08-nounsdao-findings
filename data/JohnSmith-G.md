| |Issue|Instances|
|-|:-|:-:|
| 1 | `domainSeparator` should not be fully re-calculated during every call | 1 |
| 2 | Cheaper input valdiations should come before expensive operations | 4 |
| 3 | Obsolete checks | 2 |
| 4 | Don't compare boolean expressions to boolean literals | 1 |
| 5 | Public functions to external | 4 |
| 6 | Use a more recent version of solidity | 4 |
| 7 | Functions guaranteed to revert when called by normal users can be marked `payable` | 13 |
| 8 | `require()` strings longer than 32 bytes cost extra gas | 49 |
| 9 | `abi.encode()` is less efficient than `abi.encodePacked()` | 3 |
| 10 | Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant` | 4 |
| 11 | Default value initialization | 7 |
| 12 | Splitting `require()` statements that use `&&` saves gas | 9 |
| 13 | `internal` functions only called once can be inlined to save gas | 2 |
| 14 | Using `private` rather than `public` for constants, saves gas | 16 |
| 15 | `<array>.length` should not be looked up in every loop of a for-loop | 4 |
| 16 | Using `bool`s for storage incurs overhead | 11 |
| 17 | Multiplication/division by 2 should use bit shifting | 2 |
| 18 | Use custom errors rather than `require()` strings to save gas | 56 |
| 19 | Caching storage variables in memory to save gas | 18 |
| 20 |  ++variable costs less gas than variable++, especially when it’s used in for-loops (--variable/variable-- too) | 6 |
| 21 | Unchecking arithmetics operations that can't underflow/overflow | 31 |


## [G-01] `domainSeparator` should not be fully re-calculated during every call
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
571:         bytes32 domainSeparator = keccak256(
572:             abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
573:         );
```
See OpenZeppelin's implmementation where they cache the calculated value, as well as parts of the calculation, to speed up future calculations:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/52eeebecda140ebaf4ec8752ed119d8288287fac/contracts/utils/cryptography/draft-EIP712.sol#L70-L76


## [G-02] Cheaper input valdiations should come before expensive operations
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
184:     function propose(
185:         address[] memory targets,
186:         uint256[] memory values,
187:         string[] memory signatures,
188:         bytes[] memory calldatas,
189:         string memory description
190:     ) public returns (uint256) {
191:         ProposalTemp memory temp;
192: 
193:         temp.totalSupply = nouns.totalSupply();
194: 
195:         temp.proposalThreshold = bps2Uint(proposalThresholdBPS, temp.totalSupply);
196: 
197:         require(
198:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:             'NounsDAO::propose: proposer votes below proposal threshold'
200:         );
201:         require(
202:             targets.length == values.length &&
203:                 targets.length == signatures.length &&
204:                 targets.length == calldatas.length,
205:             'NounsDAO::propose: proposal function information arity mismatch'
206:         );
207:         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208:         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```
`require`s on L201-L208 are just input validation and  should be at the beginning of function block before storage read `nouns.totalSupply();`


## [G-03] Obsolete checks
Unlikely that address(0) becomes alive and do transactions, so such checks are benign but add gas expenses
### Findings 
```
contracts/governance/NounsDAOLogicV2.sol
375:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

819:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```


## [G-04] Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
597:         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

## [G-05] Public functions to external
The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
124:     function initialize(
125:         address timelock_,
126:         address nouns_,
127:         address vetoer_,
128:         uint256 votingPeriod_,
129:         uint256 votingDelay_,
130:         uint256 proposalThresholdBPS_,
131:         DynamicQuorumParams calldata dynamicQuorumParams_
132:     ) public virtual {

184:     function propose(
185:         address[] memory targets,
186:         uint256[] memory values,
187:         string[] memory signatures,
188:         bytes[] memory calldatas,
189:         string memory description
190:     ) public returns (uint256) {

851:     function _burnVetoPower() public {

862:     function proposalThreshold() public view returns (uint256) {
```

## [G-06] Use a more recent version of solidity
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
Prior to 0.8.10 the compiler inserted extra code, including EXTCODESIZE (100 - 2600 gas), to check for contract existence for external calls. In more recent solidity versions, the compiler will not insert these checks if the external call has a return value
### Findings
```
contracts/governance/NounsDAOProxy.sol
36: pragma solidity ^0.8.6;

contracts/base/ERC721Enumerable.sol
28: pragma solidity ^0.8.0;

contracts/base/ERC721Checkpointable.sol
35: pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV2.sol
53: pragma solidity ^0.8.6;
```

## [G-07] Functions guaranteed to revert when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are `CALLVALUE`(2),`DUP1`(3),`ISZERO`(3),`PUSH2`(3),`JUMPI`(10),`PUSH1`(3),`DUP1`(3),`REVERT`(0),`JUMPDEST`(1),`POP`(2), which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
374:     function veto(uint256 proposalId) external {

621:     function _setVotingDelay(uint256 newVotingDelay) external {

637:     function _setVotingPeriod(uint256 newVotingPeriod) external {

654:     function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {

673:     function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {

701:     function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {

726:     function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {

748:     function _setDynamicQuorumParams(

783:     function _withdraw() external {

799:     function _setPendingAdmin(address newPendingAdmin) external {

817:     function _acceptAdmin() external {

839:     function _setVetoer(address newVetoer) public {

851:     function _burnVetoPower() public {
```

## [G-08]  `require()` strings longer than 32 bytes cost extra gas
Each extra chunk of byetes past the original 32 i[incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs 3 gas
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
133:         require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

135:         require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136:         require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

137:         require(
138:             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:             'NounsDAO::initialize: invalid voting period'
140:         );
141:         require(
142:             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:             'NounsDAO::initialize: invalid voting delay'
144:         );
145:         require(
146:             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:             'NounsDAO::initialize: invalid proposal threshold bps'
148:         );

197:         require(
198:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:             'NounsDAO::propose: proposer votes below proposal threshold'
200:         );
201:         require(
202:             targets.length == values.length &&
203:                 targets.length == signatures.length &&
204:                 targets.length == calldatas.length,
205:             'NounsDAO::propose: proposal function information arity mismatch'
206:         );
207:         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208:         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213:             require(
214:                 proposersLatestProposalState != ProposalState.Active,
215:                 'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:             );
217:             require(
218:                 proposersLatestProposalState != ProposalState.Pending,
219:                 'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:             );

286:         require(
287:             state(proposalId) == ProposalState.Succeeded,
288:             'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:         );

312:         require(
313:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314:             'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:         );

324:         require(
325:             state(proposalId) == ProposalState.Queued,
326:             'NounsDAO::execute: proposal can only be executed if it is queued'
327:         );

347:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350:         require(
351:             msg.sender == proposal.proposer ||
352:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353:             'NounsDAO::cancel: proposer above threshold'
354:         );

375:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

377:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:         require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:         require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:         require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
594:         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
623:         require(
624:             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:             'NounsDAO::_setVotingDelay: invalid voting delay'
626:         );

638:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
639:         require(
640:             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:             'NounsDAO::_setVotingPeriod: invalid voting period'
642:         );

655:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
656:         require(
657:             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:             'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:         );

674:         require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

677:         require(
678:             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:             'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:         );
682:         require(
683:             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:             'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:         );

702:         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705:         require(
706:             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:             'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:         );
709:         require(
710:             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:             'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:         );

727:         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

```
contracts/governance/NounsDAOProxy.sol
79:         require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:         require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```

```
contracts/base/ERC721Checkpointable.sol
140:         require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:         require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:         require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:         require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```

```
contracts/base/ERC721Enumerable.sol
62:         require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:         require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

## [G-09] `abi.encode()` is less efficient than `abi.encodePacked()`
Consider replacing where it is possible
### Findings
contracts/governance/
NounsDAOLogicV2.sol
```
313:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

572:             abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

574:         bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

## [G-10] Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
See [this](https://github.com/ethereum/solidity/issues/9232) issue for a detail description of the issue
Constant expressions are left as expressions, not constants.
It is re-calculated each time it is in use.
Each usage of such "constant" costs ~100gas more on each access (it is still a little better than storing the result in storage, but not much..)
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
101:     bytes32 public constant DOMAIN_TYPEHASH =
102:         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

105:     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

```
contracts/base/ERC721Checkpointable.sol
59:     bytes32 public constant DOMAIN_TYPEHASH =
60:         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

63:     bytes32 public constant DELEGATION_TYPEHASH =
64:         keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
```

## [G-11] Default value initialization
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0` etc depending on the data type).
Explicitly initializing it with its default value is an anti-pattern and wastes gas.
While default initialization is taken care of by the compiler for stack variables,  for state variables it is not.
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
231:         newProposal.eta = 0;

238:         newProposal.forVotes = 0;
239:         newProposal.againstVotes = 0;
240:         newProposal.abstainVotes = 0;
241:         newProposal.canceled = false;
242:         newProposal.executed = false;
243:         newProposal.vetoed = false;
```

## [G-12] Splitting `require()` statements that use `&&` saves gas
Instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement (saving 3 gas per `&`):
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
137:         require(
138:             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:             'NounsDAO::initialize: invalid voting period'
140:         );
141:         require(
142:             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:             'NounsDAO::initialize: invalid voting delay'
144:         );
145:         require(
146:             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:             'NounsDAO::initialize: invalid proposal threshold bps'
148:         );

201:         require(
202:             targets.length == values.length &&
203:                 targets.length == signatures.length &&
204:                 targets.length == calldatas.length,
205:             'NounsDAO::propose: proposal function information arity mismatch'
206:         );

623:         require(
624:             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:             'NounsDAO::_setVotingDelay: invalid voting delay'
626:         );

639:         require(
640:             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:             'NounsDAO::_setVotingPeriod: invalid voting period'
642:         );

656:         require(
657:             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:             'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:         );

677:         require(
678:             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:             'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:         );

819:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

## [G-13] `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.
### Findings
```
contracts/base/ERC721Checkpointable.sol
282:     function getChainId() internal view returns (uint256) {
283:         uint256 chainId;
284:         assembly {
285:             chainId := chainid()
286:         }
287:         return chainId;
288:     }
```
can be replaced to:
```diff
- 135:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
+ 135:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), block.chainid, address(this))
```


```
contracts/governance/NounsDAOLogicV2.sol
1010:     function getChainIdInternal() internal view returns (uint256) {
1011:         uint256 chainId;
1012:         assembly {
1013:             chainId := chainid()
1014:         }
1015:         return chainId;
1016:     }
```
can be replaced to:
```diff
- 572:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
+ 572:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), block.chainid, address(this))
```

## [G-14] Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
059:     string public constant name = 'Nouns DAO';

062:     uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

065:     uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

068:     uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

071:     uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

074:     uint256 public constant MIN_VOTING_DELAY = 1;

077:     uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

080:     uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

083:     uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

086:     uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

089:     uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

092:     uint256 public constant proposalMaxOperations = 10; // 10 actions

095:     uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

098:     uint256 public constant REFUND_BASE_GAS = 36000;

101:     bytes32 public constant DOMAIN_TYPEHASH =
102:         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

105:     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

```
contracts/base/ERC721Checkpointable.sol
41:     uint8 public constant decimals = 0;

59:     bytes32 public constant DOMAIN_TYPEHASH =
60:         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');//@audit evaluation, make it immutable

63:     bytes32 public constant DELEGATION_TYPEHASH =
64:         keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
```


## [G-15]  `<array>.length` should not be looked up in every loop of a for-loop
### Problem
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (100 gas)
* memory arrays use `MLOAD` (3 gas)
* calldata arrays use `CALLDATALOAD` (3 gas)
	
Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
292:         for (uint256 i = 0; i < proposal.targets.length; i++) {

330:         for (uint256 i = 0; i < proposal.targets.length; i++) {

357:         for (uint256 i = 0; i < proposal.targets.length; i++) {

382:         for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-16] Using `bool`s for storage incurs overhead

```solidity
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```

<https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27>
Use `uint256(1)` and `uint256(2)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/johnsmith1623/144d23c374a08b1491a71422153ffc94)**) for the extra SLOAD, and to avoid Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past
### Findings
```
contracts/governance/NounsDAOInterfaces.sol
204:         bool canceled;

206:         bool vetoed;

208:         bool executed;

216:         bool hasVoted;

304:         bool canceled;

306:         bool vetoed;

308:         bool executed;

320:         bool hasVoted;

390:         bool canceled;

392:         bool vetoed;

394:         bool executed;
```

## [G-17] Multiplication/division by 2 should use bit shifting
`<x> * 2` is equivalent to `<x> << 1` and `<x> / 2` is the same as `<x> >> 1`. The `MUL` and `DIV` opcodes cost 5 gas, whereas `SHL` and `SHR` only cost 3 gas
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
951:             uint256 center = upper - (upper - lower) / 2;
```

```
contracts/base/ERC721Checkpointable.sol
184:             uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```

##  [G-18] Use custom errors rather than `require()` strings to save gas
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information, as explained here https://blog.soliditylang.org/2021/04/21/custom-errors/
Custom errors are defined using the `error` statement
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
133:         require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
134:         require(msg.sender == admin, 'NounsDAO::initialize: admin only');
135:         require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136:         require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
137:         require(
138:             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:             'NounsDAO::initialize: invalid voting period'
140:         );
141:         require(
142:             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:             'NounsDAO::initialize: invalid voting delay'
144:         );
145:         require(
146:             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:             'NounsDAO::initialize: invalid proposal threshold bps'
148:         );

197:         require(
198:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:             'NounsDAO::propose: proposer votes below proposal threshold'
200:         );
201:         require(
202:             targets.length == values.length &&
203:                 targets.length == signatures.length &&
204:                 targets.length == calldatas.length,
205:             'NounsDAO::propose: proposal function information arity mismatch'
206:         );
207:         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208:         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213:             require(
214:                 proposersLatestProposalState != ProposalState.Active,
215:                 'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:             );
217:             require(
218:                 proposersLatestProposalState != ProposalState.Pending,
219:                 'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:             );

286:         require(
287:             state(proposalId) == ProposalState.Succeeded,
288:             'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:         );

312:         require(
313:             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314:             'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:         );

324:         require(
325:             state(proposalId) == ProposalState.Queued,
326:             'NounsDAO::execute: proposal can only be executed if it is queued'
327:         );

347:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350:         require(
351:             msg.sender == proposal.proposer ||
352:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353:             'NounsDAO::cancel: proposer above threshold'
354:         );

375:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
376:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
377:         require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:         require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:         require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:         require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
594:         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
623:         require(
624:             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:             'NounsDAO::_setVotingDelay: invalid voting delay'
626:         );

638:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
639:         require(
640:             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:             'NounsDAO::_setVotingPeriod: invalid voting period'
642:         );

655:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
656:         require(
657:             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:             'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:         );

674:         require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
 
677:         require(
678:             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:             'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:         );
682:         require(
683:             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:             'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:         );

702:         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705:         require(
706:             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:             'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:         );
709:         require(
710:             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:             'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:         );

727:         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

1019:         require(n <= type(uint32).max, errorMessage);
```

```
contracts/base/ERC721Checkpointable.sol
140:         require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:         require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:         require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:         require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

254:         require(n < 2**32, errorMessage);

259:         require(n < 2**96, errorMessage);

269:         require(c >= a, errorMessage);

278:         require(b <= a, errorMessage);
```

```
contracts/base/
ERC721Enumerable.sol
62:         require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:         require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

```
contracts/governance/NounsDAOProxy.sol
79:         require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:         require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```

## [G-19] Caching storage variables in memory to save gas
 Caching will replace each Gwarmaccess (100 gas) with a much cheaper stack read.
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
226:         proposalCount++;
227:         Proposal storage newProposal = _proposals[proposalCount];
228:         newProposal.id = proposalCount;
```
`proposalCount` is read three times
no need to `SLOAD` `newProposal.id`  four times
can be :
```diff
- 226:         proposalCount++;
- 227:         Proposal storage newProposal = _proposals[proposalCount];
- 228:         newProposal.id = proposalCount;
+ 226:         uint256 newProposalId = ++proposalCount;
+ 227:         Proposal storage newProposal = _proposals[newProposalId];
+ 228:         newProposal.id = newProposalId;

- 247:         latestProposalIds[newProposal.proposer] = newProposal.id;
+ 247:         latestProposalIds[newProposal.proposer] = newProposalId;

- 251:             newProposal.id,
+ 251:             newProposalId,

- 265:             newProposal.id,
+ 265:             newProposalId,

- 278:         return newProposal.id;
+ 278:         return newProposalId;

```

```
contracts/governance/NounsDAOLogicV2.sol
257:             newProposal.startBlock,
258:             newProposal.endBlock,
	
271:             newProposal.startBlock,
272:             newProposal.endBlock,
273:             newProposal.proposalThreshold,
```

no need to `SLOAD` these values when we have them in `memory`:
```
temp.startBlock;
temp.endBlock;
temp.proposalThreshold;
```

```
contracts/governance/NounsDAOLogicV2.sol
351:             msg.sender == proposal.proposer ||
352:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
```
`proposal.proposer` is read two times

```
contracts/governance/NounsDAOLogicV2.sol
375:         require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
376:         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
```
`vetoer` is read two times

```
contracts/governance/NounsDAOLogicV2.sol
627:         uint256 oldVotingDelay = votingDelay;
628:         votingDelay = newVotingDelay;
629: 
630:         emit VotingDelaySet(oldVotingDelay, votingDelay);
```
`votingDelay` is read two times
can be `emit VotingDelaySet(oldVotingDelay, newVotingDelay);`

```
contracts/governance/NounsDAOLogicV2.sol
643:         uint256 oldVotingPeriod = votingPeriod;
644:         votingPeriod = newVotingPeriod;
645: 
646:         emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
```
`votingPeriod` is read two times
can be `emit VotingDelaySet(oldVotingPeriod, newVotingPeriod);`

```
contracts/governance/NounsDAOLogicV2.sol
661:         uint256 oldProposalThresholdBPS = proposalThresholdBPS;
662:         proposalThresholdBPS = newProposalThresholdBPS;
663: 
664:         emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
```
`proposalThresholdBPS` is read two times
can be `emit VotingDelaySet(oldProposalThresholdBPS, newProposalThresholdBPS);`

```
contracts/governance/NounsDAOLogicV2.sol
819:         require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
820: 
821:         // Save current values for inclusion in log
822:         address oldAdmin = admin;
823:         address oldPendingAdmin = pendingAdmin;
824: 
825:         // Store admin with value pendingAdmin
826:         admin = pendingAdmin;
827: 
828:         // Clear the pending value
829:         pendingAdmin = address(0);
830: 
831:         emit NewAdmin(oldAdmin, admin);
832:         emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
``` 
`pendingAdmin` is read four times
`admin` is read two times

```
contracts/governance/NounsDAOLogicV2.sol
840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
841: 
842:         emit NewVetoer(vetoer, newVetoer);
```
`vetoer` is read two times

```
contracts/governance/NounsDAOLogicV2.sol
867:         if (proposal.creationBlock == 0) {
868:             return proposal.startBlock - votingDelay;
869:         }
870:         return proposal.creationBlock;
```
`proposal.creationBlock` is read two times


```
contracts/governance/NounsDAOLogicV2.sol
879:         if (proposal.totalSupply == 0) {

886:                 proposal.totalSupply,
```
`proposal.totalSupply`  is read two times

```
contracts/governance/NounsDAOLogicV2.sol
929:                     minQuorumVotesBPS: safe16(quorumVotesBPS),
930:                     maxQuorumVotesBPS: safe16(quorumVotesBPS),

942:                     minQuorumVotesBPS: safe16(quorumVotesBPS),
943:                     maxQuorumVotesBPS: safe16(quorumVotesBPS),

```
`quorumVotesBPS`  is read two times 


## [G-20] ++variable costs less gas than variable++, especially when it’s used in for-loops (--variable/variable-- too)
Prefix increments are cheaper than postfix increments.
Saves 6 gas *PER LOOP*
```
contracts/governance/NounsDAOLogicV2.sol
226:         proposalCount++;

292:         for (uint256 i = 0; i < proposal.targets.length; i++) {

330:         for (uint256 i = 0; i < proposal.targets.length; i++) {

357:         for (uint256 i = 0; i < proposal.targets.length; i++) {

382:         for (uint256 i = 0; i < proposal.targets.length; i++) {
```

```
contracts/base/ERC721Checkpointable.sol
141:         require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
```

## [G-21] Unchecking arithmetics operations that can't underflow/overflow
Solidity version 0.8+ comes with implicit overflow and underflow checks on unsigned integers. When an overflow or an underflow isn't possible (as an example, when a comparison is made before the arithmetic operation), some gas can be saved by using an `unchecked` block: <https://docs.soliditylang.org/en/v0.8.10/control-structures.html#checked-or-unchecked-arithmetic>
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
223:         temp.startBlock = block.number + votingDelay;
224:         temp.endBlock = temp.startBlock + votingPeriod;
225: 
226:         proposalCount++;

291:         uint256 eta = block.timestamp + timelock.delay();
292:         for (uint256 i = 0; i < proposal.targets.length; i++) {

330:         for (uint256 i = 0; i < proposal.targets.length; i++) {

357:         for (uint256 i = 0; i < proposal.targets.length; i++) {

382:         for (uint256 i = 0; i < proposal.targets.length; i++) {

449:         } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {

603:             proposal.againstVotes = proposal.againstVotes + votes;

605:             proposal.forVotes = proposal.forVotes + votes;

607:             proposal.abstainVotes = proposal.abstainVotes + votes;

910:         uint256 adjustedQuorumBPS = params.minQuorumVotesBPS + quorumAdjustmentBPS;

198:             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,

352:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,

868:             return proposal.startBlock - votingDelay;

935:         if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {
936:             return quorumParamsCheckpoints[len - 1].params;

949:         uint256 upper = len - 1;

951:             uint256 center = upper - (upper - lower) / 2;

967:         if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
968:             quorumParamsCheckpoints[pos - 1].params = params;

153:         return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;

172:         if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {
173:             return checkpoints[account][nCheckpoints - 1].votes;

182:         uint32 upper = nCheckpoints - 1;

184:             uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

218:                 uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

225:                 uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

243:         if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
244:             checkpoints[delegatee][nCheckpoints - 1].votes = newVotes;
```

