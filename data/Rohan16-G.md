## 1.++I COSTS LESS GAS COMPARED TO I++ OR I += 1 
**Pre-increments and pre-decrements are cheaper.**

For a `uint256` i variable, the following is true with the Optimizer enabled at 10k:
Increment:
`i += 1` is the most expensive form
`i++` costs `6` `gas` less than `i += 1`
`++i` costs `5 gas` less than `i++` (11 gas less than i += 1)

Note that post-increments (or post-decrements) return the old value before incrementing or decrementing, hence the name post-increment:
### Instances
// Links to Github file
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L216
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226


```
nounsdao/contracts/governance/NounsDAOLogicV1.sol:216:        proposalCount++;
nounsdao/contracts/governance/NounsDAOLogicV2.sol:226:        proposalCount++;
```

## 2.UNCHECKED{++I} INSTEAD OF I++ (OR USE ASSEMBLY WHEN APPLICABLE)
Use `++i` instead of `i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows. For extra safety if you are worried about this, you can add a require statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However when applicable, inline assembly will save much more gas.
### Instances
//Links to github file
`NounsDAOLogicV2.sol`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++)
```
`NounsDAOLogicV1.sol`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371

``` 
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
## 3.`<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

The overheads outlined below are `PER LOOP`, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas)
Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset
### Instances
// Links to github files
`NounsDAOLogicV2.sol`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++)
```
`NounsDAOLogicV1.sol`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371

``` 
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## 4. IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for `(uint256 i = 0; i < numIterations; ++i)` { should be replaced with for `(uint256 i; i < numIterations; ++i) {`

### Instances
// Links to gihthub file

https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L948
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L41
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L181

*Actual codes used*
```
contracts/governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:948:        uint256 lower = 0;
contracts/base/ERC721Checkpointable.sol:41:    uint8 public constant decimals = 0;
contracts/base/ERC721Checkpointable.sol:181:        uint32 lower = 0;
```

## 5.Custom Errors instead of Revert Strings to save Gas

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met)

Starting from Solidity v0.8.4,there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");),but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).
### Instances 
// Links to github file
[NounsDAOProxy.sol:L79](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L79)
[NounsDAOProxy.sol:L80](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L80)
[NounsDAOLogicV1.sol:L122](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L122)
[NounsDAOLogicV1.sol:L123](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L123)
[NounsDAOLogicV1.sol:L124](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L124)
[NounsDAOLogicV1.sol:L125](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L125)
[NounsDAOLogicV1.sol:L197](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L197)
[NounsDAOLogicV1.sol:L198](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L198)
[NounsDAOLogicV1.sol:L336](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L336)
[NounsDAOLogicV1.sol:L364](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L364)
[NounsDAOLogicV1.sol:L365](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L365)
[NounsDAOLogicV1.sol:L366](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L366)
[NounsDAOLogicV1.sol:L422](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L422)
[NounsDAOLogicV1.sol:L485](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L485)
[NounsDAOLogicV1.sol:L501](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L501)
[NounsDAOLogicV1.sol:L502](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L502)
[NounsDAOLogicV1.sol:L505](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L505)
[NounsDAOLogicV1.sol:L530](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L530)
[NounsDAOLogicV1.sol:L546](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L546)
[NounsDAOLogicV1.sol:L563](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L563)
[NounsDAOLogicV1.sol:L581](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L581)
[NounsDAOLogicV1.sol:L599](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L599)
[NounsDAOLogicV1.sol:L617](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L617)
[NounsDAOLogicV1.sol:L638](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L638)
[NounsDAOLogicV1.sol:L651](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L651)
[NounsDAOLogicV2.sol:L133](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L133)
[NounsDAOLogicV2.sol:L134](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L134)
[NounsDAOLogicV2.sol:L135](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L135)
[NounsDAOLogicV2.sol:L136](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L136)
[NounsDAOLogicV2.sol:L207](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L207)
[NounsDAOLogicV2.sol:L208](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L208)
[NounsDAOLogicV2.sol:L347](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L347)
[NounsDAOLogicV2.sol:L375](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L375)
[NounsDAOLogicV2.sol:L376](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L376)
[NounsDAOLogicV2.sol:L377](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L377)
[NounsDAOLogicV2.sol:L433](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L433)
[NounsDAOLogicV2.sol:L577](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L577)
[NounsDAOLogicV2.sol:L593](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L593)
[NounsDAOLogicV2.sol:L594](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L594)
[NounsDAOLogicV2.sol:L597](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L597)
[NounsDAOLogicV2.sol:L622](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L622)
[NounsDAOLogicV2.sol:L638](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L638)
[NounsDAOLogicV2.sol:L655](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L655)
[NounsDAOLogicV2.sol:L674](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L674)
[NounsDAOLogicV2.sol:L702](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L702)
[NounsDAOLogicV2.sol:L727](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L727)
[NounsDAOLogicV2.sol:L801](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L801)
[NounsDAOLogicV2.sol:L819](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L819)
[NounsDAOLogicV2.sol:L840](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L840)
[NounsDAOLogicV2.sol:L853](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L853)
[ERC721Enumerable.sol:L62](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L62)
[ERC721Enumerable.sol:L77](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L77)
[ERC721Checkpointable.sol:L140](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L140)
[ERC721Checkpointable.sol:L141](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L141)
[ERC721Checkpointable.sol:L142](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L142)
[ERC721Checkpointable.sol:L164](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L164)



