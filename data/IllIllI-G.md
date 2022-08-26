## Summary

### Gas Optimizations
| |Issue|Instances|
|-|:-|:-:|
| [G&#x2011;01] | State checks unnecessarily re-fetch `Proposal`s | 5 |
| [G&#x2011;02] | Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate | 1 |
| [G&#x2011;03] | Structs can be packed into fewer storage slots | 3 |
| [G&#x2011;04] | Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas | 10 |
| [G&#x2011;05] | Using `storage` instead of `memory` for structs/arrays saves gas | 1 |
| [G&#x2011;06] | State variables should be cached in stack variables rather than re-reading them from storage | 11 |
| [G&#x2011;07] | Multiple accesses of a mapping/array should use a local variable cache | 2 |
| [G&#x2011;08] | `internal` functions only called once can be inlined to save gas | 7 |
| [G&#x2011;09] | Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement | 1 |
| [G&#x2011;10] | `<array>.length` should not be looked up in every loop of a `for`-loop | 8 |
| [G&#x2011;11] | `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops | 8 |
| [G&#x2011;12] | `require()`/`revert()` strings longer than 32 bytes cost extra gas | 86 |
| [G&#x2011;13] | Optimize names to save gas | 5 |
| [G&#x2011;14] | Use a more recent version of solidity | 1 |
| [G&#x2011;15] | `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too) | 10 |
| [G&#x2011;16] | Splitting `require()` statements that use `&&` saves gas | 19 |
| [G&#x2011;17] | Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead | 1 |
| [G&#x2011;18] | Using `private` rather than `public` for constants, saves gas | 31 |
| [G&#x2011;19] | Don't compare boolean expressions to boolean literals | 2 |
| [G&#x2011;20] | Division by two should use bit shifting | 2 |
| [G&#x2011;21] | `require()` or `revert()` statements that check input arguments should be at the top of the function | 3 |
| [G&#x2011;22] | Empty blocks should be removed or emit something | 1 |
| [G&#x2011;23] | Use custom errors rather than `revert()`/`require()` strings to save gas | 95 |

Total: 313 instances over 23 issues


## Gas Optimizations

### [G&#x2011;01]  State checks unnecessarily re-fetch `Proposal`s
Every call to `state()` fetches the `Proposal` storage variable, which is fetched again immediately afterwards by the caller. If instead there were a version of `state()` that took in a `Proposal storage` variable, the proposal could be fetched only once, saving the gas of the mapping lookup

*There are 5 instances of this issue:*
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol

347          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
348  
349:         Proposal storage proposal = _proposals[proposalId];

286          require(
287              state(proposalId) == ProposalState.Succeeded,
288              'NounsDAO::queue: proposal can only be queued if it is succeeded'
289          );
290:         Proposal storage proposal = _proposals[proposalId];

324          require(
325              state(proposalId) == ProposalState.Queued,
326              'NounsDAO::execute: proposal can only be executed if it is queued'
327          );
328:         Proposal storage proposal = _proposals[proposalId];

377          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
378  
379:         Proposal storage proposal = _proposals[proposalId];

593          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
594          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
595:         Proposal storage proposal = _proposals[proposalId];

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347-L349

### [G&#x2011;02]  Multiple `address`/ID mappings can be combined into a single `mapping` of an `address`/ID to a `struct`, where appropriate
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (**20000 gas**) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save **~42 gas per access** due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

*There is 1 instance of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

53        mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
54    
55        /// @notice The number of checkpoints for each account
56:       mapping(address => uint32) public numCheckpoints;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L53-L56

### [G&#x2011;03]  Structs can be packed into fewer storage slots
Each slot saved can avoid an extra Gsset (**20000 gas**) for the first setting of the struct. Subsequent reads as well as writes have smaller gas savings

*There are 3 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOInterfaces.sol

/// @audit Variable ordering with 15 slots instead of the current 16:
///           uint256(32):id, uint256(32):proposalThreshold, uint256(32):quorumVotes, uint256(32):eta, address[](32):targets, uint256[](32):values, string[](32):signatures, bytes[](32):calldatas, uint256(32):startBlock, uint256(32):endBlock, uint256(32):forVotes, uint256(32):againstVotes, uint256(32):abstainVotes, mapping(32):receipts, address(20):proposer, bool(1):canceled, bool(1):vetoed, bool(1):executed
174       struct Proposal {
175           /// @notice Unique id for looking up a proposal
176           uint256 id;
177           /// @notice Creator of the proposal
178           address proposer;
179           /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
180           uint256 proposalThreshold;
181           /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
182           uint256 quorumVotes;
183           /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
184           uint256 eta;
185           /// @notice the ordered list of target addresses for calls to be made
186           address[] targets;
187           /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
188           uint256[] values;
189           /// @notice The ordered list of function signatures to be called
190           string[] signatures;
191           /// @notice The ordered list of calldata to be passed to each call
192           bytes[] calldatas;
193           /// @notice The block at which voting begins: holders must delegate their votes prior to this block
194           uint256 startBlock;
195           /// @notice The block at which voting ends: votes must be cast prior to this block
196           uint256 endBlock;
197           /// @notice Current number of votes in favor of this proposal
198           uint256 forVotes;
199           /// @notice Current number of votes in opposition to this proposal
200           uint256 againstVotes;
201           /// @notice Current number of votes for abstaining for this proposal
202           uint256 abstainVotes;
203           /// @notice Flag marking whether the proposal has been canceled
204           bool canceled;
205           /// @notice Flag marking whether the proposal has been vetoed
206           bool vetoed;
207           /// @notice Flag marking whether the proposal has been executed
208           bool executed;
209           /// @notice Receipts of ballots for the entire set of voters
210           mapping(address => Receipt) receipts;
211:      }

/// @audit Variable ordering with 17 slots instead of the current 18:
///           uint256(32):id, uint256(32):proposalThreshold, uint256(32):quorumVotes, uint256(32):eta, address[](32):targets, uint256[](32):values, string[](32):signatures, bytes[](32):calldatas, uint256(32):startBlock, uint256(32):endBlock, uint256(32):forVotes, uint256(32):againstVotes, uint256(32):abstainVotes, mapping(32):receipts, uint256(32):totalSupply, uint256(32):creationBlock, address(20):proposer, bool(1):canceled, bool(1):vetoed, bool(1):executed
274       struct Proposal {
275           /// @notice Unique id for looking up a proposal
276           uint256 id;
277           /// @notice Creator of the proposal
278           address proposer;
279           /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
280           uint256 proposalThreshold;
281           /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
282           uint256 quorumVotes;
283           /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
284           uint256 eta;
285           /// @notice the ordered list of target addresses for calls to be made
286           address[] targets;
287           /// @notice The ordered list of values (i.e. msg.value) to be passed to the calls to be made
288           uint256[] values;
289           /// @notice The ordered list of function signatures to be called
290           string[] signatures;
291           /// @notice The ordered list of calldata to be passed to each call
292           bytes[] calldatas;
293           /// @notice The block at which voting begins: holders must delegate their votes prior to this block
294           uint256 startBlock;
295           /// @notice The block at which voting ends: votes must be cast prior to this block
296           uint256 endBlock;
297           /// @notice Current number of votes in favor of this proposal
298           uint256 forVotes;
299           /// @notice Current number of votes in opposition to this proposal
300           uint256 againstVotes;
301           /// @notice Current number of votes for abstaining for this proposal
302           uint256 abstainVotes;
303           /// @notice Flag marking whether the proposal has been canceled
304           bool canceled;
305           /// @notice Flag marking whether the proposal has been vetoed
306           bool vetoed;
307           /// @notice Flag marking whether the proposal has been executed
308           bool executed;
309           /// @notice Receipts of ballots for the entire set of voters
310           mapping(address => Receipt) receipts;
311           /// @notice The total supply at the time of proposal creation
312           uint256 totalSupply;
313           /// @notice The block at which this proposal was created
314           uint256 creationBlock;
315:      }

/// @audit Variable ordering with 12 slots instead of the current 13:
///           uint256(32):id, uint256(32):proposalThreshold, uint256(32):quorumVotes, uint256(32):eta, uint256(32):startBlock, uint256(32):endBlock, uint256(32):forVotes, uint256(32):againstVotes, uint256(32):abstainVotes, uint256(32):totalSupply, uint256(32):creationBlock, address(20):proposer, bool(1):canceled, bool(1):vetoed, bool(1):executed
368       struct ProposalCondensed {
369           /// @notice Unique id for looking up a proposal
370           uint256 id;
371           /// @notice Creator of the proposal
372           address proposer;
373           /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
374           uint256 proposalThreshold;
375           /// @notice The minimum number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
376           uint256 quorumVotes;
377           /// @notice The timestamp that the proposal will be available for execution, set once the vote succeeds
378           uint256 eta;
379           /// @notice The block at which voting begins: holders must delegate their votes prior to this block
380           uint256 startBlock;
381           /// @notice The block at which voting ends: votes must be cast prior to this block
382           uint256 endBlock;
383           /// @notice Current number of votes in favor of this proposal
384           uint256 forVotes;
385           /// @notice Current number of votes in opposition to this proposal
386           uint256 againstVotes;
387           /// @notice Current number of votes for abstaining for this proposal
388           uint256 abstainVotes;
389           /// @notice Flag marking whether the proposal has been canceled
390           bool canceled;
391           /// @notice Flag marking whether the proposal has been vetoed
392           bool vetoed;
393           /// @notice Flag marking whether the proposal has been executed
394           bool executed;
395           /// @notice The total supply at the time of proposal creation
396           uint256 totalSupply;
397           /// @notice The block at which this proposal was created
398           uint256 creationBlock;
399:      }

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L174-L211

### [G&#x2011;04]  Using `calldata` instead of `memory` for read-only arguments in `external` functions saves gas
When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for-loop to copy each index of the `calldata` to the `memory` index. **Each iteration of this for-loop costs at least 60 gas** (i.e. `60 * <mem_array>.length`). Using `calldata` directly, obliviates the need for such a loop in the contract code and runtime execution. Note that even if an interface defines a function as having `memory` arguments, it's still valid for implementation contracs to use `calldata` arguments instead. 

If the array is passed to an `internal` function which passes the array to another internal function where the array is modified and therefore `memory` is used in the `external` call, it's still more gass-efficient to use `calldata` when the `external` function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one

Note that I've also flagged instances where the function is `public` but can be marked as `external` since it's not called by the contract, and cases where a constructor is involved

*There are 10 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit targets
/// @audit values
/// @audit signatures
/// @audit calldatas
/// @audit description
174       function propose(
175           address[] memory targets,
176           uint256[] memory values,
177           string[] memory signatures,
178           bytes[] memory calldatas,
179           string memory description
180:      ) public returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174-L180

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit targets
/// @audit values
/// @audit signatures
/// @audit calldatas
/// @audit description
184       function propose(
185           address[] memory targets,
186           uint256[] memory values,
187           string[] memory signatures,
188           bytes[] memory calldatas,
189           string memory description
190:      ) public returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L184-L190

### [G&#x2011;05]  Using `storage` instead of `memory` for structs/arrays saves gas
When fetching data from a storage location, assigning the data to a `memory` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (**2100 gas**) for *each* field of the struct/array. If the fields are read from the new memory variable, they incur an additional `MLOAD` rather than a cheap stack read. Instead of declearing the variable with the `memory` keyword, declaring the variable with the `storage` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a `memory` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires `memory`, or if the array/struct is being read from another `memory` array/struct

*There is 1 instance of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV2.sol

952:              DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L952

### [G&#x2011;06]  State variables should be cached in stack variables rather than re-reading them from storage
The instances below point to the second+ access of a state variable within a function. Caching of a state variable replace each Gwarmaccess (**100 gas**) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses.

*There are 11 instances of this issue:*
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol

/// @audit proposal.proposer on line 351
352:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L352

```solidity
File: /contracts/governance/NounsDAOLogicV1.sol

/// @audit proposal.proposer on line 340
341:                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L341

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit vetoer on line 364
365:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

/// @audit vetoer on line 638
640:          emit NewVetoer(vetoer, newVetoer);

/// @audit votingDelay on line 536
538:          emit VotingDelaySet(oldVotingDelay, votingDelay);

/// @audit votingPeriod on line 552
554:          emit VotingPeriodSet(oldVotingPeriod, votingPeriod);

/// @audit proposalThresholdBPS on line 570
572:          emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);

/// @audit quorumVotesBPS on line 587
589:          emit QuorumVotesBPSSet(oldQuorumVotesBPS, quorumVotesBPS);

/// @audit proposalCount on line 216
217:          Proposal storage newProposal = proposals[proposalCount];

/// @audit proposalCount on line 217
219:          newProposal.id = proposalCount;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L365

```solidity
File: contracts/governance/NounsDAOProxy.sol

/// @audit implementation on line 83
85:           emit NewImplementation(oldImplementation, implementation);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L85

### [G&#x2011;07]  Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

*There are 2 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit quorumParamsCheckpoints[<etc>] on line 935
936:              return quorumParamsCheckpoints[len - 1].params;

/// @audit quorumParamsCheckpoints[<etc>] on line 967
968:              quorumParamsCheckpoints[pos - 1].params = params;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L936

### [G&#x2011;08]  `internal` functions only called once can be inlined to save gas
Not inlining costs **20 to 40 gas** because of two extra `JUMP` instructions and additional stack operations needed for function calls.

*There are 7 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

294       function queueOrRevertInternal(
295           address target,
296           uint256 value,
297           string memory signature,
298           bytes memory data,
299:          uint256 eta

676:      function getChainIdInternal() internal view returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L294-L299

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

305       function queueOrRevertInternal(
306           address target,
307           uint256 value,
308           string memory signature,
309           bytes memory data,
310:          uint256 eta

866:      function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {

974:      function _refundGas(uint256 startGas) internal {

1010:     function getChainIdInternal() internal view returns (uint256) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L305-L310

```solidity
File: contracts/governance/NounsDAOProxy.sol

94:       function delegateTo(address callee, bytes memory data) internal {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L94

### [G&#x2011;09]  Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

*There is 1 instance of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

/// @audit require() on line 278
279:          return a - b;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L279

### [G&#x2011;10]  `<array>.length` should not be looked up in every loop of a `for`-loop
The overheads outlined below are _PER LOOP_, excluding the first loop
* storage arrays incur a Gwarmaccess (**100 gas**)
* memory arrays use `MLOAD` (**3 gas**)
* calldata arrays use `CALLDATALOAD` (**3 gas**)

Caching the length changes each of these to a `DUP<N>` (**3 gas**), and gets rid of the extra `DUP<N>` needed to store the stack offset

*There are 8 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

281:          for (uint256 i = 0; i < proposal.targets.length; i++) {

319:          for (uint256 i = 0; i < proposal.targets.length; i++) {

346:          for (uint256 i = 0; i < proposal.targets.length; i++) {

371:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

292:          for (uint256 i = 0; i < proposal.targets.length; i++) {

330:          for (uint256 i = 0; i < proposal.targets.length; i++) {

357:          for (uint256 i = 0; i < proposal.targets.length; i++) {

382:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

### [G&#x2011;11]  `++i`/`i++` should be `unchecked{++i}`/`unchecked{i++}` when it is not possible for them to overflow, as is the case when used in `for`- and `while`-loops
The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves **30-40 gas [per loop](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)**

*There are 8 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

281:          for (uint256 i = 0; i < proposal.targets.length; i++) {

319:          for (uint256 i = 0; i < proposal.targets.length; i++) {

346:          for (uint256 i = 0; i < proposal.targets.length; i++) {

371:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

292:          for (uint256 i = 0; i < proposal.targets.length; i++) {

330:          for (uint256 i = 0; i < proposal.targets.length; i++) {

357:          for (uint256 i = 0; i < proposal.targets.length; i++) {

382:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

### [G&#x2011;12]  `require()`/`revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**

*There are 86 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

140:          require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

141:          require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

142:          require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:          require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140

```solidity
File: contracts/base/ERC721Enumerable.sol

62:           require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:           require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L62

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

122:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

124:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

125:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

126           require(
127               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128               'NounsDAO::initialize: invalid voting period'
129:          );

130           require(
131               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132               'NounsDAO::initialize: invalid voting delay'
133:          );

134           require(
135               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136               'NounsDAO::initialize: invalid proposal threshold'
137:          );

138           require(
139               quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140               'NounsDAO::initialize: invalid proposal threshold'
141:          );

187           require(
188               nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189               'NounsDAO::propose: proposer votes below proposal threshold'
190:          );

191           require(
192               targets.length == values.length &&
193                   targets.length == signatures.length &&
194                   targets.length == calldatas.length,
195               'NounsDAO::propose: proposal function information arity mismatch'
196:          );

197:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');

198:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

203               require(
204                   proposersLatestProposalState != ProposalState.Active,
205                   'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
206:              );

207               require(
208                   proposersLatestProposalState != ProposalState.Pending,
209                   'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
210:              );

275           require(
276               state(proposalId) == ProposalState.Succeeded,
277               'NounsDAO::queue: proposal can only be queued if it is succeeded'
278:          );

301           require(
302               !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
303               'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
304:          );

313           require(
314               state(proposalId) == ProposalState.Queued,
315               'NounsDAO::execute: proposal can only be executed if it is queued'
316:          );

336:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

339           require(
340               msg.sender == proposal.proposer ||
341                   nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
342               'NounsDAO::cancel: proposer above threshold'
343:          );

364:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

366:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

422:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

485:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

501:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

502:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

505:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

530:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

531           require(
532               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533               'NounsDAO::_setVotingDelay: invalid voting delay'
534:          );

546:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

547           require(
548               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549               'NounsDAO::_setVotingPeriod: invalid voting period'
550:          );

563:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

564           require(
565               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:          );

581:          require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

582           require(
583               newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:          );

599:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

638:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

651:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

133:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

135:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

136:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

137           require(
138               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139               'NounsDAO::initialize: invalid voting period'
140:          );

141           require(
142               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143               'NounsDAO::initialize: invalid voting delay'
144:          );

145           require(
146               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147               'NounsDAO::initialize: invalid proposal threshold bps'
148:          );

197           require(
198               nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199               'NounsDAO::propose: proposer votes below proposal threshold'
200:          );

201           require(
202               targets.length == values.length &&
203                   targets.length == signatures.length &&
204                   targets.length == calldatas.length,
205               'NounsDAO::propose: proposal function information arity mismatch'
206:          );

207:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');

208:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213               require(
214                   proposersLatestProposalState != ProposalState.Active,
215                   'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:              );

217               require(
218                   proposersLatestProposalState != ProposalState.Pending,
219                   'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:              );

286           require(
287               state(proposalId) == ProposalState.Succeeded,
288               'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:          );

312           require(
313               !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314               'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:          );

324           require(
325               state(proposalId) == ProposalState.Queued,
326               'NounsDAO::execute: proposal can only be executed if it is queued'
327:          );

347:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350           require(
351               msg.sender == proposal.proposer ||
352                   nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353               'NounsDAO::cancel: proposer above threshold'
354:          );

375:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

377:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

594:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

623           require(
624               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625               'NounsDAO::_setVotingDelay: invalid voting delay'
626:          );

638:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

639           require(
640               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641               'NounsDAO::_setVotingPeriod: invalid voting period'
642:          );

655:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

656           require(
657               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659               'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:          );

674:          require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

677           require(
678               newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679                   newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680               'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:          );

682           require(
683               newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684               'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:          );

702:          require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705           require(
706               newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707               'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:          );

709           require(
710               params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711               'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:          );

727:          require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133

```solidity
File: contracts/governance/NounsDAOProxy.sol

79:           require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

80:           require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79

### [G&#x2011;13]  Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. See [this](https://gist.github.com/IllIllI000/a5d8b486a8259f9f77891a919febd1a9) link for an example of how it works. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save **128 gas** each during deployment, and renaming functions to have lower method IDs will save **22 gas** per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92)

*There are 5 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

/// @audit votesToDelegate()
39:   abstract contract ERC721Checkpointable is ERC721Enumerable {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L39

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

/// @audit delay(), GRACE_PERIOD(), acceptAdmin(), queuedTransactions(), queueTransaction(), cancelTransaction(), executeTransaction()
402:  interface INounsDAOExecutor {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L402

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit initialize(), propose(), queue(), execute(), cancel(), veto(), getActions(), getReceipt(), state(), castVote(), castVoteWithReason(), castVoteBySig(), _setVotingDelay(), _setVotingPeriod(), _setProposalThresholdBPS(), _setQuorumVotesBPS(), _setVetoer(), _burnVetoPower(), proposalThreshold(), quorumVotes()
65:   contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L65

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit initialize(), propose(), queue(), execute(), cancel(), veto(), getActions(), getReceipt(), state(), proposals(), castVote(), castRefundableVote(), castRefundableVoteWithReason(), castVoteWithReason(), castVoteBySig(), _setVotingDelay(), _setVotingPeriod(), _setProposalThresholdBPS(), _setMinQuorumVotesBPS(), _setMaxQuorumVotesBPS(), _setQuorumCoefficient(), _setDynamicQuorumParams(), _withdraw(), _setVetoer(), _burnVetoPower(), proposalThreshold(), quorumVotes(), dynamicQuorumVotes(), getDynamicQuorumParamsAt(), minQuorumVotes(), maxQuorumVotes()
57:   contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L57

```solidity
File: contracts/governance/NounsDAOProxy.sol

/// @audit _setImplementation()
40:   contract NounsDAOProxy is NounsDAOProxyStorage, NounsDAOEvents {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L40

### [G&#x2011;14]  Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

*There is 1 instance of this issue:*
```solidity
File: contracts/base/ERC721Enumerable.sol

28:   pragma solidity ^0.8.0;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L28

### [G&#x2011;15]  `++i` costs less gas than `i++`, especially when it's used in `for`-loops (`--i`/`i--` too)
Saves **5 gas per loop**

*There are 10 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

216:          proposalCount++;

281:          for (uint256 i = 0; i < proposal.targets.length; i++) {

319:          for (uint256 i = 0; i < proposal.targets.length; i++) {

346:          for (uint256 i = 0; i < proposal.targets.length; i++) {

371:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L216

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

226:          proposalCount++;

292:          for (uint256 i = 0; i < proposal.targets.length; i++) {

330:          for (uint256 i = 0; i < proposal.targets.length; i++) {

357:          for (uint256 i = 0; i < proposal.targets.length; i++) {

382:          for (uint256 i = 0; i < proposal.targets.length; i++) {

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226

### [G&#x2011;16]  Splitting `require()` statements that use `&&` saves gas
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper by **3 gas**

*There are 19 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

126           require(
127               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128               'NounsDAO::initialize: invalid voting period'
129:          );

130           require(
131               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132               'NounsDAO::initialize: invalid voting delay'
133:          );

134           require(
135               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136               'NounsDAO::initialize: invalid proposal threshold'
137:          );

138           require(
139               quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140               'NounsDAO::initialize: invalid proposal threshold'
141:          );

191           require(
192               targets.length == values.length &&
193                   targets.length == signatures.length &&
194                   targets.length == calldatas.length,
195               'NounsDAO::propose: proposal function information arity mismatch'
196:          );

531           require(
532               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533               'NounsDAO::_setVotingDelay: invalid voting delay'
534:          );

547           require(
548               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549               'NounsDAO::_setVotingPeriod: invalid voting period'
550:          );

564           require(
565               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:          );

582           require(
583               newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:          );

617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L126-L129

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

137           require(
138               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139               'NounsDAO::initialize: invalid voting period'
140:          );

141           require(
142               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143               'NounsDAO::initialize: invalid voting delay'
144:          );

145           require(
146               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147               'NounsDAO::initialize: invalid proposal threshold bps'
148:          );

201           require(
202               targets.length == values.length &&
203                   targets.length == signatures.length &&
204                   targets.length == calldatas.length,
205               'NounsDAO::propose: proposal function information arity mismatch'
206:          );

623           require(
624               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625               'NounsDAO::_setVotingDelay: invalid voting delay'
626:          );

639           require(
640               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641               'NounsDAO::_setVotingPeriod: invalid voting period'
642:          );

656           require(
657               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659               'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:          );

677           require(
678               newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679                   newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680               'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:          );

819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L137-L140

### [G&#x2011;17]  Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
> When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html
Each operation involving a `uint8` costs an extra [**22-28 gas**](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed

*There is 1 instance of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

/// @audit uint32 upper
191:                  upper = center - 1;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L191

### [G&#x2011;18]  Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that [returns a tuple](https://github.com/code-423n4/2022-08-frax/blob/90f55a9ce4e25bceed3a74290b854341d8de6afa/src/contracts/FraxlendPair.sol#L156-L178) of the values of all currently-public constants. Saves **3406-3606 gas** in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

*There are 31 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

41:       uint8 public constant decimals = 0;

59        bytes32 public constant DOMAIN_TYPEHASH =
60:           keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

63        bytes32 public constant DELEGATION_TYPEHASH =
64:           keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L41

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

67:       string public constant name = 'Nouns DAO';

70:       uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

73:       uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

76:       uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

79:       uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

82:       uint256 public constant MIN_VOTING_DELAY = 1;

85:       uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

88:       uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%

91:       uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

94:       uint256 public constant proposalMaxOperations = 10; // 10 actions

97        bytes32 public constant DOMAIN_TYPEHASH =
98:           keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

101:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L67

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

59:       string public constant name = 'Nouns DAO';

62:       uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

65:       uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

68:       uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

71:       uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

74:       uint256 public constant MIN_VOTING_DELAY = 1;

77:       uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

80:       uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

83:       uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

86:       uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

89:       uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

92:       uint256 public constant proposalMaxOperations = 10; // 10 actions

95:       uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

98:       uint256 public constant REFUND_BASE_GAS = 36000;

101       bytes32 public constant DOMAIN_TYPEHASH =
102:          keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

105:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L59

### [G&#x2011;19]  Don't compare boolean expressions to boolean literals
`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

*There are 2 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

505:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

597:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597

### [G&#x2011;20]  Division by two should use bit shifting
`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both, the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/IllIllI000/ec0e4e6c4f52a6bca158f137a3afd4ff) due to `JUMP`s to and from a compiler utility function that introduces checks which can be avoided by using `unchecked {}` around the division by two

*There are 2 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

184:              uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L184

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

951:              uint256 center = upper - (upper - lower) / 2;

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L951

### [G&#x2011;21]  `require()` or `revert()` statements that check input arguments should be at the top of the function
Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (**2100 gas***) in a function that may ultimately revert in the unhappy case.

*There are 3 instances of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV1.sol

/// @audit expensive op on line 501
502:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L502

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

/// @audit expensive op on line 593
594:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

/// @audit expensive op on line 703
705           require(
706               newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707               'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:          );

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L594

### [G&#x2011;22]  Empty blocks should be removed or emit something 
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be `abstract` and the function signatures be added without any default implementation. If the block is an empty `if`-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`). Empty `receive()`/`fallback() payable` functions that are not used, can be removed to save deployment gas.

*There is 1 instance of this issue:*
```solidity
File: contracts/governance/NounsDAOLogicV2.sol

1030:     receive() external payable {}

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1030

### [G&#x2011;23]  Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save [**~50 gas**](https://gist.github.com/IllIllI000/ad1bd0d29a0101b25e57c293b4b0c746) each time they're hit by [avoiding having to allocate and store the revert string](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth). Not defining the strings also save deployment gas

*There are 95 instances of this issue:*
```solidity
File: contracts/base/ERC721Checkpointable.sol

140:          require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

141:          require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

142:          require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:          require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

254:          require(n < 2**32, errorMessage);

259:          require(n < 2**96, errorMessage);

269:          require(c >= a, errorMessage);

278:          require(b <= a, errorMessage);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140

```solidity
File: contracts/base/ERC721Enumerable.sol

62:           require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:           require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L62

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

122:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

123:          require(msg.sender == admin, 'NounsDAO::initialize: admin only');

124:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

125:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

126           require(
127               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128               'NounsDAO::initialize: invalid voting period'
129:          );

130           require(
131               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132               'NounsDAO::initialize: invalid voting delay'
133:          );

134           require(
135               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136               'NounsDAO::initialize: invalid proposal threshold'
137:          );

138           require(
139               quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140               'NounsDAO::initialize: invalid proposal threshold'
141:          );

187           require(
188               nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189               'NounsDAO::propose: proposer votes below proposal threshold'
190:          );

191           require(
192               targets.length == values.length &&
193                   targets.length == signatures.length &&
194                   targets.length == calldatas.length,
195               'NounsDAO::propose: proposal function information arity mismatch'
196:          );

197:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');

198:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

203               require(
204                   proposersLatestProposalState != ProposalState.Active,
205                   'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
206:              );

207               require(
208                   proposersLatestProposalState != ProposalState.Pending,
209                   'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
210:              );

275           require(
276               state(proposalId) == ProposalState.Succeeded,
277               'NounsDAO::queue: proposal can only be queued if it is succeeded'
278:          );

301           require(
302               !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
303               'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
304:          );

313           require(
314               state(proposalId) == ProposalState.Queued,
315               'NounsDAO::execute: proposal can only be executed if it is queued'
316:          );

336:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

339           require(
340               msg.sender == proposal.proposer ||
341                   nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
342               'NounsDAO::cancel: proposer above threshold'
343:          );

364:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

365:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

366:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

422:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

485:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

501:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

502:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

505:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

530:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

531           require(
532               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533               'NounsDAO::_setVotingDelay: invalid voting delay'
534:          );

546:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

547           require(
548               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549               'NounsDAO::_setVotingPeriod: invalid voting period'
550:          );

563:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

564           require(
565               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:          );

581:          require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

582           require(
583               newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584               'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:          );

599:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

638:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

651:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

133:          require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

134:          require(msg.sender == admin, 'NounsDAO::initialize: admin only');

135:          require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

136:          require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

137           require(
138               votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139               'NounsDAO::initialize: invalid voting period'
140:          );

141           require(
142               votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143               'NounsDAO::initialize: invalid voting delay'
144:          );

145           require(
146               proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147               'NounsDAO::initialize: invalid proposal threshold bps'
148:          );

197           require(
198               nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199               'NounsDAO::propose: proposer votes below proposal threshold'
200:          );

201           require(
202               targets.length == values.length &&
203                   targets.length == signatures.length &&
204                   targets.length == calldatas.length,
205               'NounsDAO::propose: proposal function information arity mismatch'
206:          );

207:          require(targets.length != 0, 'NounsDAO::propose: must provide actions');

208:          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213               require(
214                   proposersLatestProposalState != ProposalState.Active,
215                   'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:              );

217               require(
218                   proposersLatestProposalState != ProposalState.Pending,
219                   'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:              );

286           require(
287               state(proposalId) == ProposalState.Succeeded,
288               'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:          );

312           require(
313               !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314               'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:          );

324           require(
325               state(proposalId) == ProposalState.Queued,
326               'NounsDAO::execute: proposal can only be executed if it is queued'
327:          );

347:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350           require(
351               msg.sender == proposal.proposer ||
352                   nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353               'NounsDAO::cancel: proposer above threshold'
354:          );

375:          require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

376:          require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

377:          require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:          require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:          require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

594:          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:          require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

623           require(
624               newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625               'NounsDAO::_setVotingDelay: invalid voting delay'
626:          );

638:          require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

639           require(
640               newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641               'NounsDAO::_setVotingPeriod: invalid voting period'
642:          );

655:          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

656           require(
657               newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658                   newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659               'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:          );

674:          require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

677           require(
678               newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679                   newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680               'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:          );

682           require(
683               newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684               'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:          );

702:          require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705           require(
706               newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707               'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:          );

709           require(
710               params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711               'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:          );

727:          require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:          require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:          require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

1019:         require(n <= type(uint32).max, errorMessage);

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133

```solidity
File: contracts/governance/NounsDAOProxy.sol

79:           require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

80:           require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79

