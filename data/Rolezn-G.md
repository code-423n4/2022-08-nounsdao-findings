## (1) Abi.encode() Is Less Efficient Than Abi.encodepacked()

Severity: Gas Optimizations

## Proof Of Concept


	abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L135

	bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L137

	!timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L302

	abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L480

	bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L482

	!timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L313

	abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L572

	bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L574





## (2) <Array>.length Should Not Be Looked Up In Every Loop Of A For-loop

Severity: Gas Optimizations

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset

## Proof Of Concept

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382






## (3) Using Calldata Instead Of Memory For Read-only Arguments In External Functions Saves Gas

Severity: Gas Optimizations

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one


## Proof Of Concept


	function propose(
		address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    )
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L174

	function getActions(uint256 proposalId)
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L392

	function propose(
		address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    )
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L184

	function getActions(uint256 proposalId)
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L403






## (4) Don’t Compare Boolean Expressions To Boolean Literals

Severity: Gas Optimizations

For cases of: if (<x> == true), use if (<x>) instead
For cases of: if (<x> == false), use if (!<x>) instead

## Proof Of Concept

	require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L505

	require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L597





## (5) Empty Blocks Should Be Removed Or Emit Something

Severity: Gas Optimizations

The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting. If the contract is meant to be extended, the contract should be abstract and the function signatures be added without any default implementation. If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions, the else-if/else conditions should be nested under the negation of the if-statement, because they involve different classes of checks, which may lead to the introduction of errors when the code is later modified (if(x){}else if(y){...}else{...} => if(!x){if(y){...}else{...}})

## Proof Of Concept

	receive() external payable {}
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1030






## (6) ++i Costs Less Gas Than i++, Especially When It’s Used In For-loops (--i/i-- Too)

Severity: Gas Optimizations

Saves 6 gas per loop

## Proof Of Concept


	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382



## Recommended Mitigation Steps

For example, use ++i instead of i++



## (7) It Costs More Gas To Initialize Variables To Zero Than To Let The Default Of Zero Be Applied

Severity: Gas Optimizations

## Proof Of Concept


	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382






## (8) Multiple Address Mappings Can Be Combined Into A Single Mapping Of An Address To A Struct, Where Appropriate

Severity: Gas Optimizations

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot.

## Proof Of Concept


	mapping(address => address) private _delegates;
	mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
	mapping(address => uint32) public numCheckpoints;
	mapping(address => uint256) public nonces;

https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L44

	mapping(address => uint256) public latestProposalIds;
	mapping(address => Receipt) receipts;

https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L172






## (9) Multiplication/division By Two Should Use Bit Shifting

Severity: Gas Optimizations

<x> * 2 is equivalent to <x> << 1 and <x> / 2 is the same as <x> >> 1. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas

## Proof Of Concept


	uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L184

	uint256 center = upper - (upper - lower) / 2;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L951





## (10) Using Private Rather Than Public For Constants, Saves Gas

Severity: Gas Optimizations

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

## Proof Of Concept



	Found 3 usage of public constants
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L41
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L59-L64


	Found 12 usage of public constants
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L67-L101


	Found 16 usage of public constants 
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L59-L105



## Recommended Mitigation Steps

Set variable to private.



## (11) Public Functions To External

Severity: Gas Optimizations

The following functions could be set external to save gas and improve code quality.
External call cost is less expensive than of public functions.

