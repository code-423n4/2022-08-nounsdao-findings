### Use defualt value rather than overwriite variable with their default value.

Overriting varibles with defualt values with their default value will waste only gas and not necessary.

_There are **28** instances of this issue:_

```solidity
File: ./contracts/NounsArt.sol

131:     for (uint256 i = 0; i < _backgrounds.length; i++) {

431:     uint256 pageFirstImageIndex = 0;

432:     for (uint256 i = 0; i < len; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsArt.sol#L131

```solidity
File: ./contracts/NounsDescriptor.sol

110:    for (uint256 i = 0; i < newColors.length; i++) {

120:    for (uint256 i = 0; i < _backgrounds.length; i++) {

130:    for (uint256 i = 0; i < _bodies.length; i++) {

140:    for (uint256 i = 0; i < _accessories.length; i++) {

150:    for (uint256 i = 0; i < _heads.length; i++) {

160:    for (uint256 i = 0; i < _glasses.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsDescriptor.sol#L110

```solidity
File: ./contracts/governance/NounsDAOLogicV1.sol

281:    for (uint256 i = 0; i < proposal.targets.length; i++) {

319:    for (uint256 i = 0; i < proposal.targets.length; i++) {

346:    for (uint256 i = 0; i < proposal.targets.length; i++) {

371:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: ./contracts/governance/NounsDAOLogicV2.sol

292:    for (uint256 i = 0; i < proposal.targets.length; i++) {

330:    for (uint256 i = 0; i < proposal.targets.length; i++) {

357:    for (uint256 i = 0; i < proposal.targets.length; i++) {

382:    for (uint256 i = 0; i < proposal.targets.length; i++) {

948:    uint256 lower = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
File: ./contracts/SVGRenderer.sol

100:    for (uint8 p = 0; p < params.parts.length; p++) {

111:    for (uint256 i = 0; i < image.draws.length; i++) {

168:    for (uint256 i = 0; i < cursor; i += 4) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/SVGRenderer.sol#L100

```solidity
File: ./contracts/base/ERC721Checkpointable.sol

181: uint32 lower = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L181

```solidity
File: ./contracts/libs/Inflate.sol

150:    uint256 code = 0;

152:    uint256 first = 0;

156:    uint256 index = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/libs/Inflate.sol#L150

```solidity
File: ./contracts/libs/MultiPartRLEToSVG.sol

81:     for (uint8 p = 0; p < params.parts.length; p++) {

90:     for (uint256 i = 0; i < image.rects.length; i++) {

127     for (uint256 i = 0; i < cursor; i += 4) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/libs/MultiPartRLEToSVG.sol#L81

### Use `unchecked{++i}`/`unchecked{i++}` rather than this `++i`/`i++` in `for`-loops

Using a `unchecked{++i}`/`unchecked{i++}` is recommended in for loops if underflow and overflow is not possible. This will save some gas because the compiler woudn't make a safety check. This safety checks is came from version 0.8.0 and that applies also to above versions.

_There are **14** instances of this issue:_

```solidity
File: ./contracts/NounsArt.sol

131:     for (uint256 i = 0; i < _backgrounds.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsArt.sol#L131

```solidity
File: ./contracts/NounsDescriptor.sol

110:    for (uint256 i = 0; i < newColors.length; i++) {

120:    for (uint256 i = 0; i < _backgrounds.length; i++) {

130:    for (uint256 i = 0; i < _bodies.length; i++) {

150:    for (uint256 i = 0; i < _heads.length; i++) {

160:    for (uint256 i = 0; i < _glasses.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsDescriptor.sol#L110

```solidity
File: ./contracts/governance/NounsDAOLogicV1.sol

281:    for (uint256 i = 0; i < proposal.targets.length; i++) {

319:    for (uint256 i = 0; i < proposal.targets.length; i++) {

346:    for (uint256 i = 0; i < proposal.targets.length; i++) {

371:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: ./contracts/governance/NounsDAOLogicV2.sol

292:    for (uint256 i = 0; i < proposal.targets.length; i++) {

330:    for (uint256 i = 0; i < proposal.targets.length; i++) {

357:    for (uint256 i = 0; i < proposal.targets.length; i++) {

382:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

### `++i`/`--i` are more cheap operations than `i++`/`i--`

Using a `++i`/`--i` operations can save about 6 gas for loop/instance because compiler will make less operations

_There are **14** instances of this issue:_

```solidity
File: ./contracts/NounsArt.sol

131:     for (uint256 i = 0; i < _backgrounds.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsArt.sol#L131

```solidity
File: ./contracts/NounsDescriptor.sol

110:    for (uint256 i = 0; i < newColors.length; i++) {

120:    for (uint256 i = 0; i < _backgrounds.length; i++) {

130:    for (uint256 i = 0; i < _bodies.length; i++) {

150:    for (uint256 i = 0; i < _heads.length; i++) {

160:    for (uint256 i = 0; i < _glasses.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsDescriptor.sol#L110

```solidity
File: ./contracts/governance/NounsDAOLogicV1.sol

281:    for (uint256 i = 0; i < proposal.targets.length; i++) {

319:    for (uint256 i = 0; i < proposal.targets.length; i++) {

346:    for (uint256 i = 0; i < proposal.targets.length; i++) {

371:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: ./contracts/governance/NounsDAOLogicV2.sol

292:    for (uint256 i = 0; i < proposal.targets.length; i++) {

330:    for (uint256 i = 0; i < proposal.targets.length; i++) {

357:    for (uint256 i = 0; i < proposal.targets.length; i++) {

382:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

### In for loop use outside variable for array length

For loop written like this`for (uint256 i; i < array.length; ++i) {` will cost more gas than `for (uint256 i; i < _lengthOfArray; ++i) {` because for every iteration we use mload and memory_offset
that will cost about 6 gas

_There are **14** instances of this issue:_

```solidity
File: ./contracts/NounsArt.sol

131:     for (uint256 i = 0; i < _backgrounds.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsArt.sol#L131

```solidity
File: ./contracts/NounsDescriptor.sol

110:    for (uint256 i = 0; i < newColors.length; i++) {

120:    for (uint256 i = 0; i < _backgrounds.length; i++) {

130:    for (uint256 i = 0; i < _bodies.length; i++) {

150:    for (uint256 i = 0; i < _heads.length; i++) {

160:    for (uint256 i = 0; i < _glasses.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/NounsDescriptor.sol#L110

```solidity
File: ./contracts/governance/NounsDAOLogicV1.sol

281:    for (uint256 i = 0; i < proposal.targets.length; i++) {

319:    for (uint256 i = 0; i < proposal.targets.length; i++) {

346:    for (uint256 i = 0; i < proposal.targets.length; i++) {

371:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
File: ./contracts/governance/NounsDAOLogicV2.sol

292:    for (uint256 i = 0; i < proposal.targets.length; i++) {

330:    for (uint256 i = 0; i < proposal.targets.length; i++) {

357:    for (uint256 i = 0; i < proposal.targets.length; i++) {

382:    for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292