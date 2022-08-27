## Add unchecked

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L291

```
unchecked {
uint256 eta = block.timestamp + timelock.delay();
}
```

delay shouldn't large so it won't overflow uint256

## Add unchecked

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L948-L960

```
unchecked {
        uint256 lower = 0;
        uint256 upper = len - 1;
        while (upper > lower) {
            uint256 center = upper - (upper - lower) / 2;
            DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];
            if (cp.fromBlock == blockNumber) {
                return cp.params;
            } else if (cp.fromBlock < blockNumber) {
                lower = center;
            } else {
                upper = center - 1;
            }
        }
}
```

Binary search shouldn't overflow uint256

## Use custom error instead of require

```
require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
```

to

```
error ProposalExecuted();
if (state(proposalId) == ProposalState.Executed) revert ProposalExecuted();
```

## Cache for loop length, Use ++i instead of i++ and add unchecked

```
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

to

```
uint256 targetsLength = proposal.targets.length;
for (uint256 i = 0; i < targetsLength; ) {
...
unchecked { ++i; }
}
```