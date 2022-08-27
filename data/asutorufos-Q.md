N-1 EVENT IS MISSING INDEXED FIELDS
Each event should use three indexed fields if there are three or more fields

[NounsDAOInterfaces,sol L#50]https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20ProposalCreatedWithRequirements(

[NounsDAOInterfaces.sol L#70](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20VoteCast(address%20indexed%20voter%2C%20uint256%20proposalId%2C%20uint8%20support%2C%20uint256%20votes%2C%20string%20reason)%3B)

[NounsDAOInterfaces.sol L#120](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20RefundableVote(address%20indexed%20voter%2C%20uint256%20refundAmount%2C%20bool%20refundSent)%3B)


N-2 Shows changes for an event before you change of vetoers address
[NounsDAOLogicv2.sol l#839]https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#:~:text=function%20_setVetoer(,%7D

N-3 USE A MORE RECENT VERSION OF SOLIDITY
Use a solidity version of at least 0.8.13 to get the ability to use `using for` with a list of free functions


L-1 UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert 
[NounsDAOLogicV@.sol L#1030](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#:~:text=receive()%20external%20payable%20%7B%7D)