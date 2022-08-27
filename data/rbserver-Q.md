## [L-01] SETTING MAX_REFUND_PRIORITY_FEE TO 2 gwei CAN DISCOURAGE USERS FROM VOTING
Posts like https://www.blocknative.com/blog/eip-1559-fees and https://www.liquity.org/blog/how-eip-1559-changes-the-transaction-fees-of-ethereum commonly recommend setting the max priority fee to be 2 gwei or higher to ensure that transactions to be included and executed promptly.

Users who want to vote might need to set the max priority fee to be at least 2 gwei as a common practice but will be likely to get a refund that is less than their actual transaction cost for gas usage due to the `MAX_REFUND_PRIORITY_FEE` cap being set to 2 gwei for being used in the following `_refundGas` function. Hence, this can discourage users from voting. Please consider increasing `MAX_REFUND_PRIORITY_FEE` to a moderately higher amount.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L974-L986
```solidity
    function _refundGas(uint256 startGas) internal {
        unchecked {
            uint256 balance = address(this).balance;
            if (balance == 0) {
                return;
            }
            uint256 gasPrice = min(tx.gasprice, block.basefee + MAX_REFUND_PRIORITY_FEE);
            uint256 gasUsed = startGas - gasleft() + REFUND_BASE_GAS;
            uint256 refundAmount = min(gasPrice * gasUsed, balance);
            (bool refundSent, ) = msg.sender.call{ value: refundAmount }('');
            emit RefundableVote(msg.sender, refundAmount, refundSent);
        }
    }
```

## [L-02] HARDCODED REFUND_BASE_GAS MIGHT NOT BE FUTURE-PROOF
Gas cost can change in the future. The hardcoded `REFUND_BASE_GAS`, which is used in the `_refundGas` function above, can inflate or deflate the gas calculation used for voting when comparing to the actual gas usage in the future. Please consider adding a time-locked function for adjusting `REFUND_BASE_GAS` that is only callable by the `NounsDAOExecutor` contract.

## [L-03] MISSING RETURN VALUE CHECK FOR call()
When calling the following `_withdraw` function, `sent` returned by `msg.sender.call{ value: amount }('')` is not checked. This can cause the failed ETH transfer to revert silently. Please consider checking it to prevent silent failures that can later surprise users unexpectedly.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783-L792
```solidity
    function _withdraw() external {
        if (msg.sender != admin) {
            revert AdminOnly();
        }

        uint256 amount = address(this).balance;
        (bool sent, ) = msg.sender.call{ value: amount }('');

        emit Withdraw(amount, sent);
    }
```

## [L-04] MISSING ZERO-ADDRESS CHECK FOR CRITICAL ADDRESSES
To prevent unintended behaviors, the critical address inputs should be checked against `address(0)`. 

Please consider checking `vetoer_` in the following function.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L124-L132
```solidity
    function initialize(
        address timelock_,
        address nouns_,
        address vetoer_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThresholdBPS_,
        DynamicQuorumParams calldata dynamicQuorumParams_
    ) public virtual {
```

Please consider checking `newPendingAdmin` in the following function.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799-L811
```solidity
    function _setPendingAdmin(address newPendingAdmin) external {
        // Check caller = admin
        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

        // Save current value, if any, for inclusion in log
        address oldPendingAdmin = pendingAdmin;

        // Store pendingAdmin with value newPendingAdmin
        pendingAdmin = newPendingAdmin;

        // Emit NewPendingAdmin(oldPendingAdmin, newPendingAdmin)
        emit NewPendingAdmin(oldPendingAdmin, newPendingAdmin);
    }
```

Please consider checking `newVetoer` in the following function.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839-L845
```solidity
    function _setVetoer(address newVetoer) public {
        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

        emit NewVetoer(vetoer, newVetoer);

        vetoer = newVetoer;
    }
```

## [N-01] executeTransaction of INounsDAOExecutor IS PAYABLE BUT CORRESPONDING FUNCTION IS NOT
The following `executeTransaction` of `INounsDAOExecutor` is `payable` but the corresponding function is not. Please consider removing `payable` from `executeTransaction` of `INounsDAOExecutor` for improving readability and maintainability.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L427-L433
```solidity
    function executeTransaction(
        address target,
        uint256 value,
        string calldata signature,
        bytes calldata data,
        uint256 eta
    ) external payable returns (bytes memory);
```

## [N-02] MAX_QUORUM_VOTES_BPS CAN BE REMOVED FROM NounsDAOLogicV2 CONTRACT
The `MAX_QUORUM_VOTES_BPS` constant is no longer used in the `NounsDAOLogicV2` contract. Please remove it from the contract for better readability and maintainability.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L89
```solidity
    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
```

## [N-03] proposalMaxOperations CAN BE NAMED USING CAPITAL LETTERS AND UNDERSCORES
Because the following `proposalMaxOperations` is a constant, it can be named using capital letters and underscores by convention, which improves readability and maintainability.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L92
```
    uint256 public constant proposalMaxOperations = 10; // 10 actions
```

## [N-04] CONSTANTS CAN BE USED INSTEAD OF MAGIC NUMBERS
To improve readability and maintainability, constants can be used instead of the magic numbers. Please consider replacing the magic numbers used in the following code with constants.
```solidity
contracts\base\ERC721Checkpointable.sol
  254: require(n < 2**32, errorMessage); 
  259: require(n < 2**96, errorMessage); 

contracts\governance\NounsDAOLogicV1.sol
  673: return (number * bps) / 10000;

contracts\governance\NounsDAOLogicV2.sol
  908: uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
  909: uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;
  1007: return (number * bps) / 10000;
```