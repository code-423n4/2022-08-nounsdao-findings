1. Use Custom Error instead of Revert / Require String to Save Gas

Custom error from solidity 0.8.4 are cheaper than revert strings, custom error are defined using the `error` statement can use inside and outside the contract.

source https://blog.soliditylang.org/2021/04/21/custom-errors/

i suggest replacing revert / require error strings with custom error.

POC : 

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L73
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L74
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L81
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L82
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L83
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L90
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L100
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L114
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L117
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L134
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L149
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L152
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L155
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L159
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L174
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L122
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L123
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L125
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L128
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L132
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L136
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L140
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L133
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L134
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L136
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L139
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L143
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L147


2. `require()`/`revert()` strings longer than 32 bytes cost extra gas

Each extra chunk of bytes past the original 32 which costs 3 gas.

resource : https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L117
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L205
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L303
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L314

3. empty blocks should be remove or emit something

he code should be refactored such that they no longer exist, or the block should do something useful,
such as emitting an event or reverting. If the contract is meant to be extended,
the contract should be `abstract` and the function signatures be added without any default implementation.
If the block is an empty if-statement block to avoid doing subsequent checks in the else-if/else conditions,
the else-if/else conditions should be nested under the negation of the if-statement,
because they involve different classes of checks, which may lead to the introduction of errors when the code is
later modified (`if(x){}else if(y){...}else{...}` => `if(!x){if(y){...}else{...}}`)

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L186
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOExecutor.sol#L188
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030

4. useage of uint / int smaller than 32 bytes incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

POC : 

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L218
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L462


5. Splitting `require()` statements that use && save gas

see https://github.com/code-423n4/2022-01-xdefi-findings/issues/128 which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L127
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L131
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L135
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L139
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L640
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L657
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L678

6. use int256 instead of bool for true or false

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L95

7. ++i costs less gas than i++, especially when it's used in for-loops

save 6 gas per loop

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371

8. cheaper foor loops

You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

```
for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
          // Do the thing
          // Unchecked pre-increment is cheapest
          unchecked { ++i; }
  }     
```

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371

9. Declaring `uint256 i`

Declaring `uint256 i = 0;` means doing an MSTORE of the value 0 Instead you could just declare `uint256 i` to declare the variable without assigning it’s default value, saving 3 gas per declaration

POC :

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371