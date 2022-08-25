# QA REPORT

## [QA 00] Improving precision at the following calculations by preferring a mathematical multiplication instead division
inequation of the form ```x / y < z``` can be transformed into ```x < z * y``` to increase precision.

### Proof of concept:
- [NounsAuctionHouse.sol#L109](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L109)

## [QA 01] Use safeTransfer instead transfer in the following locations


### Proof of concept:
- [NounsAuctionHouse.sol#L248](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L248)
- [DeployUtils.sol#L66](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/helpers/DeployUtils.sol#L66)
- [NounsAuctionHouse.sol#L232](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L232)

## [QA 02] Some tokens needs approve 0 before any approve above 0
Consider using increase/decrease approve notation instead approve to deal with that.

### Proof of concept:
- [NounsToken.sol#L139](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L139)

## [QA 03] Timelock is missing for the following functions


### Proof of concept:
- [DescriptorUpgrade.t.sol#L20](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgrade.t.sol#L20)
- [NounsDescriptorV2.sol#L98](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L98)
- [NounsArt.sol#L155](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L155)
- [NounsArt.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L79)
- [NounsDescriptorV2.sol#L78](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L78)
- [NounsToken.sol#L196](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L196)
- [NounsAuctionHouse.sol#L165](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L165)
- [NounsAuctionHouse.sol#L175](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L175)
- [NounsAuctionHouse.sol#L96](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L96)
- [NounsToken.sol#L128](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L128)

## [QA 04] Use SafeMath in the following contracts to avoid unexpected overflow/underflow


### Proof of concept:
- [Multicall2.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/Multicall2.sol)

## [QA 05] Use two steps verification for the following contracts


### Proof of concept:
- [NounsDescriptorV2.t.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol)

## [QA 06] Put hte nonReentrancy modifier first in the following functions
A best practice is to put the nonReentrancy modifier first.

### Proof of concept:
- [NounsAuctionHouse.sol#L96](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L96)

## [QA 07] Missing event indexers


### Proof of concept:
- [NounsArt.t.sol#L27](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L27)
- [NounsArt.t.sol#L19](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L19)
- [NounsArt.t.sol#L15](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L15)
- [NounsArt.t.sol#L21](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L21)

## [QA 08] Remove the name from the following unused function parameters


### Proof of concept:
- [NounsDAOExecutorHarness.sol#L24](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/NounsDAOExecutorHarness.sol#L24)
- [NounsAuctionHouseProxy.sol#L27](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/proxies/NounsAuctionHouseProxy.sol#L27)
- [NounsTokenHarness.sol#L19](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/NounsTokenHarness.sol#L19)

## [QA 09] Both return statement and named return
For readability purposes consider having one of the two return options (for the following functions)

### Proof of concept:
- [SVGRenderer.sol#L50](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L50)
- [DescriptorHelpers.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/helpers/DescriptorHelpers.sol#L79)
- [NounsDAOLogicV1.sol#L401](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L401)
- [SVGRenderer.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L68)

## [QA 10] Not emitted event for state changes


### Proof of concept:
- [NounsArt.t.sol#L33](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L33)
- [NounsToken.t.sol#L18](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsToken.t.sol#L18)
- [DescriptorUpgrade.t.sol#L20](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgrade.t.sol#L20)
- [NounsToken.sol#L109](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L109)
- [MaliciousVoter.sol#L18](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/MaliciousVoter.sol#L18)
- [NounsToken.sol#L128](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L128)

## [QA 11] Magic number, consider using named constant instead.


### Proof of concept:
- [NounsDescriptorV2.t.sol#L442](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L442)
- [NounsDescriptorV2.t.sol#L163](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L163)
- [SVGRenderer.sol#L168](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L168)
- [SVGRenderer.sol#L223](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L223)
- [NounsDescriptorV2.t.sol#L173](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L173)
- [SVGRenderer.sol#L224](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L224)
- [NounsDescriptorV2.t.sol#L143](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L143)
- [NounsDescriptorV2.t.sol#L432](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L432)
- [NounsAuctionHouse.sol#L257](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L257)
- [NounsAuctionHouse.sol#L109](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L109)
- [NounsDescriptorV2.t.sol#L420](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L420)
- [SVGRenderer.sol#L226](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L226)
- [NounsDescriptorV2.t.sol#L448](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L448)

--