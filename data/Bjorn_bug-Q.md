### Low Risk and Non-Critical Issues
#### [L-01] Missing checks for <strong> address(0x0) </strong>  when assigning values to <strong>address</strong>
##### There are 16 instances: 
File: contracts/governance/NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L116
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L412
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L597
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L637

File: contracts/governance/NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L127
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L423
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L589
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839

File: contracts/governance/NounsDAOProxy.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94

File: contracts/base/ERC721Checkpointable.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L79
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L151
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163

File: contracts/base/ERC721Enumerable.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L120
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L143

#### [L-02] Use of floating pragma 
##### Impact : 
##### There are 8 instances: 
The contracts should be deployed with the same solidity version with which they have been tested.
Locking the pragma ensures that the contracts don't get deployed with an older version which might introduce bugs.

File: contracts/governance/NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L61

File: contracts/governance/NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L53

File: contracts/governance/NounsDAOInterfaces.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L33

File: contracts/governance/NounsDAOProxy.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L36

File: contracts/base/ERC721Enumerable.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28

File: contracts/base/ERC721Checkpointable.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L35

#### [L-03] Inconsistent solidity pragma
the source files have different solidity compilers. This leads to security flaws between deployed contracts
Use 0.8.0 pragma solidity in File: contracts/base/ERC721Enumerable.sol, while 0.8.6 is used in all contracts in File: contracts/governance/



