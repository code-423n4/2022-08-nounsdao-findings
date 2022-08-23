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

source: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79