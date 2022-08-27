# Summary

| Id | Title |
| -- | ----- |
| 1 | Inconsistent coding style between `safe32()` and `safe16()` |

## 1. Inconsistent coding style between `safe32()` and `safe16()`

In `NounsDAOLogicV2`, two function `safe32()` and `safe16()` even though have similar functionality but are implemented in different ways. 

```solidity
function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
    require(n <= type(uint32).max, errorMessage);
    return uint32(n);
}

function safe16(uint256 n) internal pure returns (uint16) {
    if (n > type(uint16).max) {
        revert UnsafeUint16Cast();
    }
    return uint16(n);
}
```
It should be consistent for better code quality

### Affected Codes

- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1018-L1028

### Recommended Mitigation Steps

- Refactor to make them consistent


