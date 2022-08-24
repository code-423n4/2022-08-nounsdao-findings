# Low effect on readability
## [G-01] Optimisation fo for loop
The easiest thing to do is to optimize every `for` loop, the objective is to replace those like the following example:

    for  (uint256 i =  0; i < array.length; i++)  {
    	//do something
    }

to

    uint256 len =  array.length;
    for  (uint256 i; i < len;)  {
    	//do something
    	unchecked{ ++i; }
    }

By doing so, the `length` is cached which is cheaper than looking at it every loop, `i = 0` is not initialized since `uint` have already a default value of `0` and the increment is transformed to a cheaper form since it can't overflow. (This is cheaper because without `unchecked` there is a check for overflow at each calculation and with the post-increment, the EVM need to store the value with and without increment, but the pre-increment only store the value with increment.)

8 instances: 
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L281-L289
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L319-L327
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L346-L354
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L371-L379
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L292-L300
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L330-L338
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L357-L365
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L382-L390

Consider optimizing `for` loop.

With those change, these evolutions in gas average report can be observed:

    NounsDAOImmutable: cancel: 97511 -> 97347 (-164)
    NounsDAOImmutable: execute: 100124 -> 99958 (-166)
    NounsDAOImmutable: queue: 122808 -> 122642 (-166)
    NounsDAOLogicV1: cancel: 99962 -> 99798 (-164)
    NounsDAOLogicV1: execute: 170128 -> 169884 (-244)
    NounsDAOLogicV1: queue: 148150 -> 147935 (-215)
    NounsDAOLogicV1: veto: 96416 -> 96244 (-172)
    NounsDAOLogicV2: setQuorumCoefficient: 85740 -> 85751 (-11)
    Deployment: MaliciousVoter: 263292 -> 263304 (-12)
    Deployment: NounsDAOImmutable: 3944159 -> 3940023 (-4136)
    Deployment: NounsDAOLogicV1: 3994842 -> 3990826 (-4016)
    Deployment: NounsDAOLogicV1Harness: 3775316 -> 3771156 (-4160)
    Deployment: NounsDAOLogicV2: 5309066 -> 5304710 (-4356)

## [G-02] Transformation of post-increment to pre-increment
As said before, a pre-increment is cheaper than a post one. When it is possible, it is a good practice to apply pre-increment.

2 instances:
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L216
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L226

Consider transforming `proposalCount++` to `++proposalCount`.

With those change, these evolutions in gas average report can be observed:

    NounsDAOImmutable: propose: 402061 -> 402059 (-2)
    NounsDAOLogicV1: propose: 433213 -> 433211 (-2)
    Deployment: MaliciousVoter: 263292 -> 263304 (+12)
    Deployment: NounsDAOImmutable: 3944159 -> 3943955 (-204)
    Deployment: NounsDAOLogicV1: 3994842 -> 3994722 (-120)
    Deployment: NounsDAOLogicV1Harness: 3775316 -> 3775112 (-204)
    Deployment: NounsDAOLogicV2: 5309066 -> 5308838 (-228)

## [G-03] Unnecessary initialization of variable
As mentioned before, some data type have a default value which is already the desired one. The default value of `uint` is `0` and `bool`, `false`, it is so unnecessary to initialize these again.

14 instances
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L181
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L223
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L230-L235
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L231
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L238-L243
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L948

Consider removing `= 0` or `= false` for simple variable and everything in case of struct initialization.

With those change, these evolutions in gas average report can be observed:

    NounsDAOImmutable: propose: 402061 -> 390968 (-11093)
    NounsDAOLogicV1: propose: 433213 -> 422118 (-11095)
    Deployment: MaliciousVoter: 263292 -> 263304 (+12)
    Deployment: NounsDAOImmutable: 3944159 -> 3928207 (-15952)
    Deployment: NounsDAOLogicV1: 3994842 -> 3978973 (-15869)
    Deployment: NounsDAOLogicV1Harness: 3775316 -> 3759364 (-15952)
    Deployment: NounsDAOLogicV2: 5309066 ->  5293112 (-15954)

## [G-04] Use `!= 0` instead of `> 0` for unsigned integers.
A `uint` can't be below zero, so `!= 0` is sufficient and is gas more efficient.

