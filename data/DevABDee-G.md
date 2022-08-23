# 1. single `Uint8` costs more gas than the `uint256`
`uint256` gives better gas optimization than `uint8`. For more info check [this](https://ethereum.stackexchange.com/questions/3067/why-does-uint8-cost-more-gas-than-uint256)
```solidity
contracts/base/ERC721Checkpointable.sol:41: uint8 public constant decimals = 0;
```

# 2. Declaring a variable and assigning it to the default value costs extra gas.
All `uint`s by default are assigned to Zero. but if you assign them again to their default value (0), That will cost extra gas.
```diff
contracts/base/ERC721Checkpointable.sol:41: 
- uint8 public constant decimals = 0;
+ uint8 public constant decimals;
```