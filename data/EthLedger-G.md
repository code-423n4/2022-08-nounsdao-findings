# Gas Optimizations:

## [1] Use custom errors instead of require()

Wherever require is used, it is better to use custom errors instead. Take the line below for example:

```require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');```

It would be better to declare an error variable first, and then use that instead of require, like this:

```
error Unauthorized();

if(msg.sender != admin){
   revert Unauthorized();
}
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79

## [2] When there is no possibility for overflow (such as in for loops), use unchecked

```
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

The above line can be altered as follows:

```
for (uint256 i = 0; i < proposal.targets.length; unchecked{i++}) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319

## [3] ++i is more gas efficient than i++

Insead of 
```
for (uint256 i = 0; i < proposal.targets.length; i++) {
```
use this:
```
for (uint256 i = 0; i < proposal.targets.length; unchecked{++i}) {
```

## [4] Public functions not used internally should be declared external

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L133
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61
