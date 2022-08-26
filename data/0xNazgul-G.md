## [NAZ-G1] Build the `DOMAIN_TYPEHASH` In The `initialize()` To Save Gas and Clarify/Clean Code
**Context**: [`NounsDAOLogicV2.sol#L101`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L101), [`NounsDAOLogicV1.sol#L97`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L97), [`ERC721Checkpointable.sol#L59`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59)

**Description**:
Building the `DOMAIN_TYPEHASH` in the `initialize()` can save gas and clean the code up.

**Recommendation**: 
1. Remove the `bytes32 public constant DOMAIN_TYPEHASH`
2. Add `bytes32 private immutable DOMAIN_SEPARATOR` as a contract VAR
3. Assign the `DOMAIN_SEPARATOR` in the `initialize()`


## [NAZ-G2] Functions Visibility Can Be Declared External
**Context**: [`NounsDAOLogicV2.sol#L190`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L190), [`NounsDAOLogicV1.sol#L174`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L174), [`NounsDAOLogicV1.sol#L649`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L649), [`NounsDAOLogicV1.sol#L660`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L660), [`NounsDAOLogicV1.sol#L668`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L668), [`ERC721Checkpointable.sol#L112`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L112), [`ERC721Checkpointable.sol#L126`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L126), [`ERC721Checkpointable.sol#L163`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163), [`ERC721Enumerable.sol#L61`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61), [`ERC721Enumerable.sol#L76`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L76)

**Description**:
Several functions across multiple contracts have a public visibility and can be marked with external visibility to save gas. 

**Recommendation**: 
Change the functions visibility to external to save gas.


## [NAZ-G3] Setting The Constructor To Payable
**Context**: [`NounsDAOProxy.sol`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol)

**Description**:
You can cut out 10 opcodes in the creation-time EVM bytecode if you declare a constructor payable. Making the constructor payable eliminates the need for an initial check of `msg.value == 0` and saves 21 gas on deployment with no security risks.

**Recommendation**: 
Set the constructor to payable.


## [NAZ-G4] Right Shift Instead of Dividing By 2
**Context**: [`NounsDAOLogicV2.sol#L951`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L951), [`ERC721Checkpointable.sol#L184`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184)

**Description**:
The `SHR` opcode is 3 gas cheaper than `DIV` and also bypasses Solidity's division by 0 prevention overhead.

**Recommendation**: 
Consider using right shift instead of dividing by 2.


## [NAZ-G5] Use `++index` instead of `index++` to increment a loop counter
**Context**: [`NounsDAOLogicV2.sol#L292`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292), [`NounsDAOLogicV2.sol#L330`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330), [`NounsDAOLogicV2.sol#L357`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357), [`NounsDAOLogicV2.sol#L382`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382), [`NounsDAOLogicV1.sol#L281`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281), [`NounsDAOLogicV1.sol#L319`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319), [`NounsDAOLogicV1.sol#L346`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346), [`NounsDAOLogicV1.sol#L371`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

**Description**:
Due to reduced stack operations, using `++index` saves 5 gas per iteration.

**Recommendation**: 
Use `++index `to increment a loop counter.


## [NAZ-G6] The Increment In For Loop Post Condition Can Be Made Unchecked
**Context**: [`NounsDAOLogicV2.sol#L292`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292), [`NounsDAOLogicV2.sol#L330`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330), [`NounsDAOLogicV2.sol#L357`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357), [`NounsDAOLogicV2.sol#L382`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382), [`NounsDAOLogicV1.sol#L281`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281), [`NounsDAOLogicV1.sol#L319`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319), [`NounsDAOLogicV1.sol#L346`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346), [`NounsDAOLogicV1.sol#L371`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

**Description**:
(This is only relevant if you are using the default solidity checked arithmetic). `i++` involves checked arithmetic, which is not required. This is because the value of `i` is always strictly less than length <= 2**256 - 1. Therefore, the theoretical maximum value of `i` to enter the for-loop body is `2**256 - 2`. This means that the `i++` in the for loop can never overflow. Regardless, the overflow checks are performed by the compiler.

Unfortunately, the Solidity optimizer is not smart enough to detect this and remove the checks. One can manually do this by:
```js
for (uint i = 0; i < length; ) {
    // do something that doesn't change the value of i
    unchecked {
        ++i;
    }
}
```
Note that it’s important that the call to `unchecked_inc` is inlined. This is only possible for solidity versions starting from `0.8.2`.

**Recommendation**: 
Consider doing the increment in the for loop post condition in an unchecked block.


## [NAZ-G7] Catching The Array Length Prior To Loop
**Context**: [`NounsDAOLogicV2.sol#L292`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292), [`NounsDAOLogicV2.sol#L330`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330), [`NounsDAOLogicV2.sol#L357`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357), [`NounsDAOLogicV2.sol#L382`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382), [`NounsDAOLogicV1.sol#L281`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281), [`NounsDAOLogicV1.sol#L319`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319), [`NounsDAOLogicV1.sol#L346`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346), [`NounsDAOLogicV1.sol#L371`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

**Description**:
One can save gas by caching the array length (in stack) and using that set variable in the loop. Replace state variable reads and writes within loops with local variable reads and writes. This is done by assigning state variable values to new local variables, reading and/or writing the local variables in a loop, then after the loop assigning any changed local variables to their equivalent state variables.

**Recommendation**: 
Simply do something like so before the for loop: ```uint length =  variable.length```. Then add ```length``` in place of ``` variable.length``` in the for loop. 


## [NAZ-G8] Use of Custom Errors Instead of String
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts)

**Description**:
To save some gas the use of custom errors leads to cheaper deploy time cost and run time cost. The run time cost is only relevant when the revert condition is met.

**Recommendation**: 
Use Custom Errors instead of strings.


## [NAZ-G9] Function Ordering via Method ID
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts)

**Description**:
Contracts most called functions could simply save gas by function ordering via Method ID. Calling a function at runtime will be cheaper if the function is positioned earlier in the order (has a relatively lower Method ID) because 22 gas are added to the cost of a function for every position that came before it. The caller can save on gas if you prioritize most called functions. One could use [`This tool`](https://emn178.github.io/solidity-optimize-name/) to help find alternative function names with lower Method IDs while keeping the original name intact.

**Recommendation**: 
Find a lower method ID name for the most called functions for example ```mostCalled()``` vs. ```mostCalled_41q()``` is cheaper by 44 gas.