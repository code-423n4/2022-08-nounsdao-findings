* Misleading comments:
```solidity
  uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```
```solidity
  // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```

* Inconsistency:
```solidity
  if (msg.sender != admin) {
    revert AdminOnly();
  }
```
```solidity
  require(msg.sender == admin, '...');
```

* You dont need assembly here:
```solidity
chainId := chainid()
```
the same can be achieved from a built-in variable: ```block.chainid```.

* Admin can front-run refundable votes and ```_withdraw``` all eth making the subsequent txs nonrefundable.

* From https://github.com/nounsDAO/nouns-tech/tree/main/vote-refund :
_withdraw - Withdraw the ETH balance to the caller.
Rules:
...
This function must only be callable by the timelock (treasury)._

However, the implementation checks if the sender is an admin:
```solidity
  if (msg.sender != admin) {
      revert AdminOnly();
  }
```

* ```MIN_VOTING_DELAY``` is 1 block and ```MIN_VOTING_PERIOD``` is around 1 day. If both configs are set to these min values, it won't leave enough time for the community to react and adjust. Consider introducing a minimum joint requirement for these values, e.g. ```votingDelay_``` + ```votingPeriod_``` >= 2 days.

* Should be >= to cover the case when the number of votes equals to proposalThreshold:
```solidity
  require(
    nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
    'NounsDAO::propose: proposer votes below proposal threshold'
  );
```