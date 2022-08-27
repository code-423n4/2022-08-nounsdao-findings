# Nouns DAO Contest Gas Optimization Report

## Summary

The following gas optimization issues were found during the code audit:

1. Use `calldata` instead of `memory` (116 instances)
2. Cache `<array>.length` (23 instances)
3. Use `unchecked{}` to suppress overflow/underflow check (40 instances)
4. Long `require()`/`revert()` string (1 instances)
5. Using `bool`s for storage incurs overhead (25 instances)
6. Use `!= 0` instead of `> 0` when comparing uint (11 instances)
7. Empty blocks should be removed or at least emit an event (8 instances)
8. Don't initialize variables with default value (36 instances)
9. Use `++i`/`--i` instead of `i++`/`i--` (29 instances)
10. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (2 instances)
11. Use uint256/int256 instead of other variations (264 instances)
12. Use `abi.encodePacked()` instead of `abi.encode()` (36 instances)
13. Use `private` instead of `public` for constants (34 instances)
14. Use custom errors instead of `revert()`/`require()` strings (3 instances)
15. Use shift right/left instead of division/multiplication if possible (3 instances)

Total 631 instances of 15 issues.

## 1. Use `calldata` instead of `memory` (116 instances) - Completed

When a function with a `memory` array is called externally, the `abi.decode()` step has to use a for loop to copy each index of the `calldata` to the `memory` index. This overhead can be optimized by using `calldata` directly.