```
contracts/governance/NounsDAOProxy.sol:79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
contracts/governance/NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
contracts/governance/NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
contracts/governance/NounsDAOLogicV1.sol:123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
contracts/governance/NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
contracts/governance/NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
contracts/governance/NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
contracts/governance/NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
contracts/governance/NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
contracts/governance/NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
contracts/governance/NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
contracts/governance/NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
contracts/governance/NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
contracts/governance/NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
contracts/governance/NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
contracts/governance/NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
contracts/governance/NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
contracts/governance/NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
contracts/governance/NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
contracts/governance/NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
contracts/governance/NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
contracts/governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
contracts/governance/NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
contracts/governance/NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
contracts/governance/NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
contracts/governance/NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
contracts/governance/NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
contracts/governance/NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
contracts/governance/NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
contracts/governance/NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
contracts/governance/NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
contracts/governance/NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
contracts/governance/NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
contracts/governance/NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
contracts/governance/NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
contracts/governance/NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
contracts/governance/NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
contracts/governance/NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
contracts/governance/NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
contracts/governance/NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
contracts/governance/NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
contracts/governance/NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
contracts/governance/NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
contracts/governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
contracts/governance/NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
contracts/governance/NounsDAOLogicV2.sol:853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
contracts/base/ERC721Enumerable.sol:62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
contracts/base/ERC721Enumerable.sol:77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
contracts/base/ERC721Checkpointable.sol:140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
contracts/base/ERC721Checkpointable.sol:141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
contracts/base/ERC721Checkpointable.sol:142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
contracts/base/ERC721Checkpointable.sol:164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```
## 6. Reduce the size of error messages (Long revert Strings)
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.

Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

