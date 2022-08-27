## Non critical risk

### Using old version of solidity


Consider change version of solidity like 0.8.13

### typo in `MAX_QUORUM_VOTES_BPS_UPPER_BOUND` comment

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86

Change to  `6,000` basis points or 60%

### using function in event

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L557

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L578

Call function before emit event.
This is for code clarity.

