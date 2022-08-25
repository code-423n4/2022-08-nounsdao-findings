
# GAS REPORT

## [GAS] Use assembly opcodes iszero in the following locations


### Proof of concept:
- [NounsDAOLogicV2.sol#L866](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L866)
- [NounsArt.sol#L155](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L155)

## [GAS] Use bytes32 in the following locations


### Proof of concept:
- [DescriptorUpgrade.t.sol#L77](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgrade.t.sol#L77)
- [SVGRenderer.sol#L52](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L52)

## [GAS] Use abiEncodePacked()


### Proof of concept:
- [NounsDescriptorV2.t.sol#L480](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L480)
- [NounsDescriptorV2.t.sol#L454](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L454)

## [GAS] In the following revert statements consider using custom error instead a message


### Proof of concept:
- [NounsAuctionHouse.sol#L223](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L223)
- [NounsDAOExecutor.sol#L152](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L152)

## [GAS] Cache array size
You can cache the array size to improve gas usage in the following locations

### Proof of concept:
- [NounsArt.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L130)
- [NounsDAOLogicV1.sol#L370](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L370)

## [GAS] Do not cache msg.sender since loading msg.sender is more efficient than a local variable


### Proof of concept:
- [NounsArt.t.sol#L253](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L253)
- [NounsDAOExecutor.sol#L148](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L148)

--
