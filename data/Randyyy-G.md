1. Chached Array

##Impact

Loading length for storage arrays cost 100 gas and for memory arrays it costs 3 gas. When arr.length is defined as the condition of for loop, at the start of every iteration the length is loaded from memory. If the length doesn't change during the loop, loading the length of arrays repeatedly can be avoided by saving the length to the stack.

##Poc

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L292

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L357

2. i++ —> ++i

##Impact

++i cost less gas than i++, especially when its used in for-loops.

##POC

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L292

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L357
