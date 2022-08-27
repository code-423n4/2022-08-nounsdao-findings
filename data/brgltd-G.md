# [G-01] Prefix increment costs less gas than postfix increment

There are 5 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
226: proposalCount++;
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

# [G-02] The increment in for loop post condition can be made unchecked to save gas

There are 4 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

# [G-03] Cache the length of the array before the loop

There are 4 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

# [G-04] Initializing a variable with the default value wastes gas

There are 5 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
948: uint256 lower = 0;
```

# [G-05] Use != 0 instead of > 0 to save gas.

Replace `> 0` with `!= 0` for unsigned integers.

There are 2 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
541: if (votes > 0) {
967: if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
```

# [G-06] Use custom errors rather than require/revert strings to save gas

There are 32 instances of this issue.

```
File: contracts/base/ERC721Enumerable.sol
62: require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
77: require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```

```
File: contracts/governance/NounsDAOLogicV2.sol
133: require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
134: require(msg.sender == admin, 'NounsDAO::initialize: admin only');
135: require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136: require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
207: require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208: require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
347: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
375: require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
376: require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
377: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
433: require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
577: require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
593: require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
594: require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
597: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
622: require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
638: require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
655: require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
674: require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
702: require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
727: require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
801: require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
819: require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
840: require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
1019: require(n <= type(uint32).max, errorMessage);
```

```
File: contracts/governance/NounsDAOProxy.sol
79: require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80: require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
98: revert(add(returnData, 0x20), returndatasize())
118: revert(free_mem_ptr, returndatasize())
```

# [G-07] Use right/left shift instead of division/multiplication to save gas

There's 1 instance of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
951: uint256 center = upper - (upper - lower) / 2;
```

# [G-08] Don’t compare boolean expressions to boolean literals

There's 1 instance of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
597: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

# [G-09] Using private rather than public for constants, saves gas

The values can still be inspected on the source code if necessary.

There are 16 instances of this issue.

```
File: contracts/governance/NounsDAOLogicV2.sol
59: string public constant name = 'Nouns DAO';
62: uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
65: uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
68: uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
71: uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
74: uint256 public constant MIN_VOTING_DELAY = 1;
77: uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
80: uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
83: uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
86: uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
89: uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
92: uint256 public constant proposalMaxOperations = 10; // 10 actions
95: uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
98: uint256 public constant REFUND_BASE_GAS = 36000;
101: bytes32 public constant DOMAIN_TYPEHASH =
105: bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

# [G-10] Repeated statements used for validation can be converted into a function modifier

There are 7 instances of this issue.

The following statements used to validate `msg.sender` can be converted into a single function modifier.

```
File: contracts/governance/NounsDAOLogicV2.sol
622: require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
638: require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
655: require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
674: require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
702: require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
727: require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
804: require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
```

The modifier can contain require statements or custom errors. From a gas-savings perspective, custom errors are a better choice.
