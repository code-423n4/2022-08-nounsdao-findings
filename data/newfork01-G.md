### description

calldata should be used instead of memory for function parameters saves gas if the function argument is only read.


https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/Inflator.sol#L32

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptorV2.sol#L401
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptorV2.sol#L411
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptorV2.sol#L423
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptorV2.sol#L424
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptorV2.sol#L467

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsToken.sol#L128



### description

Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecessary gas.

Suggest not initializing the for loop counter to 0.

An array’s length should be cached to save gas in for-loops

Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Suggest storing the array’s length in a variable before the for-loop, and use it instead:

++i costs less gas compared to i++

++i costs less gas compared to i++ for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration)

Suggest using ++i instead of i++ to increment the value of an uint variable.

Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

taking all of the above, the recommended format for gas savings is

```
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsArt.sol#L131
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsArt.sol#L432

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L110
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L120
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L130
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L140
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L150
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/NounsDescriptor.sol#L160

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/SVGRenderer.sol#L111
