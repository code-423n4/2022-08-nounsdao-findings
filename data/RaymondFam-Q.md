## Voter Refund
In rules 3 and 4, partial or no refund will be provided when contract runs very low in balance or no balance.
To avoid voter grievances, I suggest implementing chainlink keeper to auto-fund contract with ETH when the contract balance is low.
## Typo Mistake
[Line 85 to 86](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L85-L86)
The comment is wrong. Instead of 4,000 basis points, it should be 6,000.
## _withdraw() function
For best practices, the function should revert when address(this).balance == 0, and / or revert when ```bool sent``` is false.
Note: Not checking bool refundSent on ```function _refundGas``` is understandable so that ```function castRefundableVoteInternal``` would still go through  regardless of the internal refund status.