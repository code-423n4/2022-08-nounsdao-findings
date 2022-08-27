# [L-01] Add zero address checks for `vetoer` in `initializer()` and `newPendingAdmin` in `_setPendingAdmin()`

## Impact

Passing an invalid `vetoer` in `initializer()` might have to force redeployments.

If `vetoer` receives the address zero in `initializer()`, the contract will have to be reployed.

```
function initialize(
     address timelock_,
     address nouns_,
     address vetoer_,
     uint256 votingPeriod_,
     uint256 votingDelay_,
     uint256 proposalThresholdBPS_,
     DynamicQuorumParams calldata dynamicQuorumParams_
) public virtual {
     require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
     require(msg.sender == admin, 'NounsDAO::initialize: admin only');
     require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
     require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
     require(
          votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
          'NounsDAO::initialize: invalid voting period'
     );
     require(
          votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
          'NounsDAO::initialize: invalid voting delay'
     );
     require(
          proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
          'NounsDAO::initialize: invalid proposal threshold bps'
     );

     emit VotingPeriodSet(votingPeriod, votingPeriod_);
     emit VotingDelaySet(votingDelay, votingDelay_);
     emit ProposalThresholdBPSSet(proposalThresholdBPS, proposalThresholdBPS_);

     timelock = INounsDAOExecutor(timelock_);
     nouns = NounsTokenLike(nouns_);
     vetoer = vetoer_;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L124-L156


It can also cause an incorrect admin address being passed to `_setPendingAdmin`, and therefore cause malfunctions on the protocol.

```
File: contracts/governance/NounsDAOLogicV2.sol
function _setPendingAdmin(address newPendingAdmin) external {
     // Check caller = admin
     require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

     // Save current value, if any, for inclusion in log
     address oldPendingAdmin = pendingAdmin;

     // Store pendingAdmin with value newPendingAdmin
     pendingAdmin = newPendingAdmin;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799-L807

## Recommended Mitigation Steps

Add validation checks to prevent `vetoer` and `newPendingAdmin` from receiving zero address.


# [NC-01] Non library/interface files should use fixed compiler verion

Locking the pragma helps to ensure that contracts do not accidentally get deployed using an outdated compiler version.
Note that pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or a package.

There are 5 instances of this issue.

```
File: contracts/base/ERC721Enumerable.sol
28: pragma solidity ^0.8.0;
```

```
File: contracts/governance/NounsDAOInterfaces.sol
33: pragma solidity ^0.8.6;
```

```
File: contracts/governance/NounsDAOLogicV1.sol
61: pragma solidity ^0.8.6;
```

```
File: contracts/governance/NounsDAOLogicV2.sol
53: pragma solidity ^0.8.6;
```

```
File: contracts/governance/NounsDAOProxy.sol
36: pragma solidity ^0.8.6;
```

# [NC-02] Use constants rather than defining magic numbers

For the examples above, the value 10000 could be a constant

```
File: contracts/governance/NounsDAOLogicV2.sol
908: uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
1007: return (number * bps) / 10000;
```

```
File: contracts/governance/NounsDAOLogicV1.sol
673: return (number * bps) / 10000;
```

# [NC-03] Public functions not called by the contract should be declared external

```
File: contracts/governance/NounsDAOLogicV2.sol
184: function propose(
851: function _burnVetoPower() public {
862: function proposalThreshold() public view returns (uint256) {
1002: function maxQuorumVotes() public view returns (uint256) {
```

# [NC-04] Critical parameter changes should use two-step procedure

Lack of two-step procedure for critical operations is error-prone and could cause bad parameters being inserted into the protocol.

```
File: contracts/governance/NounsDAOLogicV2.sol
799: function _setPendingAdmin(address newPendingAdmin) external {
839: function _setVetoer(address newVetoer) public {
```

# [NC-05] Missing NATSPEC

Consider adding NATSPEC for the following functions:

```
File: contracts/governance/NounsDAOLogicV2.sol
783: function _withdraw() external {
866: function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
964: function _writeQuorumParamsCheckpoint(DynamicQuorumParams memory params) internal {
974: function _refundGas(uint256 startGas) internal {
988: function min(uint256 a, uint256 b) internal pure returns (uint256) {
1006: function bps2Uint(uint256 bps, uint256 number) internal pure returns (uint256) {
1010: function getChainIdInternal() internal view returns (uint256) {
1018: function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
1023: function safe16(uint256 n) internal pure returns (uint16) {
```

# [NC-06] Use type(uint<n>).max instead of 2**<n>

Using type(uint<n>).max will be more readable and it can also save some gas. 

```        
File: contracts/base/ERC721Checkpointable.sol
254: require(n < 2**32, errorMessage);
259: require(n < 2**96, errorMessage);
```