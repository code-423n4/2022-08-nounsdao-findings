## GAS

### [G-01] Unused logic in `NounsDAOLogicV2.getDynamicQuorumParamsAt`

After calling the implementation contract `NounsDAOLogicV2.sol` from the proxy the `initialize` function is called. In the `initialize` function, `_setDynamicQuorumParams` is called and set via `dynamicQuorumParams_` the quorum parameters of the struct  `params`. Then the `_writeQuorumParamsCheckpoint` function is called with `params` as argument, since the array `quorumParamsCheckpoints` still has a length 0 the else logic is executed and quorumParamsCheckpoints[0] is updated to current block as quorumParamsCheckpoints[0].fromBlock and the chosen parameters as quorumParamsCheckpoints[0].params.

Now when the `getDynamicQuorumParamsAt` function is called (necessarily after the initialize function) the `quorumParamsCheckpoints` array is not of length 0 anymore so `len == 0` will never be true.
Assuming the initialize function being called right after `NounsDAOLogicV2.sol` being implemented via the proxy contract, this left the code associated with the check unused.

File :
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L926-L933

```solidity
       if (len == 0) {
           return
               DynamicQuorumParams({
                    minQuorumVotesBPS: safe16(quorumVotesBPS),
                    maxQuorumVotesBPS: safe16(quorumVotesBPS),
                    quorumCoefficient: 0
                });
        }

```

### [G-02] Unnecessary function _burnVetoPower() in `NounsDAOLogicV1.sol` and `NounsDAOLogicV2.sol`

If the vetoer has the intention to “destroy veto power forever” he will use `_burnVetoPower()` which will check if caller is `vetoer` and call `_setVetoer(address(0))` to check (again) if caller is `vetoer` and then assign `vetoer` to `address(0)`. 

If the vetoer has the intention to change the vetoer he will use `_setVetoer()` with the address he chooses as argument. No 0 address check can be made because the function has to be usable for the purpose of destroying veto power.

The _burnVetoPower() function is not useful. `vetoer` can use _setVetoer(address(0)) directly to destroy veto power. 
