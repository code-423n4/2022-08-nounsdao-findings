# QA Report
* Wrong comment in the `_burnVetoPower` function
    ```sol
    function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

        _setVetoer(address(0));
    }
    ```
* Pragmas should be locked to a specific compiler version, to avoid contracts getting deployed using a different version, which may have a greater risk of undiscovered bugs.

## NounsDAOLogicV2
* Wrong amount in the comment on line 86 (should be 6,000 instead of 4,000)
    `uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%`
* Unsafe setVetoer mechanism can make the vetoer become unreachable - it is recommended to use an accept vetoer function (similar to the mechanism that is used for admin transfer)
* A user can call the `vote` with 0 votes and spam the logs - this won't cause any damage because the function won't change the state, but it will emit an event.