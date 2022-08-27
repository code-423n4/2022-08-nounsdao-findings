1.	`MAX_QUORUM_VOTES_BPS` constant is not used in the code. It should be removed.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L89
2.	There is no need of struct `ProposalTemp`. It is used only in the propose method. Use params in the method instead of struct and save gas, that you don’t need to store struct to the memory and only use params from stack.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L167
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L191
3.	Revert when there is no such proposal instead of returning zero values.
Add check like `require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');`
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L403
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L423
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L462