```solidity
2022-08-nounsdao/contracts/Inflator.sol::32 => function puff(bytes memory source, uint256 destlen) public pure returns (Inflate.ErrorCode, bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::92 => function getBodiesTrait() external view override returns (Trait memory) {

2022-08-nounsdao/contracts/NounsArt.sol::102 => function getAccessoriesTrait() external view override returns (Trait memory) {

2022-08-nounsdao/contracts/NounsArt.sol::112 => function getHeadsTrait() external view override returns (Trait memory) {

2022-08-nounsdao/contracts/NounsArt.sol::122 => function getGlassesTrait() external view override returns (Trait memory) {

2022-08-nounsdao/contracts/NounsArt.sol::340 => function heads(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::347 => function bodies(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::354 => function accessories(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::361 => function glasses(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::368 => function palettes(uint8 paletteIndex) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::411 => function imageByIndex(INounsArt.Trait storage trait, uint256 index) internal view returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::445 => function decompressAndDecode(INounsArt.NounArtStoragePage storage page) internal view returns (bytes[] memory) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::252 => function tokenURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::262 => function dataURI(uint256 tokenId, INounsSeeder.Seed memory seed) public view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::290 => function generateSVGImage(INounsSeeder.Seed memory seed) external view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::343 => function _getPartsForSeed(INounsSeeder.Seed memory seed) internal view returns (bytes[] memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::314 => function backgrounds(uint256 index) public view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::323 => function heads(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::332 => function bodies(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::341 => function accessories(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::350 => function glasses(uint256 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::359 => function palettes(uint8 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::401 => function tokenURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::411 => function dataURI(uint256 tokenId, INounsSeeder.Seed memory seed) public view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::439 => function generateSVGImage(INounsSeeder.Seed memory seed) external view override returns (string memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::450 => function getPartsForSeed(INounsSeeder.Seed memory seed) public view returns (ISVGRenderer.Part[] memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::467 => function _getPalette(bytes memory part) private view returns (bytes memory) {

2022-08-nounsdao/contracts/NounsSeeder.sol::28 => function generateSeed(uint256 nounId, INounsDescriptorMinimal descriptor) external view override returns (Seed memory) {

2022-08-nounsdao/contracts/NounsToken.sol::120 => function contractURI() public view returns (string memory) {

2022-08-nounsdao/contracts/NounsToken.sol::128 => function setContractURIHash(string memory newContractURIHash) external onlyOwner {

2022-08-nounsdao/contracts/NounsToken.sol::168 => function tokenURI(uint256 tokenId) public view override returns (string memory) {

2022-08-nounsdao/contracts/NounsToken.sol::177 => function dataURI(uint256 tokenId) public view override returns (string memory) {

2022-08-nounsdao/contracts/SVGRenderer.sol::50 => function generateSVG(SVGParams calldata params) external pure override returns (string memory svg) {

2022-08-nounsdao/contracts/SVGRenderer.sol::68 => function generateSVGPart(Part calldata part) external pure override returns (string memory partialSVG) {

2022-08-nounsdao/contracts/SVGRenderer.sol::78 => function generateSVGParts(Part[] calldata parts) external pure override returns (string memory partialSVG) {

2022-08-nounsdao/contracts/SVGRenderer.sol::86 => function _generateSVGRects(SVGParams memory params)

2022-08-nounsdao/contracts/SVGRenderer.sol::166 => function _getChunk(uint256 cursor, string[16] memory buffer) private pure returns (string memory) {

2022-08-nounsdao/contracts/SVGRenderer.sol::182 => function _decodeRLEImage(bytes memory image) private pure returns (DecodedImage memory) {

2022-08-nounsdao/contracts/SVGRenderer.sol::218 => function _toHexString(bytes memory b) private pure returns (string memory) {

2022-08-nounsdao/contracts/base/ERC721.sol::108 => function name() public view virtual override returns (string memory) {

2022-08-nounsdao/contracts/base/ERC721.sol::115 => function symbol() public view virtual override returns (string memory) {

2022-08-nounsdao/contracts/base/ERC721.sol::122 => function tokenURI(uint256 tokenId) public view virtual override returns (string memory) {

2022-08-nounsdao/contracts/base/ERC721.sol::134 => function _baseURI() internal view virtual returns (string memory) {

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::253 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::258 => function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::412 => function getReceipt(uint256 proposalId, address voter) external view returns (Receipt memory) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::423 => function getReceipt(uint256 proposalId, address voter) external view returns (Receipt memory) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::462 => function proposals(uint256 proposalId) external view returns (ProposalCondensed memory) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::922 => function getDynamicQuorumParamsAt(uint256 blockNumber_) public view returns (DynamicQuorumParams memory) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::964 => function _writeQuorumParamsCheckpoint(DynamicQuorumParams memory params) internal {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1018 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::94 => function delegateTo(address callee, bytes memory data) internal {

2022-08-nounsdao/contracts/governance/NounsDAOProxyV2.sol::97 => function delegateTo(address callee, bytes memory data) internal {

2022-08-nounsdao/contracts/interfaces/IInflator.sol::23 => function puff(bytes memory source, uint256 destlen) external pure returns (Inflate.ErrorCode, bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::79 => function palettes(uint8 paletteIndex) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::135 => function backgrounds(uint256 index) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::137 => function heads(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::139 => function bodies(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::141 => function accessories(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::143 => function glasses(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::145 => function getBodiesTrait() external view returns (Trait memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::147 => function getAccessoriesTrait() external view returns (Trait memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::149 => function getHeadsTrait() external view returns (Trait memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::151 => function getGlassesTrait() external view returns (Trait memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::34 => function baseURI() external returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::36 => function palettes(uint8 paletteIndex, uint256 colorIndex) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::38 => function backgrounds(uint256 index) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::40 => function bodies(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::42 => function accessories(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::44 => function heads(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::46 => function glasses(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::88 => function tokenURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::90 => function dataURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::98 => function generateSVGImage(INounsSeeder.Seed memory seed) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorMinimal.sol::27 => function tokenURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorMinimal.sol::29 => function dataURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::44 => function baseURI() external returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::46 => function palettes(uint8 paletteIndex) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::48 => function backgrounds(uint256 index) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::50 => function bodies(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::52 => function accessories(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::54 => function heads(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::56 => function glasses(uint256 index) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::130 => function tokenURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::132 => function dataURI(uint256 tokenId, INounsSeeder.Seed memory seed) external view override returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::140 => function generateSVGImage(INounsSeeder.Seed memory seed) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsSeeder.sol::31 => function generateSeed(uint256 nounId, INounsDescriptorMinimal descriptor) external view returns (Seed memory);

2022-08-nounsdao/contracts/interfaces/INounsToken.sol::47 => function dataURI(uint256 tokenId) external returns (string memory);

2022-08-nounsdao/contracts/interfaces/ISVGRenderer.sol::31 => function generateSVG(SVGParams memory params) external view returns (string memory svg);

2022-08-nounsdao/contracts/interfaces/ISVGRenderer.sol::33 => function generateSVGPart(Part memory part) external view returns (string memory partialSVG);

2022-08-nounsdao/contracts/interfaces/ISVGRenderer.sol::35 => function generateSVGParts(Part[] memory parts) external view returns (string memory partialSVG);

2022-08-nounsdao/contracts/libs/Inflate.sol::75 => function bits(State memory s, uint256 need) private pure returns (ErrorCode, uint256) {

2022-08-nounsdao/contracts/libs/Inflate.sol::103 => function _stored(State memory s) private pure returns (ErrorCode) {

2022-08-nounsdao/contracts/libs/Inflate.sol::145 => function _decode(State memory s, Huffman memory h) private pure returns (ErrorCode, uint256) {

2022-08-nounsdao/contracts/libs/Inflate.sol::572 => function _build_fixed(State memory s) private pure returns (ErrorCode) {

2022-08-nounsdao/contracts/libs/Inflate.sol::606 => function _fixed(State memory s) private pure returns (ErrorCode) {

2022-08-nounsdao/contracts/libs/Inflate.sol::613 => function _build_dynamic_lengths(State memory s) private pure returns (ErrorCode, uint256[] memory) {

2022-08-nounsdao/contracts/libs/Inflate.sol::646 => function _build_dynamic(State memory s)

2022-08-nounsdao/contracts/libs/Inflate.sol::796 => function _dynamic(State memory s) private pure returns (ErrorCode) {

2022-08-nounsdao/contracts/libs/Inflate.sol::814 => function puff(bytes memory source, uint256 destlen) internal pure returns (ErrorCode, bytes memory) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::48 => function generateSVG(SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::68 => function _generateSVGRects(SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::125 => function _getChunk(uint256 cursor, string[16] memory buffer) private pure returns (string memory) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::141 => function _decodeRLEImage(bytes memory image) private pure returns (DecodedImage memory) {

2022-08-nounsdao/contracts/libs/NFTDescriptor.sol::34 => function constructTokenURI(TokenURIParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/NFTDescriptor.sol::60 => function generateSVGImage(MultiPartRLEToSVG.SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/NFTDescriptorV2.sol::34 => function constructTokenURI(ISVGRenderer renderer, TokenURIParams memory params)

2022-08-nounsdao/contracts/libs/NFTDescriptorV2.sol::60 => function generateSVGImage(ISVGRenderer renderer, ISVGRenderer.SVGParams memory params)

2022-08-nounsdao/contracts/libs/SSTORE2.sol::15 => function write(bytes memory data) internal returns (address pointer) {

2022-08-nounsdao/contracts/libs/SSTORE2.sol::51 => function read(address pointer) internal view returns (bytes memory) {

2022-08-nounsdao/contracts/libs/SSTORE2.sol::55 => function read(address pointer, uint256 start) internal view returns (bytes memory) {

2022-08-nounsdao/contracts/test/Multicall2.sol::23 => function aggregate(Call[] memory calls) public returns (uint256 blockNumber, bytes[] memory returnData) {

2022-08-nounsdao/contracts/test/Multicall2.sol::33 => function blockAndAggregate(Call[] memory calls)

2022-08-nounsdao/contracts/test/Multicall2.sol::76 => function tryAggregate(bool requireSuccess, Call[] memory calls) public returns (Result[] memory returnData) {

2022-08-nounsdao/contracts/test/Multicall2.sol::89 => function tryBlockAndAggregate(bool requireSuccess, Call[] memory calls)

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::65 => function readFile(string memory filepath) internal returns (bytes memory output) {
```

