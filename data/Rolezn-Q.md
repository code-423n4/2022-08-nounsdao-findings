## (1) Missing Contract-existence Checks Before Low-level Calls

Severity: Low

Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that <address>.code.length > 0

## Proof Of Concept


	(bool success, bytes memory returnData) = callee.delegatecall(data);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L95

	(bool success, ) = implementation.delegatecall(msg.data);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L110


## Recommended Mitigation Steps
Add zero-address checks and add a check to verify that <address>.code.length > 0



## (2) Missing Checks for Address(0x0) 

Severity: Low

## Proof Of Concept


    function _delegate(address delegator, address delegatee) internal {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L197

    function _setPendingAdmin(address newPendingAdmin) external {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L597

    function _setVetoer(address newVetoer) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L637

    function _setPendingAdmin(address newPendingAdmin) external {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L799

    function _setVetoer(address newVetoer) public {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L839



## Recommended Mitigation Steps

Consider adding zero-address checks in the mentioned codebase.



## (3) Unused Receive() Function Will Lock Ether In Contract 

Severity: Low

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

## Proof Of Concept


    receive() external payable {}
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1030



## Recommended Mitigation Steps

The function should call another function, otherwise it should revert



## (4) Avoid Floating Pragmas: The Version Should Be Locked

Severity: Non-Critical

## Proof Of Concept

    Found usage of floating pragmas ^0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L35

    Found usage of floating pragmas ^0.8.0 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L28

    Found usage of floating pragmas ^0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L33

    Found usage of floating pragmas ^0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L61

    Found usage of floating pragmas ^0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L53

    Found usage of floating pragmas ^0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L36


## Recommended Mitigation Steps

Avoid Floating Pragmas: The Version Should Be Locked



## (5) Constants Should Be Defined Rather Than Using Magic Numbers

Severity: Non-Critical

## Proof Of Concept

        require(n < 2**32, errorMessage);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L254

        require(n < 2**96, errorMessage);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L259





## (6) Event Is Missing Indexed Fields

Severity: Non-Critical

Each event should use three indexed fields if there are three or more fields.
In addition, each event should have at least one indexed fields to allow easier filtering of logs.

## Proof Of Concept


    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L73

    event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L37

    event ProposalCreatedWithRequirements(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        uint256 proposalThreshold,
        uint256 quorumVotes,
        string description
    );
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L50

    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L70

    event ProposalQueued(uint256 id, uint256 eta);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L76

    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L85

    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L88

    event NewImplementation(address oldImplementation, address newImplementation);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L91

    event ProposalThresholdBPSSet(uint256 oldProposalThresholdBPS, uint256 newProposalThresholdBPS);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L94

    event QuorumVotesBPSSet(uint256 oldQuorumVotesBPS, uint256 newQuorumVotesBPS);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L97

    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L100

    event NewAdmin(address oldAdmin, address newAdmin);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L103

    event NewVetoer(address oldVetoer, address newVetoer);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L106

    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L111

    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L114

    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L117

    event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L120

    event Withdraw(uint256 amount, bool sent);
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L123




## (7) Implementation contract may not be initialized

OpenZeppelin recommends that the initializer modifier be applied to constructors. 
Per OZs Post implementation contract should be initialized to avoid potential griefs or exploits.
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5

Severity: Non-Critical

## Proof Of Concept


    contract NounsDAOEvents 
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L35

    contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents 
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L65

    contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L57

    contract NounsDAOProxy is NounsDAOProxyStorage, NounsDAOEvents 
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L40





## (8) Non-assembly Method Available

Severity: Non-Critical

## Proof Of Concept



    chainId := chainid()
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L285


    chainId := chainid()
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L679


    chainId := chainid()
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1013






## (9) Use a more recent version of Solidity

Severity: Non-Critical

Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>)
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

## Proof Of Concept


    Found old version 0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L35

    Found old version 0.8.0 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L28

    Found old version 0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L33

    Found old version 0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L61

    Found old version 0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L53

    Found old version 0.8.6 of Solidity
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L36



## Recommended Mitigation Steps

Consider updating to a more recent solidity version.



## (10) Public Functions Not Called By The Contract Should Be Declared External Instead

Severity: Non-Critical

Contracts are allowed to override their parents’ functions and change the visibility from external to public.

## Proof Of Concept


    function delegate(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L112

    function delegateBySig(
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L126





## (11) Large multiples of ten should use scientific notation

Severity: Non-Critical

Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

## Proof Of Concept


        return (number * bps) / 10000;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L673

        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L908

        return (number * bps) / 10000;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1007

        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L908

        return (number * bps) / 10000;
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1007





## (12) Use of Block.Timestamp

Severity: Non-Critical

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

## Proof Of Concept


        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L142

        uint256 eta = block.timestamp + timelock.delay();
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L280

        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L438

        uint256 eta = block.timestamp + timelock.delay();
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L291

        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L449



## Recommended Mitigation Steps
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor (either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change a contract state can be more secure, as miners are unable to easily manipulate the block number.



