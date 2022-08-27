| |Issue|Instances|
|-|:-|:-:|
| 1 | Zero-address checks are missing | 2 |
| 2 | Use of floating pragma | 4 |

## [L-01] Zero-address checks are missing

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are some places where this is missing.
Impact: Accidental use of zero-addresses may result in exceptions or force redeployment of contracts.
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
156:         vetoer = vetoer_;

844:         vetoer = newVetoer;
```

## [L-02] Use of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

<https://swcregistry.io/docs/SWC-103>

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


| |Issue|Instances|
|-|:-|:-:|
| 1 | Function is missing NatSpec | 19 |
| 2 | No need to explicitly initialize variables with default values | 6 |
| 3 | Event is missing indexed fields | 5 |
| 4 | Consider addings checks for signature malleability | 1 |
| 5 | Non-assembly method available | 2 |
| 6 | NatSpec is incomplete | 6 |
| 7 | Duplicated `require()`/`revert()` checks should be refactored to a modifier or function | 11 |
| 8 | Misleading comment | 1 |
| 9 | Not following constant naming conventions | 1 |


## [N-01] Function is missing NatSpec
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
305:     function queueOrRevertInternal(

783:     function _withdraw() external {

866:     function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {

964:     function _writeQuorumParamsCheckpoint(DynamicQuorumParams memory params) internal {

974:     function _refundGas(uint256 startGas) internal {

988:     function min(uint256 a, uint256 b) internal pure returns (uint256) {

1006:     function bps2Uint(uint256 bps, uint256 number) internal pure returns (uint256) {

1010:     function getChainIdInternal() internal view returns (uint256) {

1018:     function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

1023:     function safe16(uint256 n) internal pure returns (uint16) {
```


```
contracts/governance/NounsDAOInterfaces.sol
402: interface INounsDAOExecutor {
403:     function delay() external view returns (uint256);
404: 
405:     function GRACE_PERIOD() external view returns (uint256);
406: 
407:     function acceptAdmin() external;
408: 
409:     function queuedTransactions(bytes32 hash) external view returns (bool);
410: 
411:     function queueTransaction(

419:     function cancelTransaction(

427:     function executeTransaction(

436: interface NounsTokenLike {
437:     function getPriorVotes(address account, uint256 blockNumber) external view returns (uint96);
438: 
439:     function totalSupply() external view returns (uint96);
440: }
```


## [N-02] No need to explicitly initialize variables with default values
If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...). Explicitly initializing it with its default value is an anti-pattern.
As an example: `for (uint256 i = 0; i < numIterations; ++i) {` should be replaced with `for (uint256 i; i < numIterations; ++i) {`
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
292:         for (uint256 i = 0; i < proposal.targets.length; i++) {
330:         for (uint256 i = 0; i < proposal.targets.length; i++) {
357:         for (uint256 i = 0; i < proposal.targets.length; i++) {
382:         for (uint256 i = 0; i < proposal.targets.length; i++) {
948:         uint256 lower = 0;
```

```
contracts/base/ERC721Checkpointable.sol
181:         uint32 lower = 0;
```


## [N-03] Event is missing indexed fields
Each `event` should use three `indexed` fields if there are three or more fields
### Findings
```
contracts/governance/NounsDAOInterfaces.sol
37:     event ProposalCreated(
38:         uint256 id,
39:         address proposer,
40:         address[] targets,
41:         uint256[] values,
42:         string[] signatures,
43:         bytes[] calldatas,
44:         uint256 startBlock,
45:         uint256 endBlock,
46:         string description
47:     );

50:     event ProposalCreatedWithRequirements(
51:         uint256 id,
52:         address proposer,
53:         address[] targets,
54:         uint256[] values,
55:         string[] signatures,
56:         bytes[] calldatas,
57:         uint256 startBlock,
58:         uint256 endBlock,
59:         uint256 proposalThreshold,
60:         uint256 quorumVotes,
61:         string description
62:     );

70:     event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

120:     event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);
```

```
contracts/base/ERC721Checkpointable.sol
73:     event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
```

## [N-04] Consider addings checks for signature malleability
Use OpenZeppelin's `ECDSA` contract rather than calling `ecrecover()` directly
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
576:         address signatory = ecrecover(digest, v, r, s);
577:         require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
578:         emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
```

## [N-05] Non-assembly method available
`assembly{ chainId := chainid() }` => `uint256 chainId = block.chainid`, 
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
1013:             chainId := chainid()
```
```
contracts/base/ERC721Checkpointable.sol
285:             chainId := chainid()
```


## [N-06] NatSpec is incomplete
Check `@audit` comments for details
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
			//@audit misssing @param for proposalId, support, v, r, s
560:     /**
561:      * @notice Cast a vote for a proposal by signature
562:      * @dev External function that accepts EIP-712 signatures for voting on proposals.
563:      */
564:     function castVoteBySig(
565:         uint256 proposalId,
566:         uint8 support,
567:         uint8 v,
568:         bytes32 r,
569:         bytes32 s
570:     ) external {

		//@audit missing @param newVetoer
835:     /**
836:      * @notice Changes vetoer address
837:      * @dev Vetoer function for updating vetoer address
838:      */
839:     function _setVetoer(address newVetoer) public {

		//@audit missing @return
858:     /**
859:      * @notice Current proposal threshold using Noun Total Supply
860:      * Differs from `GovernerBravo` which uses fixed amount
861:      */
862:     function proposalThreshold() public view returns (uint256) {

			//@audit missing @param proposalId and @return
873:     /**
874:      * @notice Quorum votes required for a specific proposal to succeed
875:      * Differs from `GovernerBravo` which uses fixed amount
876:      */
877:     function quorumVotes(uint256 proposalId) public view returns (uint256) {

		//@audit missing @return
992:     /**
993:      * @notice Current min quorum votes using Noun total supply
994:      */
995:     function minQuorumVotes() public view returns (uint256) {

		//@audit missing @return
0999:     /**
1000:      * @notice Current max quorum votes using Noun total supply
1001:      */
1002:     function maxQuorumVotes() public view returns (uint256) {
```



## [N-07] Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
622:         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

638:         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

655:         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

674:         require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

702:         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

727:         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

753:         if (msg.sender != admin) {
754:             revert AdminOnly();
755:         }

784:         if (msg.sender != admin) {
785:             revert AdminOnly();
786:         }

801:         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
```

```
contracts/governance/NounsDAOLogicV2.sol
840:         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```


## [N-08] Misleading comment
Can confuse people and let them assume method is implemented incorrectly
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
851:     function _burnVetoPower() public {
852:         // Check caller is pendingAdmin and pendingAdmin ≠ address(0)	@audit out of place comment
853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

## [N-09] Not following constant naming conventions
Constant variable names should be all caps by naming conventions
### Findings
```
contracts/governance/NounsDAOLogicV2.sol
92:     uint256 public constant proposalMaxOperations = 10; // 10 actions
```
Should be `PROPOSAL_MAX_OPERATIONS`