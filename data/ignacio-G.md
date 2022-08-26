 # 1)<ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP and Increments can be unchecked
The overheads outlined below are PER LOOP, excluding the first loop
storage arrays incur a Gwarmaccess (100 gas)
memory arrays use MLOAD (3 gas)
calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset
( ++I/I++ ) SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
++I COSTS LESS GAS THAN I++
The unchecked keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas per loop
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
# 2  USE MULTIPLE REQUIRE() STATMENTS INSTED OF REQUIRE(EXPRESSION && EXPRESSION && ...)
SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191
# 3 REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L133
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L139
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L147
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L215
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L314
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L433
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L593
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L625
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L641
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L659
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L122
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L567
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L584
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L80
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140
# 4 Use != 0 instead of > 0 at the above mentioned codes. The variable is uint, so it will not be below 0 so it can just check != 0.
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L541
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L967
# 5 USING CALLDATA INSTEAD OF MEMORY FOR READ-ONLY ARGUMENTS IN EXTERNAL FUNCTIONS SAVES GAS
When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memory index. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution.
If the array is passed to an internal function which passes the array to another internal function where the array is modified and therefore memory is used in the external call, it’s still more gass-efficient to use calldata when the external function uses modifiers, since the modifiers may prevent the internal functions from being called. Structs have the same overhead as an array of length one
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L403
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L423
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L701
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726
# 6 ABI.ENCODE() IS LESS EFFICIENT THAN ABI.ENCODEPACKED()
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L572
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L574
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L313