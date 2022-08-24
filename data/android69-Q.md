Vulnerable Endpoint : https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
Line Number 489 to 490

Vulnerability - Within the function castVote improper validations found for 'proposalId' and 'support' params. It is not being checked whether 'proposalId' is empty or not and no validation for 'support' i.e. not checking if it only contains "0=against, 1=for, 2=abstain" or not.