## 2. Cache `<array>.length` (23 instances) - Completed

If `<array>.length` is used as for loop termination condition, then the `.length` method will be called in each iteration. Caching it in a local variable can save gas.

```solidity
2022-08-nounsdao/contracts/NounsArt.sol::131 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::110 => for (uint256 i = 0; i < newColors.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::120 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::130 => for (uint256 i = 0; i < _bodies.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::140 => for (uint256 i = 0; i < _accessories.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::150 => for (uint256 i = 0; i < _heads.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::160 => for (uint256 i = 0; i < _glasses.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::100 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::111 => for (uint256 i = 0; i < image.draws.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::192 => for (uint256 i = 5; i < image.length; i += 2) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::81 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::90 => for (uint256 i = 0; i < image.rects.length; i++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::152 => for (uint256 i = 5; i < image.length; i += 2) {

2022-08-nounsdao/contracts/test/Multicall2.sol::26 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::78 => for (uint256 i = 0; i < calls.length; i++) {
```

## 3. Use `unchecked{}` to suppress overflow/underflow check (40 instances) - Completed

Starting from version 0.8.0, Solidity does overflow/underflow checks by default. It is a good feature to prevent vulnerabilities but it has a significant overhead, especially when used in for loop. When using uint256/int256, it is extremely hard to trigger overflow, so it makes sense to skip these checks. To suppress the overflow/underflow checks, use `unchecked {}`. For increment situations, just use `unchecked {}` directly; for decrement situations, add a `require()` statement before decrementing to prevent underflow.

```solidity
2022-08-nounsdao/contracts/NounsArt.sol::131 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsArt.sol::432 => for (uint256 i = 0; i < len; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::110 => for (uint256 i = 0; i < newColors.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::120 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::130 => for (uint256 i = 0; i < _bodies.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::140 => for (uint256 i = 0; i < _accessories.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::150 => for (uint256 i = 0; i < _heads.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::160 => for (uint256 i = 0; i < _glasses.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::100 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::111 => for (uint256 i = 0; i < image.draws.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/libs/Inflate.sol::275 => for (len = 0; len <= MAXBITS; ++len) {

2022-08-nounsdao/contracts/libs/Inflate.sol::278 => for (symbol = 0; symbol < n; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::342 => for (len = 1; len < MAXBITS; ++len) {

2022-08-nounsdao/contracts/libs/Inflate.sol::347 => for (symbol = 0; symbol < n; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::580 => for (symbol = 0; symbol < 144; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::583 => for (; symbol < 256; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::586 => for (; symbol < 280; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::589 => for (; symbol < FIXLCODES; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::596 => for (symbol = 0; symbol < MAXDCODES; ++symbol) {

2022-08-nounsdao/contracts/libs/Inflate.sol::632 => for (index = 0; index < ncode; ++index) {

2022-08-nounsdao/contracts/libs/Inflate.sol::638 => for (; index < 19; ++index) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::81 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::90 => for (uint256 i = 0; i < image.rects.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::26 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::78 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/NounsTokenHarness.sol::26 => for (uint256 i = 0; i < amount; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::33 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::38 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::67 => for (uint256 i = 0; i < max; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::597 => for (uint256 i = 0; i < pageCount; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::604 => for (uint256 i = 0; i < glassesCount; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::606 => for (uint256 j = 1; j < pageCount; j++) {
```

## 4. Long `require()`/`revert()` string (1 instances) - Completed

Shortening revert strings to fit in 32 bytes will decrease gas costs for deployment and gas costs when the revert condition has been met.

```solidity
2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::152 => require(queuedTransactions[txHash], "NounsDAOExecutor::executeTransaction: Transaction hasn't been queued.");
```

## 5. Using `bool`s for storage incurs overhead (25 instances) - Completed

Use `uint256(1)` and `uint256(2)` for true/false. Booleans are more expensive than uint256 or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.

