1.	Don’t initialize to zero. By default it’s cheaper.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41
2.	Use `uint256` instead of `uint8`.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41
3.	Use `external` modifier instead of `public` to save gas.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L76
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L184
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L862
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1002
4.	It’s cheaper to separate `require` that uses && statements.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677
5.	Use `++i` or `unchecked{++i}` instead of `i++`.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
6.	Don’t check length of storage array in the loop, create memory param before instead and use it in the loop.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
7.	Don’t assign default values to the storage param. It’s done by default with no gas cost.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L231
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L238-L243
8.	Use function variables instead of storage value.
Change `latestProposalIds[newProposal.proposer] = newProposal.id;` to `latestProposalIds[msg.sender]=proposalCount;`
Change `return newProposal.id;` to `return proposalCount;`
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L247
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L278
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L247-L278
9.	Use `param!= 0` instead of `param>0` for unsigned types.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L541
