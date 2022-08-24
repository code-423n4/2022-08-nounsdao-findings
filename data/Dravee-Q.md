**Overview**
Risk Rating | Number of issues
--- | ---
Low Risk | 3
Non-Critical Risk | 5

**Table of Contents**

- [1. Low Risk Issues](#1-low-risk-issues)
  - [1.1. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version](#11-known-vulnerabilities-exist-in-currently-used-openzeppelincontracts-version)
  - [1.2. Mismatch intent between `NounsDAOLogicV1` and `NounsDAOLogicV2`](#12-mismatch-intent-between-nounsdaologicv1-and-nounsdaologicv2)
  - [1.3. Low level calls with solidity version <= 0.8.14 can result in optimizer bug](#13-low-level-calls-with-solidity-version--0814-can-result-in-optimizer-bug)
- [2. Non-Critical Issues](#2-non-critical-issues)
  - [2.1. Typos](#21-typos)
  - [2.2. A magic number should be documented and explained](#22-a-magic-number-should-be-documented-and-explained)
  - [2.3. Use `bytes.concat()`](#23-use-bytesconcat)
  - [2.4. The pragmas used are not the same everywhere](#24-the-pragmas-used-are-not-the-same-everywhere)
  - [2.5. Non-library/interface files should use fixed compiler versions, not floating ones](#25-non-libraryinterface-files-should-use-fixed-compiler-versions-not-floating-ones)

# 1. Low Risk Issues

## 1.1. Known vulnerabilities exist in currently used `@openzeppelin/contracts` version

As some [known vulnerabilities](https://snyk.io/test/npm/@openzeppelin/contracts/4.1.0) exist in the current `@openzeppelin/contracts` version, consider updating `package.json` with at least `@openzeppelin/contracts@4.7.3` here:

- <https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/package.json#L31-L32>

```json
File: package.json
31:     "@openzeppelin/contracts": "^4.1.0",
32:     "@openzeppelin/contracts-upgradeable": "^4.1.0",
```

While vulnerabilities are known, the current scope isn't affected (this might not hold true for the whole solution)

## 1.2. Mismatch intent between `NounsDAOLogicV1` and `NounsDAOLogicV2`

In `NounsDAOLogicV1`, there's the following text:

```solidity
File: NounsDAOLogicV1.sol
57: //   in `execute(uint proposalId)`. This contract should not hold funds and does not
58: //   implement `receive()` or `fallback()` functions.
```

However, in `NounsDAOLogicV2`, an unused/empty `receive()` function appeared at the bottom of the contract:

```solidity
File: NounsDAOLogicV2.sol
1030:     receive() external payable {}
```

This seems like an unintended error that got through.

## 1.3. Low level calls with solidity version <= 0.8.14 can result in optimizer bug

The protocol is using low level calls with solidity version <= 0.8.14 which can result in optimizer bug.

<https://medium.com/certora/overly-optimistic-optimizer-certora-bug-disclosure-2101e3f7994d>

Consider upgrading to solidity 0.8.15 here:

```solidity
governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
governance/NounsDAOLogicV2.sol:789:        (bool sent, ) = msg.sender.call{ value: amount }('');
governance/NounsDAOLogicV2.sol:983:            (bool refundSent, ) = msg.sender.call{ value: refundAmount }('');
```

# 2. Non-Critical Issues

## 2.1. Typos

- contructor

```solidity
governance/NounsDAOLogicV1.sol:104:     * @notice Used to initialize the contract during delegator contructor
governance/NounsDAOLogicV2.sol:115:     * @notice Used to initialize the contract during delegator contructor
```

- priviledges

```solidity
governance/NounsDAOLogicV1.sol:646:     * @notice Burns veto priviledges
governance/NounsDAOLogicV2.sol:848:     * @notice Burns veto priviledges
```

## 2.2. A magic number should be documented and explained

*Similar issue in the past: [here](https://github.com/code-423n4/2021-04-marginswap-findings/issues/71)*

```solidity
governance/NounsDAOLogicV2.sol:1007:        return (number * bps) / 10000;
```

Consider using `constant` variables as this would make the code more maintainable and readable while costing nothing gas-wise (constants are replaced by their value at compile-time).

## 2.3. Use `bytes.concat()`

Solidity version 0.8.4 introduces `bytes.concat()` (vs `abi.encodePacked(<bytes>,<bytes>)`)

```solidity
base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
base/ERC721Checkpointable.sol:138:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
governance/NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
governance/NounsDAOLogicV1.sol:483:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
governance/NounsDAOLogicV2.sol:575:        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
```

## 2.4. The pragmas used are not the same everywhere

`^0.8.0` and `^0.8.6`

```solidity
base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
governance/NounsDAOInterfaces.sol:33:pragma solidity ^0.8.6;
governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
```

## 2.5. Non-library/interface files should use fixed compiler versions, not floating ones

```solidity
base/ERC721Checkpointable.sol:35:pragma solidity ^0.8.6;
base/ERC721Enumerable.sol:28:pragma solidity ^0.8.0;
governance/NounsDAOLogicV1.sol:61:pragma solidity ^0.8.6;
governance/NounsDAOLogicV2.sol:53:pragma solidity ^0.8.6;
governance/NounsDAOProxy.sol:36:pragma solidity ^0.8.6;
```
