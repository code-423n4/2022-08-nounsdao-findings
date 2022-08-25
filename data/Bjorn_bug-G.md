### Gas Optimizations 
#### [G-01] Public functions to external
##### Impact : 
Change public to external to save gas and improve code quality.
External call cost is less expensive than public functions.

##### There are 16 instances:
File: contracts/governance/NounsDAOLogicV1.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L174
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L668

File: contracts/governance/NounsDAOLogicV2.sol
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1002
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L184





