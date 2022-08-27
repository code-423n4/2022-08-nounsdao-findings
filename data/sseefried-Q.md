# QA Report

## Coding style improvements

- Several public/external functions have a leading underscore. The convention is that leading undescores are just for `internal` functions. The functions are:

* `_setQuorumCoefficient`
* `_setVetoer`
* `_burnVetoPower`
* `_withdraw`
* `_setDynamicQuorumParams`

Consider removing.

## Documentation improvements

<!-- typos, incorrect documentation etc -->

Change `4,000` -> `6,000` on [line 86](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86)
```solidity
    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```

## Test improvements

## L-01: Rounding in `dynamicQuoromVotes` rounds in the wrong direction

At the moment there are around 420 Nouns and hence the same number of votes. If we multiply 420 by the the minimum BPS we get $420 \times 0.02 = 8.4$. However, because we are using integer arithmetic `dynamicQuorumVotes` will return `8` for similar values. The ceiling function should probably be used so that, in this example, `9` votes are required rather than `8`.

## L-03: Revert on failed ETH transfer is reasonable for `_withdraw`

While it is understandable that all refundable votes must succeed whether or not the refund does, the same probably doesn't hold for the `_withdraw` function. Having the function revert when an ether transfer fails seems like a reasonable behaviour that will alert NounsDAO of the need to change the `admin` or finding out what else has gone wrong.

## L-03: Allow gas refund to be settable

Gas costs for EVM operations may change in the future so it is a good idea to allow for the change of `REFUND_BASE_GAS` which is define as a fixed constant. i.e.

```solidity
uint256 public constant REFUND_BASE_GAS = 36000;
```