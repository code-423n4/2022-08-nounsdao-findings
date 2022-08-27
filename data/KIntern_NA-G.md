# [2022-08-nounsdao] Gas optimization 

###### tags: `c4`, `2022-08-nounsdao`, `QA`

## unnecessary check if `vetoer != address(0)` or not 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L364

## unnecessary check `msg.sender != address(0)` 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L617

## typo `veteor` -> `vetoer`
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L46

## `quorumVotesBPS` is always equal to zero 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L942-L943

In `NounsDaoLogicV2`, variable `quorumBPS` can't be changed since there is no function to update this variable. 
--> Delete it if not necessary