## Proof Of Concept


	function votesToDelegate(address delegator) public view returns (uint96) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L79

	function delegates(address delegator) public view returns (address) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L88

	function delegate(address delegatee) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L112

	function delegateBySig(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L126

	function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L163

	function supportsInterface(bytes4 interfaceId) public view virtual override(IERC165, ERC721) returns (bool) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L54

	function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L61

	function totalSupply() public view virtual override returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L69

	function tokenByIndex(uint256 index) public view virtual override returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L76

	function initialize(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L113

	function propose(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L174

	function state(uint256 proposalId) public view returns (ProposalState) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L421

	function _setVetoer(address newVetoer) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L637

	function _burnVetoPower() public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L649

	function proposalThreshold() public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L660

	function quorumVotes() public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L668

	function initialize(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L124

	function propose(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L184

	function state(uint256 proposalId) public view returns (ProposalState) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L432

	function _setDynamicQuorumParams(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L748

	function _setVetoer(address newVetoer) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L839

	function _burnVetoPower() public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L851

	function proposalThreshold() public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L862

	function quorumVotes(uint256 proposalId) public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L877

	function dynamicQuorumVotes(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L903

	function getDynamicQuorumParamsAt(uint256 blockNumber_) public view returns (DynamicQuorumParams memory) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L922

	function minQuorumVotes() public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L995

	function maxQuorumVotes() public view returns (uint256) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1002

	function _setImplementation(address implementation_) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L78






## (12) require()/revert() Strings Longer Than 32 Bytes Cost Extra Gas

Severity: Gas Optimizations

## Proof Of Concept


	require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L140

	require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L141

	require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L142

	require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L164

	require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L62

	require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L77

	require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L122

	require(msg.sender == admin, 'NounsDAO::initialize: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L123

	require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L124

	require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L125

	require(targets.length != 0, 'NounsDAO::propose: must provide actions');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L197

	require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L198

	require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L336

	require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L364

	require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L366

	require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L422

	require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L485

	require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L501

	require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L502

	require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L505

	require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L530

	require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L546

	require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L563

	require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L581

	require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L599

	require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L617

	require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L638

	require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L651

	require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L133

	require(msg.sender == admin, 'NounsDAO::initialize: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L134

	require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L135

	require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L136

	require(targets.length != 0, 'NounsDAO::propose: must provide actions');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L207

	require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L208

	require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L347

	require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L375

	require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L377

	require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L433

	require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L577

	require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L593

	require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L594

	require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L597

	require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L622

	require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L638

	require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L655

	require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L674

	require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L702

	require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L727

	require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L801

	require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L819

	require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L840

	require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L853

	require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L79

	require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L80





## (13) Splitting Require() Statements That Use && Saves Gas

Severity: Gas Optimizations

See https://github.com/code-423n4/2022-01-xdefi-findings/issues/128 which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

## Proof Of Concept

	require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L617

	require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L819





## (14) Help The Optimizer By Saving A Storage Variable’s Reference Instead Of Repeatedly Fetching It

Severity: Gas Optimizations

To help the optimizer, declare a storage type variable and use it instead of repeatedly fetching the reference in a map or an array.
The effect can be quite significant.
As an example, instead of repeatedly calling someMap[someIndex], save its reference like this: SomeStruct storage someStruct = someMap[someIndex] and use it.

## Proof Of Concept


	address current = _delegates[delegator];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L89

	require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L141

	uint32 nCheckpoints = numCheckpoints[account];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L152

	return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L153

	uint32 nCheckpoints = numCheckpoints[account];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L152

	return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L153

	uint32 nCheckpoints = numCheckpoints[account];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L166

	if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L172

	return checkpoints[account][nCheckpoints - 1].votes;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L173

	if (checkpoints[account][0].fromBlock > blockNumber) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L177

	Checkpoint memory cp = checkpoints[account][center];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L185

	return checkpoints[account][lower].votes;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L194

	_delegates[delegator] = delegatee;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L201

	uint32 srcRepNum = numCheckpoints[srcRep];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L217

	uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L218

	uint32 dstRepNum = numCheckpoints[dstRep];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L224

	uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L225

	if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L243

	checkpoints[delegatee][nCheckpoints - 1].votes = newVotes;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L244

	checkpoints[delegatee][nCheckpoints] = Checkpoint(blockNumber, newVotes);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L246

	numCheckpoints[delegatee] = nCheckpoints + 1;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L247

	return _ownedTokens[owner][index];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L63

	return _allTokens[index];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L78

	_ownedTokens[to][length] = tokenId;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L122

	_ownedTokensIndex[tokenId] = length;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L123

	_allTokensIndex[tokenId] = _allTokens.length;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L131

	uint256 tokenIndex = _ownedTokensIndex[tokenId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L148

	uint256 lastTokenId = _ownedTokens[from][lastTokenIndex];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L152

	_ownedTokens[from][tokenIndex] = lastTokenId; // Move the last token to the slot of the to-delete token
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L154

	_ownedTokensIndex[lastTokenId] = tokenIndex; // Update the moved token's index
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L155

	delete _ownedTokensIndex[tokenId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L159

	delete _ownedTokens[from][lastTokenIndex];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L160

	uint256 tokenIndex = _allTokensIndex[tokenId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L173

	uint256 lastTokenId = _allTokens[lastTokenIndex];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L178

	_allTokens[tokenIndex] = lastTokenId; // Move the last token to the slot of the to-delete token
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L180

	_allTokensIndex[lastTokenId] = tokenIndex; // Update the moved token's index
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L181

	delete _allTokensIndex[tokenId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L184

	Proposal storage newProposal = proposals[proposalCount];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L217

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L279

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L317

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L338

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L368

	Proposal storage p = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L402

	return proposals[proposalId].receipts[voter];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L413

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L423

	Proposal storage proposal = proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L503

	Receipt storage receipt = proposal.receipts[voter];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L504

	Proposal storage newProposal = _proposals[proposalCount];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L227

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L290

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L328

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L349

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L379

	Proposal storage p = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L413

	return _proposals[proposalId].receipts[voter];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L424

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L434

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L463

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L595

	Receipt storage receipt = proposal.receipts[voter];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L596

	Proposal storage proposal = _proposals[proposalId];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L878

	DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L952

	return quorumParamsCheckpoints[lower].params;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L961







## (15) use of non-uint256 state variable is less efficient than uint256

Severity: Gas Optimizations

Lower than uint256 size storage variables are less gas efficient.
Using uint64 does not give any efficiency, actually, it is the opposite as EVM operates on default of 256-bit values so uint64 is more expensive in this case as it needs a conversion. It only gives improvements in cases where you can pack variables together, e.g. structs.

## Proof Of Concept


	uint8 public constant decimals = 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L41

	uint32 fromBlock;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L48

	uint96 votes;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L49

	uint32 nCheckpoints = numCheckpoints[account];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L152

	uint32 lower = 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L181

	uint32 upper = nCheckpoints - 1;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L182

	uint32 center = upper - (upper - lower) / 2;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L184

	uint96 amount = votesToDelegate(delegator);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L205

	uint32 srcRepNum = numCheckpoints[srcRep];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L217

	uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L218

	uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L219

	uint32 dstRepNum = numCheckpoints[dstRep];
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L224

	uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L225

	uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L226

	uint96 c = a + b;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L268

	uint8 support;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L218

	uint96 votes;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L220

	uint16 minQuorumVotesBPS;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L352

	uint16 maxQuorumVotesBPS;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L354

	uint32 quorumCoefficient;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L357

	uint32 fromBlock;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L363

	uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L508

	uint96 votes = castVoteInternal(msg.sender, proposalId, support);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L539

	uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L600

	uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L687

	uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L714

	uint32 oldQuorumCoefficient = params.quorumCoefficient;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L730

	uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L923

	uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L965






## (16) ++i/i++ Should Be Unchecked{++i}/unchecked{i++} When It Is Not Possible For Them To Overflow, As Is The Case When Used In For- And While-loops

Severity: Gas Optimizations

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas PER LOOP

## Proof Of Concept


	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357

	for (uint256 i = 0; i < proposal.targets.length; i++) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382





## (17) Use assembly to check for address(0)

Severity: Gas Optimizations

Save 6 gas per instance if using assembly to check for address(0)

e.g.
assembly {
	if iszero(_addr) {
		mstore(0x00, "AddressZero")
		revert(0x00, 0x20)
	}
}

## Proof Of Concept


    function delegate(address delegatee) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L112

    function _setImplementation(address implementation_) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L78