7 instances:
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L541
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L967
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L153
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L215
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L218
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L225
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L243

Consider replacing `>` by `!=`.

*save 3 gas each*
    
## [G-05] Some operations can be marked unchecked
As mentioned before, if an operation can't overflow, it is cheaper to mark it as unchecked to avoid the automatic check of overflow.
In this case:

    uint32 center = upper - (upper - lower) /  2;
The operation is already written to be overflow safe.

2 instances:
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L184
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L951

Consider replacing it by:

    uint32 center;
    unchecked{ center = upper -  (upper - lower)  /  2;  }

*save up to 100 gas each*

## [G-06] Using `storage` instead of `memory` for structs can be cheaper.
A `storage` structure is pre allocated by the contract, by contrast, a `memory` one is newly created. Depending on the case both can be used to optimize the gas cost because simply, a `storage` is cheaper to create but more expensive to read from and to return and a `memory` on the other hand is more expensive to create but cheaper to read from and to return. We can optimize with trials and errors instead of complex calculations (which will probably work a bit better, but it's not done here).

Following this, we can deduce 1 case that can be swapped to optimize runtime cost and deployment cost.

1 instance:
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L185


Consider changing `memory` to `storage` in these lines

With this change, this evolution in gas average report can be observed:

    Deployment: NounsToken: 3924811 -> 3919559 (-5252)

*save up to 1000 gas at each itération*

## [G-07] `external` function for the owner can be marked as `payable`

If a function is guaranteed to revert when called by a normal user, this function can be marked as `payable` to avoid the check to know if a payment is provided.

1 instances:
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L783

Consider adding `payable` keyword.

*Save 21 gas cost*

## [G-08] Unnecessary public constant

Declaring a private constant is cheaper than a public one. In some case, a constant can be declared as private to save gas. It is the case if the constant don't need to be called outside the contract. A user could still read the value directly in the code instead of calling it, if needed.

31 instances:
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L67-L101
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L59-L105
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L41
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L59-L63

Consider changing those constants to private. (The code still pass all the test with these changes.)

With those change, these evolutions in gas average report can be observed:

    MaliciousVoter: castVote: 105959 -> 105938 (-21)
    NounsAuctionHouse: settleAuction: 212176 -> 212154 (-22)
    NounsAuctionHouse: settleCurrentAndCreateNewAuction: 380773 -> 380751 (-22)
    NounsDAOImmutable: cancel: 97511 -> 97533 (+22)
    NounsDAOImmutable: castVote: 90824 -> 90846 (+22)
    NounsDAOImmutable: propose: 402061 -> 402051 (-10)
    NounsDAOImmutable: queue: 122808 -> 122764 (-44)
    NounsDAOLogicV1: _burnVetoPower: 28454 -> 28432 (-22)
    NounsDAOLogicV1: _setVetoer: 33609 -> 33576  (-33)
    NounsDAOLogicV1: _setVotingDelay: 34510 -> 34476 (-34)
    NounsDAOLogicV1: cancel: 99962 -> 99967 (+5)
    NounsDAOLogicV1: castVote: 89926 -> 89948 (+22)
    NounsDAOLogicV1: castVoteWithReason: 76550 -> 76549 (-1)
    NounsDAOLogicV1: execute: 170128 -> 170118 (-10)
    NounsDAOLogicV1: propose: 433213 -> 433231 (+18) 
    NounsDAOLogicV1: queue: 148150 -> 148106 (-44)
    NounsDAOLogicV2: _setMaxQuorumVotesBPS: 85901 -> 85834  (-67)
    NounsDAOLogicV2: _setMinQuorumVotesBPS: 85919 -> 85877 (-42)
    NounsDAOLogicV2: _setQuorumCoefficient: 85740 -> 85783 (+43)
    NounsDAOLogicV2: _withdraw: 36867 -> 36824  (-43)
    NounsDAOLogicV2: castRefundableVoteWithReason: 97094 -> 97052 (-42)
    NounsToken: burn: 91149 -> 91130 (-19)
    NounsToken: delegate: 119453 -> 119431 (-22)
    NounsToken: delegateBySig: 79632 -> 79654 (+22)
    NounsToken: setContractURIHash: 32090 -> 32038 (-52)
    NounsToken: transferFrom: 161823 -> 161801 (-22)
    Deployment: MaliciousVoter: 263292 -> 263304 (+12)
    Deployment: NounsDAOImmutable: 3944159 -> 3846150 (-98009)
    Deployment: NounsDAOLogicV1: 3994842 -> 3897551 (-97261)
    Deployment: NounsDAOLogicV1Harness: 3775316 -> 3677307 (-98009)
    Deployment: NounsDAOLogicV2: 5309066 -> 5178036 (-131030)
    Deployment: NounsToken: 3924811 -> 3895235 (-29576)


# Medium effect on readability
## [G-09] Short reverted string can save gas

Reverted string wich are longuer than 32 bytes require at least one additional `mstore` and so consume more gas than a shorter one.

88 instances:
 - https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol

	 - 37 instances, lines: 122,124,125,128,132,136,140,189,195,197,198,205,209,277,303,315,336,342,364,366,422,485,501,502,505,530,533,546,549,563,567,581,584,599,617,638,651

- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol
	- 43 instances, lines: 133,134,135,136,139,143,147,199,205,207,208,215,219,288,314,326,347,353,375,376,377,433,577,593,594,597,622,625,638,641,655,659,674,680,684,702,707,711,727,801,819,840,853,

- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOProxy.sol
	- 2 instances, lines: 79,80

- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol
	- 4 instances, lines: 140,141,142,164

- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Enumerable.sol
	- 2 instances, lines: 62,77

Consider shortening the revert strings to fit within 32 bytes, using custom errors or creating an error table. (those change will affect the readability of the contract)


Deployment cost evolution by replacing long revert string with a random 31 byte revert string (gas avg):

    Deployment: NounsDAOImmutable: 3944159 -> 3686850 (-257309)
    Deployment: NounsDAOLogicV1: 3994842 -> 3681595 (-313247)
    Deployment: NounsDAOLogicV1Harness: 3775316 -> 3518007 (-257309)
    Deployment: NounsDAOLogicV2: 5309066 -> 4939238 (-369828)
    Deployment: NounsDAOProxy: 494197 -> 469068 (-25129)
    Deployment: NounsToken: 3924811 -> 3876455 (-48356)

*Save 24 gas at each revert call.*

# High effect on readability

## [G-10] Optimise function name
Every function have a keccak256 hash, this hash defines the order of the function in the contract. The best the ranking, the minimum the gas usage to access the function. Each time a function is called, the EVM need to pass through all the functions better ranked, and this operation cost gas. This can be optimized, the ranking is defined by the first four bytes of the kekkack256 hash of the function name. The name can be changed to improve the ranking, which greatly impacts the readability. That's why it's not practical to change all the names, but it's possible to change only the ones of the functions called a lot of times. It is the most interesting on the contract `NounsDAOLogicV1` and the first functions can be 1 `castVote()`, 2 `propose()`, 3 `veto()`, 4 `queue()` as they seem to be the most called in tests. To be the first four, they need to have a hash smaller than `votingPeriod()` which has `02a251a3` (hexadecimal). So the name can be changed to outrank this:

   **[NounsDAOLogicV1](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol):**

 1. `castVote() - > castVote_1146(`)
  **Hash:** `56781388 ->  01153c1e`
  **Rank:** `22 -> 1`
 2. `propose() -> propose_760()` 
 **Hash:** `da95691a -> 015e43ac`
 **Rank:**  `40 -> 2`
 3. `veto()  -> veto_828()` 
 **Hash:** `1d28dec7 -> 01964906`
 **Rank:**  `7 -> 3`
 4. `queue() -> queue_360()`
  **Hash:** `ddf0b009 -> 01e171dd`
  **Rank:** `41 -> 4`
  
Consider optimizing these function names.
It is less interesting to apply this optimization on other contracts since calls are distributed in a more equitable way.

Going through a function cost 22 gas.
*Save rescpectively 462, 836, 88, 814 gas for calling castVote(), propose(), veto(), queue()*
*(The cost of other functions will also change, but the average of these changes is negligible compared to the gain.)*