### Instances:
// Links to github file
[NounsDAOProxy.sol:L79](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L79)
[NounsDAOProxy.sol:L80](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L80)
[NounsDAOLogicV1.sol:L122](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L122)
[NounsDAOLogicV1.sol:L123](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L123)
[NounsDAOLogicV1.sol:L124](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L124)
[NounsDAOLogicV1.sol:L125](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L125)
[NounsDAOLogicV1.sol:L197](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L197)
[NounsDAOLogicV1.sol:L198](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L198)
[NounsDAOLogicV1.sol:L336](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L336)
[NounsDAOLogicV1.sol:L364](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L364)
[NounsDAOLogicV1.sol:L365](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L365)
[NounsDAOLogicV1.sol:L366](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L366)
[NounsDAOLogicV1.sol:L422](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L422)
[NounsDAOLogicV1.sol:L485](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L485)
[NounsDAOLogicV1.sol:L501](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L501)
[NounsDAOLogicV1.sol:L502](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L502)
[NounsDAOLogicV1.sol:L505](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L505)
[NounsDAOLogicV1.sol:L530](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L530)
[NounsDAOLogicV1.sol:L546](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L546)
[NounsDAOLogicV1.sol:L563](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L563)
[NounsDAOLogicV1.sol:L581](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L581)
[NounsDAOLogicV1.sol:L599](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L599)
[NounsDAOLogicV1.sol:L617](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L617)
[NounsDAOLogicV1.sol:L638](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L638)
[NounsDAOLogicV1.sol:L651](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L651)
[NounsDAOLogicV2.sol:L133](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L133)
[NounsDAOLogicV2.sol:L134](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L134)
[NounsDAOLogicV2.sol:L135](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L135)
[NounsDAOLogicV2.sol:L136](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L136)
[NounsDAOLogicV2.sol:L207](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L207)
[NounsDAOLogicV2.sol:L208](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L208)
[NounsDAOLogicV2.sol:L347](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L347)
[NounsDAOLogicV2.sol:L375](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L375)
[NounsDAOLogicV2.sol:L376](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L376)
[NounsDAOLogicV2.sol:L377](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L377)
[NounsDAOLogicV2.sol:L433](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L433)
[NounsDAOLogicV2.sol:L577](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L577)
[NounsDAOLogicV2.sol:L593](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L593)
[NounsDAOLogicV2.sol:L594](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L594)
[NounsDAOLogicV2.sol:L597](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L597)
[NounsDAOLogicV2.sol:L622](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L622)
[NounsDAOLogicV2.sol:L638](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L638)
[NounsDAOLogicV2.sol:L655](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L655)
[NounsDAOLogicV2.sol:L674](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L674)
[NounsDAOLogicV2.sol:L702](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L702)
[NounsDAOLogicV2.sol:L727](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L727)
[NounsDAOLogicV2.sol:L801](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L801)
[NounsDAOLogicV2.sol:L819](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L819)
[NounsDAOLogicV2.sol:L840](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L840)
[NounsDAOLogicV2.sol:L853](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L853)
[ERC721Enumerable.sol:L62](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L62)
[ERC721Enumerable.sol:L77](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L77)
[ERC721Checkpointable.sol:L140](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L140)
[ERC721Checkpointable.sol:L141](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L141)
[ERC721Checkpointable.sol:L142](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L142)
[ERC721Checkpointable.sol:L164](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L164)





```
contracts/governance/NounsDAOProxy.sol:79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
contracts/governance/NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
contracts/governance/NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
contracts/governance/NounsDAOLogicV1.sol:123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
contracts/governance/NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
contracts/governance/NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
contracts/governance/NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
contracts/governance/NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
contracts/governance/NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
contracts/governance/NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
contracts/governance/NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
contracts/governance/NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
contracts/governance/NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
contracts/governance/NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
contracts/governance/NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
contracts/governance/NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
contracts/governance/NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
contracts/governance/NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
contracts/governance/NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
contracts/governance/NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
contracts/governance/NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
contracts/governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
contracts/governance/NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
contracts/governance/NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
contracts/governance/NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
contracts/governance/NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
contracts/governance/NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
contracts/governance/NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
contracts/governance/NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
contracts/governance/NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
contracts/governance/NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
contracts/governance/NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
contracts/governance/NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
contracts/governance/NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
contracts/governance/NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
contracts/governance/NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
contracts/governance/NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
contracts/governance/NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
contracts/governance/NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
contracts/governance/NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
contracts/governance/NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
contracts/governance/NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
contracts/governance/NounsDAOLogicV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
contracts/governance/NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
contracts/governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
contracts/governance/NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
contracts/governance/NounsDAOLogicV2.sol:853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
contracts/base/ERC721Enumerable.sol:62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
contracts/base/ERC721Enumerable.sol:77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
contracts/base/ERC721Checkpointable.sol:140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
contracts/base/ERC721Checkpointable.sol:141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
contracts/base/ERC721Checkpointable.sol:142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
contracts/base/ERC721Checkpointable.sol:164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```

## 7.`ABI.ENCODE()` IS LESS EFFICIENT THAN `ABI.ENCODEPACKED()`
`abi.encode` will apply ABI encoding rules. Therefore all elementary types are padded to `32` bytes and dynamic arrays include their length. Therefore it is possible to also decode this data again (with `abi.decode`) when the type are known.

`abi.encodePacked` will only use the only use the minimal required memory to encode the data. E.g. an address will only use `20` bytes and for dynamic arrays only the elements will be stored without length

