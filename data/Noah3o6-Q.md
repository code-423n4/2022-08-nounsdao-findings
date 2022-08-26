-> EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20ProposalCreated(,)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20ProposalCreatedWithRequirements(,)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20VoteCast(address%20indexed%20voter%2C%20uint256%20proposalId%2C%20uint8%20support%2C%20uint256%20votes%2C%20string%20reason)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=event%20DelegateVotesChanged(address%20indexed%20delegate%2C%20uint256%20previousBalance%2C%20uint256%20newBalance)%3B
