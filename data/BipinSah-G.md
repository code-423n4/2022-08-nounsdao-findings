## 1. ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as is the case when used in for- and while-loops

The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)
### Instances
// Links to Github file

https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L371
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L382

Actual codes used
```
contracts/governance/NounsDAOLogicV1.sol:281: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:319: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:346: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV1.sol:371: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:292: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:330: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:357: for (uint256 i = 0; i < proposal.targets.length; i++) {
contracts/governance/NounsDAOLogicV2.sol:382: for (uint256 i = 0; i < proposal.targets.length; i++) {

```
----

## 2.`<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

The overheads outlined below are `PER LOOP`, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas) memory arrays use `MLOAD` (3 gas)
call data arrays use `CALLDATALOAD` (3 gas) Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset
### Instances
// Links to Github files
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

----
## 3.UNCHECKED{++I} INSTEAD OF I++ (OR USE ASSEMBLY WHEN APPLICABLE)
Use `++i` instead of `i++`. This is especially useful in for loops but this optimization can be used anywhere in your code. You can also use `unchecked{++i;}` for even more gas savings but this will not check to see if `i` overflows. For extra safety if you are worried about this, you can add a required statement after the loop checking if `i` is equal to the final incremented value. For best gas savings, use inline assembly, however this limits the functionality you can achieve. For example you cant use Solidity syntax to internally call your own contract within an assembly block and external calls must be done with the `call()` or `delegatecall()` instruction. However, when applicable, the inline assembly will save much more gas.
### Instances
//Links to github file
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

----
## 4.++I COSTS LESS GAS COMPARED TO I++ OR I += 1
*Pre-increments and pre-decrements are cheaper.*

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

----
## 5. Variables: No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

We can use `uint number;` instead of `uint number = 0;`

### Instance
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L948
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L41
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L180
```
contracts/governance/NounsDAOLogicV2.sol:948:        uint256 lower = 0;
contracts/base/ERC721Checkpointable.sol:41:    uint8 public constant decimals = 0;
contracts/base/ERC721Checkpointable.sol:180:        uint32 lower = 0;
``` 
### Recommendation:

I suggest removing explicit initializations for default values.


---
