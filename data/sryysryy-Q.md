## [N-01] NON-LIBRARY/INTERFACE FILES SHOULD USE FIXED COMPILER VERSIONS, NOT FLOATING ONES

_There are 7 instances of this issue:_

```solidity
// contracts/base/ERC721Checkpointable.sol
35:  pragma solidity ^0.8.6;

// contracts/base/ERC721Enumerable.sol
28:  pragma solidity ^0.8.0;

// contracts/governance/NounsDAOInterfaces.sol
33:  pragma solidity ^0.8.6;

// contracts/governance/NounsDAOLogicV1.sol
61:  pragma solidity ^0.8.6;

// contracts/governance/NounsDAOLogicV2.sol
53:  pragma solidity ^0.8.6;

// contracts/governance/NounsDAOProxy.sol
36:  pragma solidity ^0.8.6;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L35
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L61
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L33
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L36
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L53

&nbsp;
&nbsp;


## [N-02] USE THE SAME SOLIDITY VERSION 

_There is 1 instance of this issue:
```solidity
// contracts/base/ERC721Enumerable.sol
28: pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28

&nbsp;
&nbsp;

## [N-03] MULTIPLE `ADDRESS` MAPPINGS CAN BE COMBINED INTO A SINGLE `MAPPING` OF AN `ADDRESS` TO A `STRUCT`, WHERE APPROPRIATE

_There is 1 instance of this issue:

```solidity
// contracts/base/ERC721Checkpointable.sol
44:    mapping(address => address) private _delegates;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L44

&nbsp;
&nbsp;


## [N-04] UNUSED/EMPTY `RECEIVE()`/`FALLBACK()` FUNCTION

_There is 1 instance of this issue:
```solidity
// contracts/governance/NounsDAOLogicV2.sol
1030:    receive() external payable {}
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030
&nbsp;
&nbsp;
&nbsp;

## [L-01] NO REQUIRE CHECKS FOR .CALL
Function may fail silently due to missing require check
_There are 2 instances of this issue:_

```solidity
// contracts/governance/NounsDAOLogicV2.sol

783:    function _withdraw() external {
784:        if (msg.sender != admin) {
785:            revert AdminOnly();
786:        }
787:        uint256 amount = address(this).balance;
788:        (bool sent, ) = msg.sender.call{ value: amount }('');
789:        emit Withdraw(amount, sent);
790:    }

974:    function _refundGas(uint256 startGas) internal {
975:        unchecked {
976:            uint256 balance = address(this).balance;
977:            if (balance == 0) {
978:                return;
979:            }
980:            uint256 gasPrice = min(tx.gasprice, block.basefee + MAX_REFUND_PRIORITY_FEE);
981:            uint256 gasUsed = startGas - gasleft() + REFUND_BASE_GAS;
982:            uint256 refundAmount = min(gasPrice * gasUsed, balance);
983:            (bool refundSent, ) = msg.sender.call{ value: refundAmount }('');
984:            emit RefundableVote(msg.sender, refundAmount, refundSent);
985:        }
986:    }
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783-L790
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L974-L986