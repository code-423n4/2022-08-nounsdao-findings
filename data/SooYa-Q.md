## WRONG COMMENT 
https://code4rena.com/reports/2022-05-runes#07-use-safetransfersafetransferfrom-consistently-instead-of-transfertransferfrom
``` Check caller is pendingAdmin and pendingAdmin ≠ address(0) ```
Should be check that `msg.sender` is the vetoer 

## REDUNDANT CHECK USING REQUIRE
There is check for `msg.sender == vetoer` in `_burnVetoPower()` and `_setVetoer`. And `_burnVetoPower()` is calling the `_setVeoter()`, so there will be double check for `msg.sender`

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L839-L856

## NO CHECK BOOL FOR USING LOW CALL FUNCTION IN `_withdraw()` 

The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually making a payment.
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L783-L792

## TYPO
There is typo in error message inside revert statement.
``` 'NounsDAO::propose: proposal function information arity mismatch' ```

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L205