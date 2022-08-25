Table Of Content
================

* [GAS REPORT](#gas-report)
        * [Use custom errors](#use-custom-errors)
        * [Using abiEncodePacked() is more efficient that abiEncode()](#using-abiencodepacked-is-more-efficient-that-abiencode)
        * [Use assembly opcodes iszero instead of solidity equation to save gas](#use-assembly-opcodes-iszero-instead-of-solidity-equation-to-save-gas)
        * [If the function is onlyOwner you may make it payable to reduce gas usage.](#if-the-function-is-onlyowner-you-may-make-it-payable-to-reduce-gas-usage)
        * [Split require statement with & operator](#split-require-statement-with--operator)
        * [Replace transferFrom(this, ...) with transfer(...)](#replace-transferfromthis--with-transfer)

# GAS REPORT

## Use custom errors
In the following require statements you can use custom errors to save gas and improve code quality.

### Code Instances:
- [NounsDAOExecutor.sol#L152](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L152)
- [NounsAuctionHouse.sol#L225](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L225)
- [NounsAuctionHouse.sol#L223](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L223)

## Using abiEncodePacked() is more efficient that abiEncode()


### Code Instances:
- [NounsDescriptorV2.t.sol#L158](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L158)
- [NounsDescriptorV2.t.sol#L420](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L420)
- [NounsDAOExecutor.sol#L135](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L135)
- [NounsDescriptorV2.t.sol#L482](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L482)
- [NounsDescriptorV2.t.sol#L477](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L477)
- [NounsDescriptorV2.t.sol#L457](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L457)
- [NounsDAOLogicV2.sol#L570](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L570)
- [NounsDescriptorV2.t.sol#L479](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L479)
- [NounsDescriptorV2.t.sol#L426](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L426)
- [NounsDescriptorV2.t.sol#L168](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L168)
- [NounsDescriptorV2.t.sol#L458](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L458)
- [NounsDescriptorV2.t.sol#L448](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L448)
- [NounsDescriptorV2.t.sol#L480](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L480)
- [NounsDescriptorV2.t.sol#L455](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L455)
- [NounsDAOLogicV1.sol#L481](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L481)
- [NounsDescriptorV2.t.sol#L456](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L456)
- [NounsDAOLogicV1.sol#L300](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L300)
- [NounsDAOLogicV2.sol#L573](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L573)

## Use assembly opcodes iszero instead of solidity equation to save gas


### Code Instances:
- [NounsArt.sol#L155](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L155)
- [NounsDAOLogicV2.sol#L601](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L601)
- [NounsDAOLogicV2.sol#L976](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L976)
- [NounsDAOLogicV1.sol#L509](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L509)
- [NounsDAOLogicV2.sol#L866](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L866)
- [NounsDAOLogicV2.sol#L878](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L878)
- [NounsArt.sol#L398](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L398)
- [NounsDAOExecutor.sol#L165](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L165)

## If the function is onlyOwner you may make it payable to reduce gas usage.


### Code Instances:
- [NounsDescriptorV2.sol#L254](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L254)
- [NounsArt.sol#L324](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L324)
- [NounsDescriptor.sol#L194](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L194)
- [NounsArt.sol#L286](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L286)
- [NounsToken.sol#L206](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L206)
- [NounsDescriptor.sol#L218](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L218)
- [NounsArt.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L142)
- [NounsDescriptorV2.sol#L224](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L224)
- [NounsToken.sol#L128](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L128)
- [NounsDescriptor.sol#L149](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L149)
- [NounsAuctionHouse.sol#L185](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L185)
- [NounsArt.sol#L233](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L233)
- [NounsDescriptorV2.sol#L288](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L288)
- [NounsDescriptor.sol#L242](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L242)
- [NounsDescriptorV2.sol#L78](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L78)
- [NounsArt.sol#L155](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L155)
- [NounsDescriptorV2.sol#L391](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L391)
- [NounsDescriptor.sol#L178](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L178)
- [NounsDescriptorV2.sol#L141](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L141)
- [NounsDescriptor.sol#L229](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L229)
- [NounsDescriptorV2.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L68)
- [NounsDescriptor.sol#L159](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L159)
- [NounsDescriptorV2.sol#L237](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L237)
- [NounsArt.sol#L197](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L197)
- [NounsDescriptor.sol#L119](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L119)
- [NounsDescriptor.sol#L210](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L210)
- [NounsToken.sol#L196](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L196)
- [NounsArt.sol#L248](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L248)
- [NounsAuctionHouse.sol#L175](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L175)
- [NounsDescriptorV2.sol#L89](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L89)
- [NounsDescriptorV2.sol#L160](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L160)
- [NounsArt.sol#L215](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L215)

## Split require statement with & operator
instead of require(A & B, ...) consider having two require statements require(A, ...) and require(B, ...) for better code quality and improved gas usage.

### Code Instances:
- [NounsDAOLogicV2.sol#L136](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L136)
- [NounsDAOLogicV2.sol#L655](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L655)
- [NounsDAOLogicV1.sol#L125](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L125)
- [NounsDAOLogicV1.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L137)
- [NounsDAOLogicV2.sol#L144](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L144)
- [NounsDAOLogicV2.sol#L140](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L140)
- [NounsDAOLogicV1.sol#L133](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L133)
- [NounsDAOLogicV2.sol#L676](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L676)
- [NounsDAOLogicV1.sol#L546](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L546)
- [NounsDAOLogicV1.sol#L563](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L563)

## Replace transferFrom(this, ...) with transfer(...)
transferFrom(this, ...) and transfer(...) has the same meaning while transfer cost less gas.

For instance, [NounsAuctionHouse.sol#L232](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L232)

--