```solidity
2022-08-nounsdao/contracts/NounsAuctionHouse.sol::126 => bool extended = _auction.endTime - block.timestamp < timeBuffer;

2022-08-nounsdao/contracts/NounsDescriptor.sol::35 => bool public override arePartsLocked;

2022-08-nounsdao/contracts/NounsDescriptor.sol::38 => bool public override isDataURIEnabled = true;

2022-08-nounsdao/contracts/NounsDescriptor.sol::230 => bool enabled = !isDataURIEnabled;

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::43 => bool public override arePartsLocked;

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::46 => bool public override isDataURIEnabled = true;

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::379 => bool enabled = !isDataURIEnabled;

2022-08-nounsdao/contracts/NounsToken.sol::43 => bool public isMinterLocked;

2022-08-nounsdao/contracts/NounsToken.sol::46 => bool public isDescriptorLocked;

2022-08-nounsdao/contracts/NounsToken.sol::49 => bool public isSeederLocked;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::204 => bool canceled;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::206 => bool vetoed;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::208 => bool executed;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::216 => bool hasVoted;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::304 => bool canceled;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::306 => bool vetoed;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::308 => bool executed;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::320 => bool hasVoted;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::390 => bool canceled;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::392 => bool vetoed;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::394 => bool executed;

2022-08-nounsdao/contracts/interfaces/INounsAuctionHouse.sol::33 => bool settled;

2022-08-nounsdao/contracts/test/MaliciousVoter.sol::11 => bool useReason;

2022-08-nounsdao/contracts/test/MaliciousVoter.sol::17 => bool useReason_

2022-08-nounsdao/contracts/test/Multicall2.sol::19 => bool success;
```

## 6. Use `!= 0` instead of `> 0` when comparing uint (11 instances) - Completed

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```solidity
2022-08-nounsdao/contracts/NounsAuctionHouse.sol::236 => if (_auction.amount > 0) {

2022-08-nounsdao/contracts/SVGRenderer.sol::115 => while (length > 0) {

2022-08-nounsdao/contracts/base/ERC721.sol::126 => return bytes(baseURI).length > 0 ? string(abi.encodePacked(baseURI, tokenId.toString())) : '';

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::153 => return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::215 => if (srcRep != dstRep && amount > 0) {

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::218 => uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::225 => uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::243 => if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::541 => if (votes > 0) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::967 => if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {

2022-08-nounsdao/contracts/libs/Inflate.sol::354 => return left > 0 ? ErrorCode.ERR_CONSTRUCT : ErrorCode.ERR_NONE;
```

## 7. Empty blocks should be removed or at least emit an event (8 instances) - Completed

Empty blocks exist in the code. The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

```solidity
2022-08-nounsdao/contracts/base/ERC721.sol::454 => ) internal virtual {}

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::186 => receive() external payable {}

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::188 => fallback() external payable {}

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1030 => receive() external payable {}

2022-08-nounsdao/contracts/proxies/NounsAuctionHouseProxy.sol::27 => ) TransparentUpgradeableProxy(logic, admin, data) {}

2022-08-nounsdao/contracts/proxies/NounsAuctionHouseProxyAdmin.sol::23 => contract NounsAuctionHouseProxyAdmin is ProxyAdmin {}

2022-08-nounsdao/contracts/test/NounsDAOExecutorHarness.sol::12 => constructor(address admin_, uint256 delay_) NounsDAOExecutor(admin_, delay_) {}

2022-08-nounsdao/contracts/test/NounsTokenHarness.sol::19 => ) NounsToken(noundersDAO, minter, descriptor, seeder, proxyRegistry) {}
```

## 8. Don't initialize variables with default value (36 instances) - Completed

Uninitialized variables are assigned with the types default value. Explicitly initializing a variable with it's default value costs unnecesary gas.

```solidity
2022-08-nounsdao/contracts/NounsArt.sol::131 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsArt.sol::431 => uint256 pageFirstImageIndex = 0;

2022-08-nounsdao/contracts/NounsArt.sol::432 => for (uint256 i = 0; i < len; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::110 => for (uint256 i = 0; i < newColors.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::120 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::130 => for (uint256 i = 0; i < _bodies.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::140 => for (uint256 i = 0; i < _accessories.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::150 => for (uint256 i = 0; i < _heads.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::160 => for (uint256 i = 0; i < _glasses.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::100 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::111 => for (uint256 i = 0; i < image.draws.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::168 => for (uint256 i = 0; i < cursor; i += 4) {

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::181 => uint32 lower = 0;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::948 => uint256 lower = 0;

2022-08-nounsdao/contracts/libs/Inflate.sol::150 => uint256 code = 0;

2022-08-nounsdao/contracts/libs/Inflate.sol::152 => uint256 first = 0;

2022-08-nounsdao/contracts/libs/Inflate.sol::156 => uint256 index = 0;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::81 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::90 => for (uint256 i = 0; i < image.rects.length; i++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::127 => for (uint256 i = 0; i < cursor; i += 4) {

2022-08-nounsdao/contracts/test/Multicall2.sol::26 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::78 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/NounsTokenHarness.sol::26 => for (uint256 i = 0; i < amount; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::33 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::38 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::67 => for (uint256 i = 0; i < max; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::597 => for (uint256 i = 0; i < pageCount; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::604 => for (uint256 i = 0; i < glassesCount; i++) {
```

## 9. Use `++i`/`--i` instead of `i++`/`i--` (29 instances) - Completed

Using `++i`/`--i` saves 6 gas per loop.

