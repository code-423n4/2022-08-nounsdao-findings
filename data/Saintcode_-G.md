## USE CALLDATA INSTEAD OF MEMORY

When a function with a memory array is called externally, the abi.decode() step has to use a for-loop to copy each index of the calldata to the memoryindex. Each iteration of this for-loop costs at least 60 gas (i.e. 60 * <mem_array>.length). Using calldata directly, obliviates the need for such a loop in the contract code and runtime execution. Structs have the same overhead as an array of length one

When arguments are read-only on external functions, the data location should be calldata

22 instances: 
-ERC721Checkpointable.sol lines 253, 258, 263, 273
-NounsDAOLogicV1.sol lines 172, 176, 177, 178, 179, 297, 298
-NounsDAOLogicV2.sol lines 185, 186, 187, 188, 189. 308. 309. 536, 906, 1018
-NounsDAOProxy.sol line 94

## <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP

Reading array length at each iteration of the loop consumes more gas than necessary.
In the best case scenario (length read on a memory variable), caching the array length in the stack saves around 3 gas per iteration. In the worst case scenario (external calls at each iteration), the amount of gas wasted can be massive.

Consider storing the array’s length in a variable before the for-loop, and use this new variable instead

4 instances:
-NounsDAOLogicV1.sol lines 281, 319, 357, 382

## ++i COSTS LESS GAS THAN i++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)

This saves 6 gas per loop

10 instances:
-NounsDAOLogicV1.sol lines 216, 281,  319, 346, 371
-NounsDAOLogicV2.sol lines 226, 292,  330, 357, 382

## IT COSTS MORE GAS TO INITIALIZE VARIABLES WITH THEIR DEFAULT VALUE THAN LETTING THE DEFAULT VALUE BE APPLIED

If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: for (uint256 i = 0; i < numIterations; ++i) { should be replaced with for (uint256 i; i < numIterations; ++i) {

8 instances:
-NounsDAOLogicV1.sol lines 281, 319, 346, 371
-NounsDAOLogicV2.sol lines 292, 330, 357, 382


## USING > 0 COSTS MORE GAS THAN != 0 WHEN USED ON A UINT IN A WHILE LOOP

This saves 6 gas per loop iteration instance.

7 instances:
-ERC721Checkpointable.sol lines 153, 215, 218, 225, 243
-NounsDAOLogicV2.sol lines 541, 967, 

## USING <= 2  COSTS MORE GAS THAN < 3
1 instance
-NounsDAOLogicV2.sol lines 594 

## CHEAPER FOR LOOPS - 25 TO 80 GAS PER INSTANCE
You can get cheaper for loops (at least 25 gas, however can be up to 80 gas under certain conditions), by rewriting:

   for (uint256 i = 0; i < orders.length; /** NOTE: Removed i++ **/ ) {
           // Do the thing
           // Unchecked pre-increment is cheapest
           unchecked { ++i; }   
}   
8 instances:
-NounsDAOLogicV1.sol lines 281, 319, 346, 371
-NounsDAOLogicV2.sol lines 292, 330, 357, 382



## REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

There are 83 instances of this issue:
-ERC721Checkpointable.sol lines 140, 141, 142, 164, 
-ERC721enumerable.sol lines 62, 77
-NounsDAOLogicV1.sol lines 122, 124, 125, 128, 132, 136, 140, 189, 197, 198, 205, 209, 277, 303, 315, 336, 342, 364, 366, 485, 501, 502, 505, 530, 533, 546, 549, 563, 564,581, 582, 599, 617, 638, 651. 
-NounsDAOLogicV2.sol lines 133, 135, 136, 139, 143, 147, 199, 205, 207, 208, 215, 219, 288, 314, 326, 347, 353, 375, 377, 433, 577, 593, 597, 594, 622, 625, 638, 641, 655, 659, 674, 680, 684, 702, 707, 711, 727, 801, 819, 840, 853, 953


