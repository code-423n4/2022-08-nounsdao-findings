### [G-01] ```abi.encode()``` is less efficient than ```abi.encodePacked()```


#### Impact
Consider using ```abi.encodePacked()``` instead for efficieny.


#### Findings:
```
contracts/base/ERC721Checkpointable.sol:L135            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))

contracts/base/ERC721Checkpointable.sol:L137        bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));

contracts/governance/NounsDAOLogicV2.sol:L313            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

contracts/governance/NounsDAOLogicV2.sol:L572            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

contracts/governance/NounsDAOLogicV2.sol:L574        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

contracts/governance/NounsDAOExecutor.sol:L120        bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

contracts/governance/NounsDAOExecutor.sol:L136        bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

contracts/governance/NounsDAOExecutor.sol:L151        bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

contracts/governance/NounsDAOLogicV1.sol:L302            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

contracts/governance/NounsDAOLogicV1.sol:L480            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

contracts/governance/NounsDAOLogicV1.sol:L482        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

```
### [G-02] Using bools for storage incurs overhead.


#### Impact
 
```
    // Booleans are more expensive than uint256 or any type that takes up a full
    // word because each write operation emits an extra SLOAD to first read the
    // slot's contents, replace the bits taken up by the boolean, and then write
    // back. This is the compiler's defense against contract upgrades and
    // pointer aliasing, and it cannot be disabled.
```
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27 Use ```uint256(1)``` and ```uint256(2)``` for true/false to avoid a Gwarmaccess ([100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)), and to avoid Gsset (20000 gas) when changing from ‘false’ to ‘true’, after having been ‘true’ in the past


#### Findings:
```
contracts/governance/NounsDAOExecutor.sol:L70    mapping(bytes32 => bool) public queuedTransactions;

```
###  [G-03] Cache Array Length Outside of Loop


#### Impact
Caching the array length outside a loop saves reading it on each iteration, as long as the array's length is not changed during the loop.


#### Findings:
```
contracts/governance/NounsDAOLogicV2.sol:L292        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L330        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L357        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L382        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L281        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L319        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L346        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L371        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

### [G-04] Use custom errors rather than ```revert()```/```require()``` string to save gas


#### Impact
Custom errors are available from solidity version 0.8.4, it saves around 50 gas each time they are hit by avoiding having to allocate and store the revert string.


#### Findings:
```
contracts/base/ERC721Enumerable.sol:L62        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

contracts/base/ERC721Enumerable.sol:L77        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

contracts/base/ERC721Checkpointable.sol:L140        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

contracts/base/ERC721Checkpointable.sol:L141        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

contracts/base/ERC721Checkpointable.sol:L142        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

contracts/base/ERC721Checkpointable.sol:L164        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

contracts/governance/NounsDAOProxy.sol:L79        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

contracts/governance/NounsDAOProxy.sol:L80        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

contracts/governance/NounsDAOLogicV2.sol:L133        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

contracts/governance/NounsDAOLogicV2.sol:L134        require(msg.sender == admin, 'NounsDAO::initialize: admin only');

contracts/governance/NounsDAOLogicV2.sol:L135        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

contracts/governance/NounsDAOLogicV2.sol:L136        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

contracts/governance/NounsDAOLogicV2.sol:L207        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

contracts/governance/NounsDAOLogicV2.sol:L208        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

contracts/governance/NounsDAOLogicV2.sol:L347        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

contracts/governance/NounsDAOLogicV2.sol:L375        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

contracts/governance/NounsDAOLogicV2.sol:L376        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

contracts/governance/NounsDAOLogicV2.sol:L377        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

contracts/governance/NounsDAOLogicV2.sol:L433        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

contracts/governance/NounsDAOLogicV2.sol:L577        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

contracts/governance/NounsDAOLogicV2.sol:L593        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

contracts/governance/NounsDAOLogicV2.sol:L594        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

contracts/governance/NounsDAOLogicV2.sol:L597        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

contracts/governance/NounsDAOLogicV2.sol:L622        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

contracts/governance/NounsDAOLogicV2.sol:L638        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

contracts/governance/NounsDAOLogicV2.sol:L655        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L674        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L702        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L727        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

contracts/governance/NounsDAOLogicV2.sol:L801        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

contracts/governance/NounsDAOLogicV2.sol:L819        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

