1. Upper bound of maximum quorum votes basis point comment was uncorrect 

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86

The ammount of correct basis point was `6000` and the comment was using `4000` (which is wrong), so it should be changed instead.

2. Needed Modifier OnlyAdmin()

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol

This contract should be needed modifier OnlyAdmin, eversince some of fn was needed to be used this case, like an fn [_acceptAdmin()](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L615) and others

3. Event is missing indexed fields

Each event should be using three indexed fields if there are three or more fields

1.) https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#L70

2.) https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#L123

indexed amount

3.) https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#L103

indexed oldAdmin, indexed newAdmin

4.) https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#L106

indexed oldVetoer, indexed newVetoer

4. nonreentrant check pattern

purpose - https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174 

5. Avoid Floatin Pragma's

Since it was used ^0.8.6. As the compiler can be use for example 0.8.x and consider locking at this version the same as another. It can be consider using  locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. Since it can be problematic, if there are publicly disclosed bugs and issues that affect the current compiler version used.

6. Uncleared Comment 

Files :

1.) https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L111

Double `**` , deleted one *

2.) https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L658

3.) https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L666

`Differ` can be changed into `differ` since it was part of above and not a variable so it should declared like that






