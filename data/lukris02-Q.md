# QA Report for Nouns DAO contest

## Overview
During the audit, 3 non-critical issues were found.

№ | Title | Risk Rating  | Instance Count
--- | --- | --- | ---
NC-1 | [Order of Functions](#nc-1-order-of-functions) | Non-Critical | 5+
NC-2 | [Floating pragma](#nc-2-floating-pragma) | Non-Critical | 6
NC-3 | [Scientific notation](#nc-3-scientific-notation) | Non-Critical | 3

## Non-Critical Risk Findings (3)
### NC-1. Order of Functions
##### Description
fallback() and receive() functions are placed after internal functions. External functions are placed after public.

##### Instances
- contracts/base/ERC721Checkpointable.sol: [Line 151](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L151).
- contracts/governance/NounsDAOProxy.sol: [Line 130](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L130), [Line 138](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L138).
- contracts/governance/NounsDAOLogicV1.sol: [Line 274](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L274) and more.
- contracts/governance/NounsDAOLogicV2.sol: [Line 285](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L285) and more.

##### Recommendation
According to [Style Guide](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions), ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier.  
Functions should be grouped according to their visibility and ordered:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private

#
### NC-2. Floating pragma
##### Description
Contracts should be deployed with the same compiler version. It helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

##### Instances
- contracts/governance/NounsDAOLogicV2.sol: [```pragma solidity ^0.8.6;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L53)
- contracts/governance/NounsDAOLogicV1.sol: [```pragma solidity ^0.8.6;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L61)
- contracts/governance/NounsDAOInterfaces.sol: [```pragma solidity ^0.8.6;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L33)
- contracts/governance/NounsDAOProxy.sol: [```pragma solidity ^0.8.6;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L36)
- contracts/base/ERC721Checkpointable.sol: [```pragma solidity ^0.8.6;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L35)  
- contracts/base/ERC721Enumerable.sol: [```pragma solidity ^0.8.0;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L28)

##### Recommendation
According to [SWC-103](https://swcregistry.io/docs/SWC-103), pragma version should be locked.

#
### NC-3. Scientific notation
##### Description
For readability, it is better to use scientific notation.

##### Instances
- contracts/governance/NounsDAOLogicV2.sol: [```uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L908)
- contracts/governance/NounsDAOLogicV2.sol: [```return (number * bps) / 10000;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1007)
- contracts/governance/NounsDAOLogicV1.sol: [```return (number * bps) / 10000;```](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L673)

##### Recommendation
Replace ```10000``` with ```10e4```.