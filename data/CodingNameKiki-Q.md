1. Unused/empty receive()/fallback() function

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert  (e.g. require(msg.sender == address(weth)))

contracts/governance/NounsDAOLogicV2.sol
1030: receive() external payable {}

2. Front-runable initializer

There is nothing preventing another account from calling the initializer before the contract owner. In the best case, the owner is forced to waste gas and re-deploy. In the worst case, the owner does not notice that his/her call reverts, and everyone starts using a contract under the control of an attacker.

contracts/governance/NounsDAOLogicV2.sol
285: function queue(uint256 proposalId) external {
323: function execute(uint256 proposalId) external {
346: function cancel(uint256 proposalId) external {
374: function veto(uint256 proposalId) external {
489: function castVote(uint256 proposalId, uint8 support) external {
503: function castRefundableVote(uint256 proposalId, uint8 support) external {
518: function castRefundableVoteWithReason(
552: function castVoteWithReason(
564: function castVoteBySig(

3. Non-assembly method available

assembly{ id := chainid() } => uint256 id = block.chainid;

contracts/governance/NounsDAOLogicV2.sol
1013: chainId := chainid()

4. Constants should be defined rather than using magic numbers

contracts/governance/NounsDAOLogicV2.sol
909: uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;
1007: return (number * bps) / 10000;

5. Inconsistent spacing in comments

contracts/governance/NounsDAOLogicV2.sol
37: //    - `_setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS)`
38: //    - `_setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS)`
39: //    - `_setQuorumCoefficient(uint32 newQuorumCoefficient)`
43: //    - `totalSupply` used in dynamic quorum calculation.
44: //    - `creationBlock` used for retrieving checkpoints of votes and dynamic quorum params. This now

6. Function state mutability can be restricted to pure

Function doesn't change and can be declared as pure:

contracts/governance/NounsDAOLogicV2.sol
432: function state(uint256 proposalId) public view returns (ProposalState) {
862: function proposalThreshold() public view returns (uint256) {
877: function quorumVotes(uint256 proposalId) public view returns (uint256) {
922: function getDynamicQuorumParamsAt(uint256 blockNumber_) public view returns (DynamicQuorumParams memory) {
995: function minQuorumVotes() public view returns (uint256) {
1002: function maxQuorumVotes() public view returns (uint256) {

7. Abi.encodePacked() should not be used with dynamic types when passing the result to a hash function such as keccak256()

Use abi.encode() instead which will pad items to 32 bytes, which will prevent hash collisions (e.g. abi.encodePacked(0x123,0x456) => 0x123456 => abi.encodePacked(0x1,0x23456), but abi.encode(0x123,0x456) => 0x0...1230...456). If there is only one argument to abi.encodePacked() it can often be cast to bytes() or bytes32() instead.

contracts/governance/NounsDAOLogicV2.sol
575: bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));

8. Use of floating pragma

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

contracts/governance/NounsDAOLogicV2.sol
53: pragma solidity ^0.8.6;

contracts/governance/NounsDAOProxy.sol
36: pragma solidity ^0.8.6;

contracts/governance/NounsDAOInterfaces.sol
33: pragma solidity ^0.8.6;

contracts/base/ERC721Checkpointable.sol
35: pragma solidity ^0.8.6;

contracts/base/ERC721Enumerable.sol
28: pragma solidity ^0.8.0;

9. Initialize functions can be frontrun

The initialize function used to initialize important contract state can be called by anyone.
The attacker can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.
In the best case for the victim, they notice it and have to redeploy their contract costing gas.

contracts/governance/NounsDAOLogicV2.sol
124: function initialize(

10. Boolean comprasions

There is no need to compate constant to (true or false)

contracts/governance/NounsDAOLogicV2.sol
(Before) 329: proposal.executed = true;
(After) 329: !proposal.executed;

(Before) 356: proposal.canceled = true;
(After) 356: !proposal.canceled;

(Before) 381: proposal.vetoed = true;
(After) 381: !proposal.vetoed;

(Before) 610: receipt.hasVoted = true;
(After) 610: !receipt.hasVoted;

11. Expressions for constant values such as a call to keccak256(), should use immutable rather than constant

contracts/governance/NounsDAOLogicV2.sol
101: bytes32 public constant DOMAIN_TYPEHASH =
105: bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

