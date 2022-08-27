## Unrestricted receive function

Contract:
https://github.com/davidbrai/c4-nouns-gov-v2/blob/master/contracts/governance/NounsDAOLogicV2.sol#L1030

Function:
receive

Issue:
If user accidentally sends eth to this contract, the eth will get stuck forever due to unrestricted receive function

Recommendation:
Remove receive function if contract is not expecting any external amount.
___

## Withdraw can fail without creating any error

Contract:
https://github.com/davidbrai/c4-nouns-gov-v2/blob/master/contracts/governance/NounsDAOLogicV2.sol#L783

Function:
_withdraw

Issue:
It was observed that function is using call to transfer eth but is not checking the return value to verify if call was success or not

Recommendation:
Add a check to see if call was success

## Wrong DOMAIN_TYPEHASH definition

Contract:
https://github.com/davidbrai/c4-nouns-gov-v2/blob/master/contracts/governance/NounsDAOLogicV2.sol#L101

Issue:
version is missing while deducing DOMAIN_TYPEHASH which breaks the EIP 712 standard

Recommendation:
Add string version, to the EIP712Domain string