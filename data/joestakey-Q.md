# QA Report

## Table of Contents

### LOW
- [hash collision with abi.encodePacked](#hash-collision-with-abi.encodepacked)


# hash collision with abi.encodePacked

## IMPACT

strings and bytes are encoded with padding when using `abi.encodePacked`. This can lead to [hash collision](https://docs.soliditylang.org/en/v0.8.15/abi-spec.html#non-standard-packed-mode) when passing the result to `keccak256`.

## SEVERITY

Low

## PROOF OF CONCEPT

2 instances:

### NounsDAOLogicV1.sol

- [bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash))](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L483)

### NounsDAOLogicV2.sol

- [bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash))](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L575)

## TOOLS USED

Manual Analysis

## MITIGATION

Use `abi.encode()` instead.

