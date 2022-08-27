QA

## [QA-1] Using < operator to validate proposal state in cancel

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347

By using < operator, beside we can save execution gas fee, we can prevent already vetoed proposal (which has != executed state) is being canceled. It has no impact for the code, but still a good consideration.


## [QA-2] Missing check payable.call value in `_refundGas`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L983

The transaction wasn't reverted if the `refundSent` == false. It's better to check the `refundSent` value inside require().