contracts/governance/NounsDAOLogicV2.sol:L840        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

contracts/governance/NounsDAOLogicV2.sol:L853        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

contracts/governance/NounsDAOExecutor.sol:L73        require(delay_ >= MINIMUM_DELAY, 'NounsDAOExecutor::constructor: Delay must exceed minimum delay.');

contracts/governance/NounsDAOExecutor.sol:L74        require(delay_ <= MAXIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must not exceed maximum delay.');

contracts/governance/NounsDAOExecutor.sol:L81        require(msg.sender == address(this), 'NounsDAOExecutor::setDelay: Call must come from NounsDAOExecutor.');

contracts/governance/NounsDAOExecutor.sol:L82        require(delay_ >= MINIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must exceed minimum delay.');

contracts/governance/NounsDAOExecutor.sol:L83        require(delay_ <= MAXIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must not exceed maximum delay.');

contracts/governance/NounsDAOExecutor.sol:L90        require(msg.sender == pendingAdmin, 'NounsDAOExecutor::acceptAdmin: Call must come from pendingAdmin.');

contracts/governance/NounsDAOExecutor.sol:L114        require(msg.sender == admin, 'NounsDAOExecutor::queueTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L134        require(msg.sender == admin, 'NounsDAOExecutor::cancelTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L149        require(msg.sender == admin, 'NounsDAOExecutor::executeTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L152        require(queuedTransactions[txHash], "NounsDAOExecutor::executeTransaction: Transaction hasn't been queued.");

contracts/governance/NounsDAOExecutor.sol:L174        require(success, 'NounsDAOExecutor::executeTransaction: Transaction execution reverted.');

contracts/governance/NounsDAOLogicV1.sol:L122        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

contracts/governance/NounsDAOLogicV1.sol:L123        require(msg.sender == admin, 'NounsDAO::initialize: admin only');

contracts/governance/NounsDAOLogicV1.sol:L124        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

contracts/governance/NounsDAOLogicV1.sol:L125        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

contracts/governance/NounsDAOLogicV1.sol:L197        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

contracts/governance/NounsDAOLogicV1.sol:L198        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

contracts/governance/NounsDAOLogicV1.sol:L336        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

contracts/governance/NounsDAOLogicV1.sol:L364        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

contracts/governance/NounsDAOLogicV1.sol:L365        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

contracts/governance/NounsDAOLogicV1.sol:L366        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

contracts/governance/NounsDAOLogicV1.sol:L422        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

contracts/governance/NounsDAOLogicV1.sol:L485        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

contracts/governance/NounsDAOLogicV1.sol:L501        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

contracts/governance/NounsDAOLogicV1.sol:L502        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

contracts/governance/NounsDAOLogicV1.sol:L505        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

contracts/governance/NounsDAOLogicV1.sol:L530        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

contracts/governance/NounsDAOLogicV1.sol:L546        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

contracts/governance/NounsDAOLogicV1.sol:L563        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

contracts/governance/NounsDAOLogicV1.sol:L581        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV1.sol:L599        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

contracts/governance/NounsDAOLogicV1.sol:L617        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

contracts/governance/NounsDAOLogicV1.sol:L638        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

contracts/governance/NounsDAOLogicV1.sol:L651        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```
### [G-05] Empty blocks should be removed or emit something


#### Impact
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.


#### Findings:
```
contracts/governance/NounsDAOLogicV2.sol:L1030    receive() external payable {}

contracts/governance/NounsDAOExecutor.sol:L186    receive() external payable {}

contracts/governance/NounsDAOExecutor.sol:L188    fallback() external payable {}

```

### [G-06] Use a more recent version of solidity.


#### Impact
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining 
Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads 
Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings 
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.


#### Findings:
```
contracts/base/ERC721Enumerable.sol:L28      pragma solidity ^0.8.0;

contracts/base/ERC721Checkpointable.sol:L35      pragma solidity ^0.8.6;

contracts/governance/NounsDAOProxy.sol:L36      pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV2.sol:L53      pragma solidity ^0.8.6;

contracts/governance/NounsDAOInterfaces.sol:L33      pragma solidity ^0.8.6;

contracts/governance/NounsDAOExecutor.sol:L31      pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV1.sol:L61      pragma solidity ^0.8.6;

```

### [G-07] ++i costs less gas than i++, especially when it's used in for loops.


#### Impact
Saves 6 gas per loop.


#### Findings:
```
contracts/governance/NounsDAOLogicV2.sol:L226        proposalCount++;

contracts/governance/NounsDAOLogicV2.sol:L292        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L330        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L357        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L382        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L216        proposalCount++;

contracts/governance/NounsDAOLogicV1.sol:L281        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L319        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L346        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L371        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

### [G-08] Using private rather than public for constants to saves gas.


#### Impact
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.


#### Findings:
```
contracts/base/ERC721Checkpointable.sol:L41    uint8 public constant decimals = 0;

contracts/base/ERC721Checkpointable.sol:L59    bytes32 public constant DOMAIN_TYPEHASH =

contracts/base/ERC721Checkpointable.sol:L63    bytes32 public constant DELEGATION_TYPEHASH =

contracts/governance/NounsDAOLogicV2.sol:L59    string public constant name = 'Nouns DAO';

contracts/governance/NounsDAOLogicV2.sol:L62    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

contracts/governance/NounsDAOLogicV2.sol:L65    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

contracts/governance/NounsDAOLogicV2.sol:L68    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

contracts/governance/NounsDAOLogicV2.sol:L71    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

contracts/governance/NounsDAOLogicV2.sol:L74    uint256 public constant MIN_VOTING_DELAY = 1;

contracts/governance/NounsDAOLogicV2.sol:L77    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

contracts/governance/NounsDAOLogicV2.sol:L80    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

contracts/governance/NounsDAOLogicV2.sol:L83    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

contracts/governance/NounsDAOLogicV2.sol:L86    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

contracts/governance/NounsDAOLogicV2.sol:L89    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

contracts/governance/NounsDAOLogicV2.sol:L92    uint256 public constant proposalMaxOperations = 10; // 10 actions

contracts/governance/NounsDAOLogicV2.sol:L95    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

contracts/governance/NounsDAOLogicV2.sol:L98    uint256 public constant REFUND_BASE_GAS = 36000;

contracts/governance/NounsDAOLogicV2.sol:L101    bytes32 public constant DOMAIN_TYPEHASH =

contracts/governance/NounsDAOLogicV2.sol:L105    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

contracts/governance/NounsDAOExecutor.sol:L62    uint256 public constant GRACE_PERIOD = 14 days;

contracts/governance/NounsDAOExecutor.sol:L63    uint256 public constant MINIMUM_DELAY = 2 days;

contracts/governance/NounsDAOExecutor.sol:L64    uint256 public constant MAXIMUM_DELAY = 30 days;

contracts/governance/NounsDAOLogicV1.sol:L67    string public constant name = 'Nouns DAO';

contracts/governance/NounsDAOLogicV1.sol:L70    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

contracts/governance/NounsDAOLogicV1.sol:L73    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

contracts/governance/NounsDAOLogicV1.sol:L76    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

contracts/governance/NounsDAOLogicV1.sol:L79    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

contracts/governance/NounsDAOLogicV1.sol:L82    uint256 public constant MIN_VOTING_DELAY = 1;

contracts/governance/NounsDAOLogicV1.sol:L85    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

contracts/governance/NounsDAOLogicV1.sol:L88    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%

contracts/governance/NounsDAOLogicV1.sol:L91    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

contracts/governance/NounsDAOLogicV1.sol:L94    uint256 public constant proposalMaxOperations = 10; // 10 actions

contracts/governance/NounsDAOLogicV1.sol:L97    bytes32 public constant DOMAIN_TYPEHASH =

contracts/governance/NounsDAOLogicV1.sol:L101    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```

### [G-09] Revert message greater than 32 bytes


#### Impact
Keep revert message lower than or equal to 32 bytes to save gas.


#### Findings:
```
contracts/base/ERC721Enumerable.sol:L62        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

contracts/base/ERC721Enumerable.sol:L77        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');

contracts/base/ERC721Checkpointable.sol:L140        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

contracts/base/ERC721Checkpointable.sol:L141        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

contracts/base/ERC721Checkpointable.sol:L142        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

contracts/base/ERC721Checkpointable.sol:L164        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

contracts/governance/NounsDAOProxy.sol:L79        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

contracts/governance/NounsDAOProxy.sol:L80        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

contracts/governance/NounsDAOLogicV2.sol:L133        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

contracts/governance/NounsDAOLogicV2.sol:L135        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

contracts/governance/NounsDAOLogicV2.sol:L136        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

contracts/governance/NounsDAOLogicV2.sol:L137        require(

contracts/governance/NounsDAOLogicV2.sol:L141        require(

contracts/governance/NounsDAOLogicV2.sol:L145        require(

contracts/governance/NounsDAOLogicV2.sol:L197        require(

contracts/governance/NounsDAOLogicV2.sol:L201        require(

contracts/governance/NounsDAOLogicV2.sol:L207        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

contracts/governance/NounsDAOLogicV2.sol:L208        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

contracts/governance/NounsDAOLogicV2.sol:L213            require(

contracts/governance/NounsDAOLogicV2.sol:L217            require(

contracts/governance/NounsDAOLogicV2.sol:L286        require(

contracts/governance/NounsDAOLogicV2.sol:L312        require(

contracts/governance/NounsDAOLogicV2.sol:L324        require(

contracts/governance/NounsDAOLogicV2.sol:L347        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

contracts/governance/NounsDAOLogicV2.sol:L350        require(

contracts/governance/NounsDAOLogicV2.sol:L375        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

contracts/governance/NounsDAOLogicV2.sol:L377        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

contracts/governance/NounsDAOLogicV2.sol:L433        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

contracts/governance/NounsDAOLogicV2.sol:L577        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

contracts/governance/NounsDAOLogicV2.sol:L593        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

contracts/governance/NounsDAOLogicV2.sol:L594        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

contracts/governance/NounsDAOLogicV2.sol:L597        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

contracts/governance/NounsDAOLogicV2.sol:L622        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

contracts/governance/NounsDAOLogicV2.sol:L623        require(

contracts/governance/NounsDAOLogicV2.sol:L638        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

contracts/governance/NounsDAOLogicV2.sol:L639        require(

contracts/governance/NounsDAOLogicV2.sol:L655        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L656        require(

contracts/governance/NounsDAOLogicV2.sol:L674        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L677        require(

contracts/governance/NounsDAOLogicV2.sol:L682        require(

contracts/governance/NounsDAOLogicV2.sol:L702        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV2.sol:L705        require(

contracts/governance/NounsDAOLogicV2.sol:L709        require(

contracts/governance/NounsDAOLogicV2.sol:L727        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

contracts/governance/NounsDAOLogicV2.sol:L801        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

contracts/governance/NounsDAOLogicV2.sol:L819        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

contracts/governance/NounsDAOLogicV2.sol:L840        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

contracts/governance/NounsDAOLogicV2.sol:L853        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

contracts/governance/NounsDAOExecutor.sol:L73        require(delay_ >= MINIMUM_DELAY, 'NounsDAOExecutor::constructor: Delay must exceed minimum delay.');

contracts/governance/NounsDAOExecutor.sol:L74        require(delay_ <= MAXIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must not exceed maximum delay.');

contracts/governance/NounsDAOExecutor.sol:L81        require(msg.sender == address(this), 'NounsDAOExecutor::setDelay: Call must come from NounsDAOExecutor.');

contracts/governance/NounsDAOExecutor.sol:L82        require(delay_ >= MINIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must exceed minimum delay.');

contracts/governance/NounsDAOExecutor.sol:L83        require(delay_ <= MAXIMUM_DELAY, 'NounsDAOExecutor::setDelay: Delay must not exceed maximum delay.');

contracts/governance/NounsDAOExecutor.sol:L90        require(msg.sender == pendingAdmin, 'NounsDAOExecutor::acceptAdmin: Call must come from pendingAdmin.');

contracts/governance/NounsDAOExecutor.sol:L98        require(

contracts/governance/NounsDAOExecutor.sol:L114        require(msg.sender == admin, 'NounsDAOExecutor::queueTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L115        require(

contracts/governance/NounsDAOExecutor.sol:L134        require(msg.sender == admin, 'NounsDAOExecutor::cancelTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L149        require(msg.sender == admin, 'NounsDAOExecutor::executeTransaction: Call must come from admin.');

contracts/governance/NounsDAOExecutor.sol:L152        require(queuedTransactions[txHash], "NounsDAOExecutor::executeTransaction: Transaction hasn't been queued.");
contracts/governance/NounsDAOExecutor.sol:L153        require(

contracts/governance/NounsDAOExecutor.sol:L157        require(

contracts/governance/NounsDAOExecutor.sol:L174        require(success, 'NounsDAOExecutor::executeTransaction: Transaction execution reverted.');

contracts/governance/NounsDAOLogicV1.sol:L122        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

contracts/governance/NounsDAOLogicV1.sol:L124        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

contracts/governance/NounsDAOLogicV1.sol:L125        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

contracts/governance/NounsDAOLogicV1.sol:L126        require(

contracts/governance/NounsDAOLogicV1.sol:L130        require(

contracts/governance/NounsDAOLogicV1.sol:L134        require(

contracts/governance/NounsDAOLogicV1.sol:L138        require(

contracts/governance/NounsDAOLogicV1.sol:L187        require(

contracts/governance/NounsDAOLogicV1.sol:L191        require(

contracts/governance/NounsDAOLogicV1.sol:L197        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

contracts/governance/NounsDAOLogicV1.sol:L198        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

contracts/governance/NounsDAOLogicV1.sol:L203            require(

contracts/governance/NounsDAOLogicV1.sol:L207            require(

contracts/governance/NounsDAOLogicV1.sol:L275        require(

contracts/governance/NounsDAOLogicV1.sol:L301        require(

contracts/governance/NounsDAOLogicV1.sol:L313        require(

contracts/governance/NounsDAOLogicV1.sol:L336        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

contracts/governance/NounsDAOLogicV1.sol:L339        require(

contracts/governance/NounsDAOLogicV1.sol:L364        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

contracts/governance/NounsDAOLogicV1.sol:L366        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

contracts/governance/NounsDAOLogicV1.sol:L422        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

contracts/governance/NounsDAOLogicV1.sol:L485        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

contracts/governance/NounsDAOLogicV1.sol:L501        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

contracts/governance/NounsDAOLogicV1.sol:L502        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

contracts/governance/NounsDAOLogicV1.sol:L505        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

contracts/governance/NounsDAOLogicV1.sol:L530        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

contracts/governance/NounsDAOLogicV1.sol:L531        require(

contracts/governance/NounsDAOLogicV1.sol:L546        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

contracts/governance/NounsDAOLogicV1.sol:L547        require(

contracts/governance/NounsDAOLogicV1.sol:L563        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

contracts/governance/NounsDAOLogicV1.sol:L564        require(

contracts/governance/NounsDAOLogicV1.sol:L581        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

contracts/governance/NounsDAOLogicV1.sol:L582        require(

contracts/governance/NounsDAOLogicV1.sol:L599        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

contracts/governance/NounsDAOLogicV1.sol:L617        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

contracts/governance/NounsDAOLogicV1.sol:L638        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

contracts/governance/NounsDAOLogicV1.sol:L651        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

### [G-10] Splitting ```require()``` statements that use && saves gas.


#### Impact
Consider splitting the ```require()``` statements to save gas.


#### Findings:
```
contracts/governance/NounsDAOLogicV2.sol:L819        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

contracts/governance/NounsDAOLogicV1.sol:L617        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

### [G-11] Explicit initialization with zero not required


#### Impact
Explicit initialization with zero is not required for variable declaration because uints are 0 by default. Removing this will reduce contract size and save a bit of gas.


#### Findings:
```
contracts/base/ERC721Checkpointable.sol:L41    uint8 public constant decimals = 0;

contracts/base/ERC721Checkpointable.sol:L181        uint32 lower = 0;

contracts/governance/NounsDAOLogicV2.sol:L292        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L330        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L357        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L382        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L948        uint256 lower = 0;

contracts/governance/NounsDAOLogicV1.sol:L281        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L319        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L346        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L371        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

### [G-12] ```++i```/```i++``` should be ```unchecked{++i}```/```unchecked{i++}``` when it is not possible for them to overflow, as is the case when used in for- and while-loops.


#### Impact
The ```unchecked``` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop.


#### Findings:
```
contracts/governance/NounsDAOLogicV2.sol:L292        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L330        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L357        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV2.sol:L382        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L281        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L319        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L346        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol:L371        for (uint256 i = 0; i < proposal.targets.length; i++) {

```