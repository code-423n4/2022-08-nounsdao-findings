## 1. PRAGMA VERSION
In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

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

## 2.Multiple initialization due to initialize function not having initializer modifier.

### Description

The attacker can initialize the contract, take malicious actions, and allow it to be re-initialized by the project without any error being noticed.

### Instances
//Links to githubfile
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L124
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L113
```
contracts/governance/NounsDAOLogicV2.sol:124:    function initialize(
contracts/governance/NounsDAOLogicV1.sol:113:    function initialize(
```
## 3. UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert (e.g. require(msg.sender == address(weth)))

There are 1 instances of this issue:
//Links to github files

[NounsDAOLogicV2.sol:L1030](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1030)

```
contracts/governance/NounsDAOLogicV2.sol:1030:    receive() external payable {}
```

## 4.`EVENT` IS MISSING INDEXED FIELDS

Each event should use three indexed fields if there are three or more fields
### Instances
Their are 3 instances
[NounsDAOInterfaces.sol:L70](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L70)
[NounsDAOInterfaces.sol:L120](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol#L120)
[ERC721Checkpointable.sol:L73](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L73)

```
contracts/governance/NounsDAOInterfaces.sol:70:    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);
contracts/governance/NounsDAOInterfaces.sol:120:    event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);
contracts/base/ERC721Checkpointable.sol:73:    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
```