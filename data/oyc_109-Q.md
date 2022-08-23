## [L-01] Unspecific Compiler Version Pragma

Avoid floating pragmas for non-library contracts.

While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::35 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::28 => pragma solidity ^0.8.0;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::33 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::61 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::53 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::36 => pragma solidity ^0.8.6;
```

## [L-02] Use of Block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::142 => require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::280 => uint256 eta = block.timestamp + timelock.delay();
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::438 => } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::291 => uint256 eta = block.timestamp + timelock.delay();
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::449 => } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
```

## [L-03] Unused receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1030 => receive() external payable {}
```

## [L-04] abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). Unless there is a compelling reason, abi.encode should be preferred. If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::138 => bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::483 => bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::575 => bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
```

## [L-05] lack of zero address checks

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L642
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L71

## [N-01] Use a more recent version of solidity

Use a solidity version of at least 0.8.4 to get bytes.concat() instead of abi.encodePacked(<bytes>,<bytes>)
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::35 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::28 => pragma solidity ^0.8.0;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::33 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::61 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::53 => pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::36 => pragma solidity ^0.8.6;
```

## [N-02] Large multiples of ten should use scientific notation

Use (e.g. 1e6) rather than decimal literals (e.g. 1000000), for better code readability

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::673 => return (number * bps) / 10000;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::908 => uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1007 => return (number * bps) / 10000;
```

## [N-03] Event is missing indexed fields

Each event should use three indexed fields if there are three or more fields

```
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::73 => event ProposalCanceled(uint256 id);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::76 => event ProposalQueued(uint256 id, uint256 eta);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::79 => event ProposalExecuted(uint256 id);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::82 => event ProposalVetoed(uint256 id);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::85 => event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::88 => event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::91 => event NewImplementation(address oldImplementation, address newImplementation);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::94 => event ProposalThresholdBPSSet(uint256 oldProposalThresholdBPS, uint256 newProposalThresholdBPS);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::97 => event QuorumVotesBPSSet(uint256 oldQuorumVotesBPS, uint256 newQuorumVotesBPS);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::100 => event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::103 => event NewAdmin(address oldAdmin, address newAdmin);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::106 => event NewVetoer(address oldVetoer, address newVetoer);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::111 => event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::114 => event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::117 => event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::123 => event Withdraw(uint256 amount, bool sent);
```

## [N-04] Public functions not called by the contract should be declared external instead

Contracts are allowed to override their parents' functions and change the visibility from external to public.

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

## [N-05] Consider addings checks for signature malleability

Use OpenZeppelin's ECDSA contract rather than calling ecrecover() directly

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::139 => address signatory = ecrecover(digest, v, r, s);
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::484 => address signatory = ecrecover(digest, v, r, s);
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::576 => address signatory = ecrecover(digest, v, r, s);
```

## [N-06] Non-assembly method available

assembly{ id := chainid() } => uint256 id = block.chainid
assembly { size := extcodesize() } => uint256 size = address().code.length

```
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::285 => chainId := chainid()
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::679 => chainId := chainid()
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1013 => chainId := chainid()
```

## [N-07] Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

instances:

```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::101 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::105 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

## [N-08] Lines are too long

Usually lines in source code are limited to 80 characters. Today's screens are much larger so it's reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

```
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::156 => /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::181 => /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::256 => /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::281 => /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::375 => /// @notice The minimum number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::507 => /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::599 => /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics
```
