## ZERO-ADDRESS CHECKS ARE MISSING

Zero-address checks are a best practice for input validation of critical address parameters. While the codebase applies this to most cases, there are many places where this is missing in constructors and setters.

Impact: Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

## Findings 

**[NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L148-L150)**.

**[NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L154-L156)**.

## Recommended Mitigation Steps

Add zero-address checks, e.g.:

require(_weth != address(0), "Zero-address");
