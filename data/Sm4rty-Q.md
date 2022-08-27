
## 1. Unused receive() function will lock Ether in contract

If the intention is for the Ether to be used, the function should call another function, otherwise, it should revert.

### Instances:
[NounsDAOLogicV2.sol:L1030](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1030)

```
contracts/governance/NounsDAOLogicV2.sol:1030:    receive() external payable {}
``` 
### References

[https://code4rena.com/reports/2022-05-sturdy#l-06-unused-receive-function-will-lock-ether-in-contract](https://code4rena.com/reports/2022-05-sturdy#l-06-unused-receive-function-will-lock-ether-in-contract)


-----

## 2. Avoid using Floating Pragma:

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Instances:
[NounsDAOLogicV2.sol:L53](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L53)
[NounsDAOInterfaces.sol:L33](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L33)
[NounsDAOLogicV1.sol:L61](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L61)
[NounsDAOProxy.sol:L36](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L36)
[ERC721Checkpointable.sol:L35](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L35)
[ERC721Enumerable.sol:L28](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol#L28)

```
contracts/governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
contracts/governance/NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
contracts/governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
contracts/governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
contracts/base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
contracts/base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
``` 
Recommend using fixed solidity version

### References:

[https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity](https://code4rena.com/reports/2022-04-phuture#g-20-use-a-more-recent-version-of-solidity)


-----
