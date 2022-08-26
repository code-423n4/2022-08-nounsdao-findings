1). Unlocked pragma versions

instances: [1](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L36), [2](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L53), [3](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L61), [4](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L33)

t's good practice to use specific solidity version to know compiler bug fixes and optimisations were enabled at the time of compiling the contract.

2). Add Zero address validation

[NounsDAOLogicV1.sol#L116](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L116)

3). use 0.8.6 for `ERC721Enumerable` as it uses 0.8.0

[ERC721Enumerable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L28)

4). `ecrecover` directly used to verify the signture

[NounsDAOLogicV1.sol#L484](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L484)

would recommend to using the ECDSA from openzeppelin as it does more validations when verifying the signature

5). emit `admin` address on `withdraw`

[NounsDAOLogicV2.sol#L791](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L791)