```solidity
2022-08-nounsdao/contracts/NounsArt.sol::131 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsArt.sol::432 => for (uint256 i = 0; i < len; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::110 => for (uint256 i = 0; i < newColors.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::120 => for (uint256 i = 0; i < _backgrounds.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::130 => for (uint256 i = 0; i < _bodies.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::140 => for (uint256 i = 0; i < _accessories.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::150 => for (uint256 i = 0; i < _heads.length; i++) {

2022-08-nounsdao/contracts/NounsDescriptor.sol::160 => for (uint256 i = 0; i < _glasses.length; i++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::100 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::111 => for (uint256 i = 0; i < image.draws.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::81 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::90 => for (uint256 i = 0; i < image.rects.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::26 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/Multicall2.sol::78 => for (uint256 i = 0; i < calls.length; i++) {

2022-08-nounsdao/contracts/test/NounsTokenHarness.sol::26 => for (uint256 i = 0; i < amount; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::33 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::38 => for (uint256 i = 0; i < tokensToMint; i++) {

2022-08-nounsdao/test/foundry/DescriptorUpgrade.t.sol::67 => for (uint256 i = 0; i < max; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::597 => for (uint256 i = 0; i < pageCount; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::604 => for (uint256 i = 0; i < glassesCount; i++) {

2022-08-nounsdao/test/foundry/NounsArt.t.sol::606 => for (uint256 j = 1; j < pageCount; j++) {
```

## 10. Split `require(xxx && yyy)` to `require(xxx)` and `require(yyy)` (2 instances) - Completed

Instead of using operator `&&` on single require check, using double `require()` checks can save more gas.

```solidity
2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::617 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::819 => require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

## 11. Use uint256/int256 instead of other variations (264 instances) - Completed

Using smaller data types such as uint8/int8 is more expensive than using uint256/int256. The EVM works with 256bit/32byte words. Every operation is based on these base units. If the data is smaller, further operations are needed to downscale from 256 bits to 8 bits, and this is more expensive than using uint256/int256.

```solidity
2022-08-nounsdao/contracts/NounsArt.sol::35 => mapping(uint8 => address) public palettesPointers;

