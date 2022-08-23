## [G-01] Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::181 => uint32 lower = 0;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::948 => uint256 lower = 0;
```

## [G-02] Cache Array Length Outside of Loop

Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-03] Use Shift Right/Left instead of Division/Multiplication if possible

A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::184 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::951 => uint256 center = upper - (upper - lower) / 2;
```

## [G-04] Use calldata instead of memory

Use calldata instead of memory for function parameters saves gas if the function argument is only read.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::253 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::258 => function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1018 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
```

## [G-05] Empty blocks should be removed or emit something

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. 

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1030 => receive() external payable {}
```

## [G-06] Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::41 => uint8 public constant decimals = 0;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::48 => uint32 fromBlock;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::152 => uint32 nCheckpoints = numCheckpoints[account];
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::166 => uint32 nCheckpoints = numCheckpoints[account];
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::181 => uint32 lower = 0;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::182 => uint32 upper = nCheckpoints - 1;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::218 => uint8 support;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::322 => uint8 support;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::352 => uint16 minQuorumVotesBPS;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::354 => uint16 maxQuorumVotesBPS;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::357 => uint32 quorumCoefficient;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::363 => uint32 fromBlock;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::687 => uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::714 => uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::730 => uint32 oldQuorumCoefficient = params.quorumCoefficient;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::923 => uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::965 => uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
```

## [G-07] ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, for example when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::141 => require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::216 => proposalCount++;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::226 => proposalCount++;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-08] abi.encode() is less efficient than abi.encodePacked()

use abi.encodePacked() where possible to save gas

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::135 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::137 => bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::302 => !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::480 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::482 => bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::313 => !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::572 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::574 => bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

## [G-09] Use a more recent version of solidity

Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::35 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::28 => pragma solidity ^0.8.0;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::33 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::61 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::53 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::36 => pragma solidity ^0.8.6;
```

## [G-10] Prefix increments cheaper than Postfix increments

++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
Saves 5 gas PER LOOP

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-11] Use bytes32 instead of string

Use bytes32 instead of string to save gas whenever possible. String is a dynamic data structure and therefore is more gas consuming then bytes32.

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::67 => string public constant name = 'Nouns DAO';
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::59 => string public constant name = 'Nouns DAO';
```

## [G-12] Splitting require() statements that use && saves gas

Saves 16 gas per instance.
If you're using the Optimizer at 200, instead of using the && operator in a single require statement to check multiple conditions, multiple require statements with 1 condition per require statement should be used to save gas:

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::617 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::819 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

## [G-13] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public and can save gas by doing so.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::163 => function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::61 => function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::76 => function tokenByIndex(uint256 index) public view virtual override returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::649 => function _burnVetoPower() public {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::660 => function proposalThreshold() public view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::668 => function quorumVotes() public view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::851 => function _burnVetoPower() public {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::862 => function proposalThreshold() public view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1002 => function maxQuorumVotes() public view returns (uint256) {
```

## [G-14] Multiple address mappings can be combined into a single mapping of an address to a struct, where appropriate

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::44 => mapping(address => address) private _delegates;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::53 => mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::56 => mapping(address => uint32) public numCheckpoints;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::67 => mapping(address => uint256) public nonces;
```

```
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::172 => mapping(address => uint256) public latestProposalIds;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::210 => mapping(address => Receipt) receipts;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::272 => mapping(address => uint256) public latestProposalIds;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::310 => mapping(address => Receipt) receipts;
```

## [G-15] Use assembly to check for address(0)

Saves 6 gas per instance if using assembly to check for address(0)

e.g.
```
assembly {
 if iszero(_addr) {
  mstore(0x00, "zero address")
  revert(0x00, 0x20)
 }
}
```

instances:

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::140 => require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::216 => if (srcRep != address(0)) {
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::223 => if (dstRep != address(0)) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::124 => require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::125 => require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::364 => require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::485 => require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::617 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::135 => require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::136 => require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::375 => require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::577 => require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::819 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::80 => require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```

## [G-16] Use selfbalance()

Use selfbalance() instead of address(this).balance when getting your contract's balance of ETH to save gas.

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::788 => uint256 amount = address(this).balance;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::976 => uint256 balance = address(this).balance;
```

## [G-17] internal functions only called once can be inlined to save gas

Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::253 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::258 => function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::282 => function getChainId() internal view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::676 => function getChainIdInternal() internal view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::866 => function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::974 => function _refundGas(uint256 startGas) internal {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1010 => function getChainIdInternal() internal view returns (uint256) {
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::94 => function delegateTo(address callee, bytes memory data) internal {
```