### Instances
//Links to githubfile 
[NounsDAOProxy.sol:L57](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L57)
[NounsDAOLogicV1.sol:L302](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L302)
[NounsDAOLogicV1.sol:L480](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L480)
[NounsDAOLogicV1.sol:L482](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L482)
[NounsDAOLogicV1.sol:L483](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L483)
[NounsDAOLogicV2.sol:L313](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L313)
[NounsDAOLogicV2.sol:L572](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L572)
[NounsDAOLogicV2.sol:L574](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L574)
[NounsDAOLogicV2.sol:L575](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L575)
[ERC721Checkpointable.sol:L135](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L135)
[ERC721Checkpointable.sol:L137](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L137)
[ERC721Checkpointable.sol:L138](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L138)



```
// actual codes used
contracts/governance/NounsDAOProxy.sol:57:            abi.encodeWithSignature(
contracts/governance/NounsDAOLogicV1.sol:302:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
contracts/governance/NounsDAOLogicV1.sol:480:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
contracts/governance/NounsDAOLogicV1.sol:482:        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
contracts/governance/NounsDAOLogicV1.sol:483:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
contracts/governance/NounsDAOLogicV2.sol:313:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
contracts/governance/NounsDAOLogicV2.sol:572:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
contracts/governance/NounsDAOLogicV2.sol:574:        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
contracts/governance/NounsDAOLogicV2.sol:575:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
contracts/base/ERC721Checkpointable.sol:135:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
contracts/base/ERC721Checkpointable.sol:137:        bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
contracts/base/ERC721Checkpointable.sol:138:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
```

## 8. SPLITTING `REQUIRE()` STATEMENTS THAT USE `&&` SAVES GAS
which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper
//Links to githubfile
[NounsDAOLogicV1.sol:L617](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L617)
[NounsDAOLogicV2.sol:L819](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L819)

```
//actual codes used
contracts/governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
contracts/governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```


## 9.USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value.

### Instances
// Links to githubfiles
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L36
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L33
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L61
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L53
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L28
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L35



```
2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
2022-08-nounsdao/contracts/base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
```

## 10. Internal function should be called once 
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.
### Instances
//Links to githubfile 

[NounsDAOLogicV1.sol:L500](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L500)
[NounsDAOLogicV1.sol:L672](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L672)
[NounsDAOLogicV1.sol:L676](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L676)
[NounsDAOLogicV2.sol:L592](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L592)
[NounsDAOLogicV2.sol:L866](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L866)
[NounsDAOLogicV2.sol:L988](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L988)
[NounsDAOLogicV2.sol:L1006](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1006)
[NounsDAOLogicV2.sol:L1010](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1010)
[NounsDAOLogicV2.sol:L1018](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1018)
[NounsDAOLogicV2.sol:L1023](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1023)
[ERC721Checkpointable.sol:L253](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L253)
[ERC721Checkpointable.sol:L258](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L258)
[ERC721Checkpointable.sol:L267](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L267)
[ERC721Checkpointable.sol:L277](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L277)
[ERC721Checkpointable.sol:L282](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L282)



```
contracts/governance/NounsDAOLogicV1.sol:500:    ) internal returns (uint96) {
contracts/governance/NounsDAOLogicV1.sol:672:    function bps2Uint(uint256 bps, uint256 number) internal pure returns (uint256) {
contracts/governance/NounsDAOLogicV1.sol:676:    function getChainIdInternal() internal view returns (uint256) {
contracts/governance/NounsDAOLogicV2.sol:592:    ) internal returns (uint96) {
contracts/governance/NounsDAOLogicV2.sol:866:    function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
contracts/governance/NounsDAOLogicV2.sol:988:    function min(uint256 a, uint256 b) internal pure returns (uint256) {
contracts/governance/NounsDAOLogicV2.sol:1006:    function bps2Uint(uint256 bps, uint256 number) internal pure returns (uint256) {
contracts/governance/NounsDAOLogicV2.sol:1010:    function getChainIdInternal() internal view returns (uint256) {
contracts/governance/NounsDAOLogicV2.sol:1018:    function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
contracts/governance/NounsDAOLogicV2.sol:1023:    function safe16(uint256 n) internal pure returns (uint16) {
contracts/base/ERC721Checkpointable.sol:253:    function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
contracts/base/ERC721Checkpointable.sol:258:    function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {
contracts/base/ERC721Checkpointable.sol:267:    ) internal pure returns (uint96) {
contracts/base/ERC721Checkpointable.sol:277:    ) internal pure returns (uint96) {
contracts/base/ERC721Checkpointable.sol:282:    function getChainId() internal view returns (uint256) {
```





