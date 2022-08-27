# [1] Should have a 2 step process for changing vetoer

## Line References

[NounsDAOLogicV2.sol#L839-L845](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839-L845)

## Description

The ```vetoer``` is able to cancel any proposal not yet executed. The current vetoer is able to transfer their priveleges to any other address by calling the ```_setVetoer``` function in ```NounsDAOLogicV2.sol```. This single step process may lead to irrevocable errors when changing vetoers.

## Recommended Mitigation Steps

To prevent errors such as setting the wrong address as the vetoer, it is recommended to add an additional step to change the vetoer such as the 2-step process currently used to change the admin.




# [2] Possible Issues with Solidity Compiler Version

## Line References

[NounsDAOLogicV2.sol#L980](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L980)

## Description

The ```_refundFee``` function in ```NounsDAOLogicV2.sol``` may be called when a user votes on a proposal and would like some of the gas costs refunded. The function makes use of the basefee function for calculations. The basefee function is only supported from solidity compiler v0.8.7 and up. Since the contract has a floating pragma allowing it to be compiled with v0.8.6 to the latest version this should not currently cause any compile issues. However, if the compiler version were to be locked to 0.8.6 the contract would have issues during compilation. 

## Recommended Mitigation Steps

If the pragmas are going to be locked, then be sure to use v0.8.7 or higher.



# [3] Documentation errors

## Line References

[NounsDAOLogicV2.sol#L783](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783)

[NounsDAOLogicV2.sol#L852](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L852)

## Description

On [line 783 in ```NounsDAOLogicV2.sol```](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783), the ```_withdraw``` function is an admin function used to withdraw funds sent to the contract. The lack of documentation describing the function and the reasoning behind it might lead some users to misunderstand this as a rug vector. Without a desription of the function, if someone pointed out that the function could be used as a rug vector there could be reputational damage to the protocol. Consider adding a description for the function to prevent such a scenario.

On [line 852 in ```NounsDAOLogicV2.sol```](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L852), the comment seems to be meant for the ```_acceptAdmin``` function. Consider changing the comment to read 'Check caller is vetoer'.