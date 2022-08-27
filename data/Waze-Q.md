#1 Missing natspec comment castVoteBySig().

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L561

castVoteBySig() was missing natspec comment. add natspec comment to castVoteBySig() to give knowledge to the user about the function and params

#2 Missing natspec comment newVeteor 

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L839

A function has a natspec comment to explain utility about function or parameter but natspec comment newVeteor is missing.
So i suggest to add natspec comment for parameter newVeteor. 

#3 incorrect comment

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L852

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L650-L651

The comment and code is not match. It makes user confuse. We suggest to remove the comment because the comment irrelevant with the function

#4 Missing indexed field for proposer

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L50

Each event should use indexed fields if there have any important param. add indexed in proposer.

#5 Missing check address(0) for immutable address

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L41-L71

constructor have five params address, so to avoid vulnerability we suggest to consider add simple check address(0) for the params
