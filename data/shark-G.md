## 1. `i++` costs more gas than `++i`
`++i` costs 5 gas less than `i++`, this is because `i++`  creates a temporary variable while `++i` does not. 

Affected Lines:
`NounsDAOLogicV2.sol`: [Line 226](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226)
`NounsDAOLogicV2.sol`: [Line 292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
`NounsDAOLogicV2.sol`: [Line 330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
`NounsDAOLogicV2.sol`: [Line 357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
`NounsDAOLogicV2.sol`: [Line 382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)
## 2. Using Custom errors instead of `require()`
Custom errors are more gas efficient than using `require()`.

## 3. Initializing variables to zero or false costs more gas
Variables by default are set to zero.

Affected Lines:
`NounsDAOLogicV2.sol`: [Line 231](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L231)
`NounsDAOLogicV2.sol`: [Line 238-243](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L238-L243)

## Using `> 0` costs more gas than `!= 0`
It is better to check if a number is greater than zero using `!= 0` as it saves 6 gas compared to `> 0`

Affected Lines:
`NounsDAOLogicV2.sol`: [Line 541](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L541)
`NounsDAOLogicV2.sol`: [Line 967](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L967)