2022-08-nounsdao/contracts/NounsArt.sol::155 => function setPalette(uint8 paletteIndex, bytes calldata palette) external override onlyDescriptor {

2022-08-nounsdao/contracts/NounsArt.sol::177 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::178 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::195 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::196 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::213 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::214 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::231 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::232 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::248 => function setPalettePointer(uint8 paletteIndex, address pointer) external override onlyDescriptor {

2022-08-nounsdao/contracts/NounsArt.sol::265 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::266 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::284 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::285 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::303 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::304 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::322 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::323 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::368 => function palettes(uint8 paletteIndex) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsArt.sol::383 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::384 => uint16 imageCount

2022-08-nounsdao/contracts/NounsArt.sol::396 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsArt.sol::397 => uint16 imageCount

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::49 => uint8 public minBidIncrementPercentage;

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::67 => uint8 _minBidIncrementPercentage,

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::185 => function setMinBidIncrementPercentage(uint8 _minBidIncrementPercentage) external override onlyOwner {

2022-08-nounsdao/contracts/NounsDescriptor.sol::44 => mapping(uint8 => string[]) public override palettes;

2022-08-nounsdao/contracts/NounsDescriptor.sol::108 => function addManyColorsToPalette(uint8 paletteIndex, string[] calldata newColors) external override onlyOwner {

2022-08-nounsdao/contracts/NounsDescriptor.sol::169 => function addColorToPalette(uint8 _paletteIndex, string calldata _color) external override onlyOwner {

2022-08-nounsdao/contracts/NounsDescriptor.sol::301 => function _addColorToPalette(uint8 _paletteIndex, string calldata _color) internal {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::160 => function setPalette(uint8 paletteIndex, bytes calldata palette) external override onlyOwner whenPartsNotLocked {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::174 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::175 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::190 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::191 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::206 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::207 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::222 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::223 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::237 => function setPalettePointer(uint8 paletteIndex, address pointer) external override onlyOwner whenPartsNotLocked {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::252 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::253 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::269 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::270 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::286 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::287 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::303 => uint80 decompressedLength,

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::304 => uint16 imageCount

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::359 => function palettes(uint8 index) public view override returns (bytes memory) {

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::468 => return art.palettes(uint8(part[0]));

2022-08-nounsdao/contracts/SVGRenderer.sol::31 => uint8 top;

2022-08-nounsdao/contracts/SVGRenderer.sol::32 => uint8 right;

2022-08-nounsdao/contracts/SVGRenderer.sol::33 => uint8 bottom;

2022-08-nounsdao/contracts/SVGRenderer.sol::34 => uint8 left;

2022-08-nounsdao/contracts/SVGRenderer.sol::38 => uint8 length;

2022-08-nounsdao/contracts/SVGRenderer.sol::39 => uint8 colorIndex;

2022-08-nounsdao/contracts/SVGRenderer.sol::100 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/SVGRenderer.sol::114 => uint8 length = _getRectLength(currentX, draw.length, image.bounds.right);

2022-08-nounsdao/contracts/SVGRenderer.sol::155 => uint8 drawLength,

2022-08-nounsdao/contracts/SVGRenderer.sol::156 => uint8 rightBound

2022-08-nounsdao/contracts/SVGRenderer.sol::157 => ) private pure returns (uint8) {

2022-08-nounsdao/contracts/SVGRenderer.sol::158 => uint8 remainingPixelsInLine = rightBound - uint8(currentX);

2022-08-nounsdao/contracts/SVGRenderer.sol::184 => top: uint8(image[1]),

2022-08-nounsdao/contracts/SVGRenderer.sol::185 => right: uint8(image[2]),

2022-08-nounsdao/contracts/SVGRenderer.sol::186 => bottom: uint8(image[3]),

2022-08-nounsdao/contracts/SVGRenderer.sol::187 => left: uint8(image[4])

2022-08-nounsdao/contracts/SVGRenderer.sol::193 => draws[cursor] = Draw({ length: uint8(image[i]), colorIndex: uint8(image[i + 1]) });

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::41 => uint8 public constant decimals = 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::48 => uint32 fromBlock;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::53 => mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::56 => mapping(address => uint32) public numCheckpoints;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::130 => uint8 v,

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::152 => uint32 nCheckpoints = numCheckpoints[account];

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::166 => uint32 nCheckpoints = numCheckpoints[account];

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::181 => uint32 lower = 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::182 => uint32 upper = nCheckpoints - 1;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::184 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::217 => uint32 srcRepNum = numCheckpoints[srcRep];

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::224 => uint32 dstRepNum = numCheckpoints[dstRep];

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::234 => uint32 nCheckpoints,

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::238 => uint32 blockNumber = safe32(

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::253 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::255 => return uint32(n);

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::70 => event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::111 => event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::114 => event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::117 => event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::218 => uint8 support;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::322 => uint8 support;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::352 => uint16 minQuorumVotesBPS;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::354 => uint16 maxQuorumVotesBPS;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::357 => uint32 quorumCoefficient;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::363 => uint32 fromBlock;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::101 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::450 => function castVote(uint256 proposalId, uint8 support) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::462 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::474 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::475 => uint8 v,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::499 => uint8 support

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::105 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::112 => error UnsafeUint16Cast();

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::489 => function castVote(uint256 proposalId, uint8 support) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::503 => function castRefundableVote(uint256 proposalId, uint8 support) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::520 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::535 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::554 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::566 => uint8 support,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::567 => uint8 v,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::591 => uint8 support

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::673 => function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::687 => uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::701 => function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::714 => uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::726 => function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::730 => uint32 oldQuorumCoefficient = params.quorumCoefficient;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::749 => uint16 newMinQuorumVotesBPS,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::750 => uint16 newMaxQuorumVotesBPS,

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::751 => uint32 newQuorumCoefficient

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::923 => uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::965 => uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1018 => function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1019 => require(n <= type(uint32).max, errorMessage);

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1020 => return uint32(n);

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1023 => function safe16(uint256 n) internal pure returns (uint16) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1024 => if (n > type(uint16).max) {

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1025 => revert UnsafeUint16Cast();

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::1027 => return uint16(n);

2022-08-nounsdao/contracts/governance/NounsDAOProxyV2.sol::61 => 'initialize(address,address,address,uint256,uint256,uint256,(uint16,uint16,uint32))',

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::46 => event PaletteSet(uint8 paletteIndex);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::48 => event BodiesAdded(uint16 count);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::50 => event AccessoriesAdded(uint16 count);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::52 => event HeadsAdded(uint16 count);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::54 => event GlassesAdded(uint16 count);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::57 => uint16 imageCount;

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::58 => uint80 decompressedLength;

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::79 => function palettes(uint8 paletteIndex) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::81 => function setPalette(uint8 paletteIndex, bytes calldata palette) external;

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::85 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::86 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::91 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::92 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::97 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::98 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::103 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::104 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::109 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::110 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::113 => function setPalettePointer(uint8 paletteIndex, address pointer) external;

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::117 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::118 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::123 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::124 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::129 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsArt.sol::130 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsAuctionHouse.sol::64 => function setMinBidIncrementPercentage(uint8 minBidIncrementPercentage) external;

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::36 => function palettes(uint8 paletteIndex, uint256 colorIndex) external view returns (string memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::58 => function addManyColorsToPalette(uint8 paletteIndex, string[] calldata newColors) external;

2022-08-nounsdao/contracts/interfaces/INounsDescriptor.sol::70 => function addColorToPalette(uint8 paletteIndex, string calldata color) external;

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::46 => function palettes(uint8 paletteIndex) external view returns (bytes memory);

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::72 => function setPalette(uint8 paletteIndex, bytes calldata palette) external;

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::76 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::77 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::82 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::83 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::88 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::89 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::94 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::95 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::98 => function setPalettePointer(uint8 paletteIndex, address pointer) external;

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::102 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::103 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::108 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::109 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::114 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::115 => uint16 imageCount

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::120 => uint80 decompressedLength,

2022-08-nounsdao/contracts/interfaces/INounsDescriptorV2.sol::121 => uint16 imageCount

2022-08-nounsdao/contracts/libs/Inflate.sol::89 => val |= uint256(uint8(s.input[s.incnt++])) << s.bitcnt;

2022-08-nounsdao/contracts/libs/Inflate.sol::117 => len = uint256(uint8(s.input[s.incnt++]));

2022-08-nounsdao/contracts/libs/Inflate.sol::118 => len |= uint256(uint8(s.input[s.incnt++])) << 8;

2022-08-nounsdao/contracts/libs/Inflate.sol::120 => if (uint8(s.input[s.incnt++]) != (~len & 0xFF) || uint8(s.input[s.incnt++]) != ((~len >> 8) & 0xFF)) {

2022-08-nounsdao/contracts/libs/Inflate.sol::372 => uint16[29] memory lens = [

2022-08-nounsdao/contracts/libs/Inflate.sol::404 => uint8[29] memory lext = [

2022-08-nounsdao/contracts/libs/Inflate.sol::436 => uint16[30] memory dists = [

2022-08-nounsdao/contracts/libs/Inflate.sol::469 => uint8[30] memory dext = [

2022-08-nounsdao/contracts/libs/Inflate.sol::518 => s.output[s.outcnt] = bytes1(uint8(symbol));

2022-08-nounsdao/contracts/libs/Inflate.sol::623 => uint8[19] memory order = [16, 17, 18, 0, 8, 7, 9, 6, 10, 5, 11, 4, 12, 3, 13, 2, 14, 1, 15];

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::28 => uint8 top;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::29 => uint8 right;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::30 => uint8 bottom;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::31 => uint8 left;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::35 => uint8 length;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::36 => uint8 colorIndex;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::40 => uint8 paletteIndex;

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::48 => function generateSVG(SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::68 => function _generateSVGRects(SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::81 => for (uint8 p = 0; p < params.parts.length; p++) {

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::142 => uint8 paletteIndex = uint8(image[0]);

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::144 => top: uint8(image[1]),

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::145 => right: uint8(image[2]),

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::146 => bottom: uint8(image[3]),

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::147 => left: uint8(image[4])

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::153 => rects[cursor] = Rect({ length: uint8(image[i]), colorIndex: uint8(image[i + 1]) });

2022-08-nounsdao/contracts/libs/NFTDescriptor.sol::34 => function constructTokenURI(TokenURIParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/libs/NFTDescriptor.sol::60 => function generateSVGImage(MultiPartRLEToSVG.SVGParams memory params, mapping(uint8 => string[]) storage palettes)

2022-08-nounsdao/contracts/test/MaliciousVoter.sol::10 => uint8 public support;

2022-08-nounsdao/contracts/test/MaliciousVoter.sol::16 => uint8 support_,

2022-08-nounsdao/contracts/test/WETH.sol::10 => uint8 public decimals = 18;

2022-08-nounsdao/test/foundry/NounsArt.t.sol::15 => event PaletteSet(uint8 paletteIndex);

2022-08-nounsdao/test/foundry/NounsArt.t.sol::17 => event BodiesAdded(uint16 count);

2022-08-nounsdao/test/foundry/NounsArt.t.sol::19 => event AccessoriesAdded(uint16 count);

2022-08-nounsdao/test/foundry/NounsArt.t.sol::21 => event HeadsAdded(uint16 count);

2022-08-nounsdao/test/foundry/NounsArt.t.sol::23 => event GlassesAdded(uint16 count);

2022-08-nounsdao/test/foundry/NounsArt.t.sol::278 => art.addBodies(hex'123456', uint80(12), uint16(1));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::307 => art.addBodies(FIRST_TWO_IMAGES_COMPRESSED, FIRST_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::308 => art.addBodies(NEXT_TWO_IMAGES_COMPRESSED, NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::339 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::341 => art.addBodiesFromPointer(SSTORE2.write(NEXT_TWO_IMAGES_COMPRESSED), NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::358 => art.addAccessories(hex'123456', uint80(12), uint16(1));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::387 => art.addAccessories(FIRST_TWO_IMAGES_COMPRESSED, FIRST_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::388 => art.addAccessories(NEXT_TWO_IMAGES_COMPRESSED, NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::419 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::424 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::442 => art.addHeads(hex'123456', uint80(12), uint16(1));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::471 => art.addHeads(FIRST_TWO_IMAGES_COMPRESSED, FIRST_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::472 => art.addHeads(NEXT_TWO_IMAGES_COMPRESSED, NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::503 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::505 => art.addHeadsFromPointer(SSTORE2.write(NEXT_TWO_IMAGES_COMPRESSED), NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::522 => art.addGlasses(hex'123456', uint80(12), uint16(1));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::551 => art.addGlasses(FIRST_TWO_IMAGES_COMPRESSED, FIRST_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::552 => art.addGlasses(NEXT_TWO_IMAGES_COMPRESSED, NEXT_TWO_IMAGES_DEFLATED_LENGTH, uint16(2));

2022-08-nounsdao/test/foundry/NounsArt.t.sol::581 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::586 => uint16(2)

2022-08-nounsdao/test/foundry/NounsArt.t.sol::595 => (bytes memory glasses, uint80 glassesLength, uint16 glassesCount) = getGlassesPage();

2022-08-nounsdao/test/foundry/NounsArt.t.sol::669 => uint80 constant FIRST_TWO_IMAGES_DEFLATED_LENGTH = 320;

2022-08-nounsdao/test/foundry/NounsArt.t.sol::675 => uint80 constant NEXT_TWO_IMAGES_DEFLATED_LENGTH = 512;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::240 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::241 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::260 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::261 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::280 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::281 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::300 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::301 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::320 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::321 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::343 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::344 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::366 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::367 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::389 => uint80 decompressedLen = 123;

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::390 => uint16 imageCount = 456;

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::40 => (bytes memory bodies, uint80 bodiesLength, uint16 bodiesCount) = abi.decode(

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::42 => (bytes, uint80, uint16)

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::46 => (bytes memory heads, uint80 headsLength, uint16 headsCount) = abi.decode(

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::48 => (bytes, uint80, uint16)

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::52 => (bytes memory accessories, uint80 accessoriesLength, uint16 accessoriesCount) = abi.decode(

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::54 => (bytes, uint80, uint16)

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::58 => (bytes memory glasses, uint80 glassesLength, uint16 glassesCount) = abi.decode(

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::60 => (bytes, uint80, uint16)

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::76 => uint80 glassesLength,

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::77 => uint16 glassesCount

2022-08-nounsdao/test/foundry/helpers/DescriptorHelpers.sol::80 => return abi.decode(readFile('./test/foundry/files/descriptor_v2/glassesPage.abi'), (bytes, uint80, uint16));
```

## 12. Use `abi.encodePacked()` instead of `abi.encode()` (36 instances) - Completed

`abi.encodePacked()` is more efficient than `abi.encode()`.

```solidity
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::135 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::137 => bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::120 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::136 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::151 => bytes32 txHash = keccak256(abi.encode(target, value, signature, data, eta));

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::302 => !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::480 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::482 => bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::313 => !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::572 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::574 => bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

2022-08-nounsdao/test/foundry/DescriptorUpgradeViaProposal.t.sol::42 => calldatas[0] = abi.encode(address(descriptorV2));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::99 => abi.encode('mock svg')

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::133 => vm.mockCall(address(art), abi.encodeWithSelector(NounsArt.backgroundsCount.selector), abi.encode(42));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::142 => abi.encode(INounsArt.Trait({ storedImagesCount: 42, storagePages: new INounsArt.NounArtStoragePage[](0) }))

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::152 => abi.encode(INounsArt.Trait({ storedImagesCount: 42, storagePages: new INounsArt.NounArtStoragePage[](0) }))

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::162 => abi.encode(INounsArt.Trait({ storedImagesCount: 42, storagePages: new INounsArt.NounArtStoragePage[](0) }))

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::172 => abi.encode(INounsArt.Trait({ storedImagesCount: 42, storagePages: new INounsArt.NounArtStoragePage[](0) }))

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::414 => abi.encode('return value')

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::421 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.heads.selector, 17), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::427 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.bodies.selector, 17), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::436 => abi.encode('return value')

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::443 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.glasses.selector, 17), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::449 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.palettes.selector, 17), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::455 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.bodies.selector), abi.encode('the body'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::456 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.accessories.selector), abi.encode('the accessory'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::457 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.heads.selector), abi.encode('the head'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::458 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.glasses.selector), abi.encode('the glasses'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::459 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.palettes.selector), abi.encode('the palette'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::477 => vm.mockCall(address(art), abi.encodeWithSelector(NounsArt.backgroundsCount.selector), abi.encode(123));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::478 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.backgrounds.selector), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::479 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.bodies.selector), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::480 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.accessories.selector), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::481 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.heads.selector), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::482 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.glasses.selector), abi.encode('return value'));

2022-08-nounsdao/test/foundry/NounsDescriptorV2.t.sol::483 => vm.mockCall(address(art), abi.encodeWithSelector(INounsArt.palettes.selector), abi.encode('return value'));
```

## 13. Use `private` instead of `public` for constants (34 instances) - Completed

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

```solidity
2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::41 => uint8 public constant decimals = 0;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::59 => bytes32 public constant DOMAIN_TYPEHASH =

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::63 => bytes32 public constant DELEGATION_TYPEHASH =

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::62 => uint256 public constant GRACE_PERIOD = 14 days;

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::63 => uint256 public constant MINIMUM_DELAY = 2 days;

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::64 => uint256 public constant MAXIMUM_DELAY = 30 days;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::67 => string public constant name = 'Nouns DAO';

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::70 => uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::73 => uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::76 => uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::79 => uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::82 => uint256 public constant MIN_VOTING_DELAY = 1;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::85 => uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::88 => uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::91 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::94 => uint256 public constant proposalMaxOperations = 10; // 10 actions

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::97 => bytes32 public constant DOMAIN_TYPEHASH =

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::101 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::59 => string public constant name = 'Nouns DAO';

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::62 => uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::65 => uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::68 => uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::71 => uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::74 => uint256 public constant MIN_VOTING_DELAY = 1;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::77 => uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::80 => uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::83 => uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::86 => uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::89 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::92 => uint256 public constant proposalMaxOperations = 10; // 10 actions

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::95 => uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::98 => uint256 public constant REFUND_BASE_GAS = 36000;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::101 => bytes32 public constant DOMAIN_TYPEHASH =

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::105 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```

## 14. Use custom errors instead of `revert()`/`require()` strings (3 instances) - Completed

Using `require()`/`revert()` strings is expensive. Starting from Soldity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors.

Custom errors decrease both deploy and runtime gas costs. Note that runtime gas cost is only relevant when the revert condition is met.

```solidity
2022-08-nounsdao/contracts/NounsAuctionHouse.sol::224 => require(_auction.startTime != 0, "Auction hasn't begun");

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::226 => require(block.timestamp >= _auction.endTime, "Auction hasn't completed");

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::152 => require(queuedTransactions[txHash], "NounsDAOExecutor::executeTransaction: Transaction hasn't been queued.");
```

## 15. Use shift right/left instead of division/multiplication if possible (3 instances)

A division/multiplication by any number `x` being a power of 2 can be calculated by shifting `log2(x)` to the right/left. While the `DIV` opcode uses 5 gas, the `SHR` opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

```solidity
2022-08-nounsdao/contracts/SVGRenderer.sol::191 => Draw[] memory draws = new Draw[]((image.length - 5) / 2);

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::184 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

2022-08-nounsdao/contracts/libs/MultiPartRLEToSVG.sol::151 => Rect[] memory rects = new Rect[]((image.length - 5) / 2);
```
