### 1. MAX_QUORUM_VOTES_BPS_UPPER_BOUND limit description isn't fully accurate (low)

_setDynamicQuorumParams() description isn't fully correct.

## Proof of Concept

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L744

```solidity
     *     Must be lower than `MAX_QUORUM_VOTES_BPS_UPPER_BOUND`
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L762-L764

```solidity
        if (newMaxQuorumVotesBPS > MAX_QUORUM_VOTES_BPS_UPPER_BOUND) {
            revert InvalidMaxQuorumVotesBPS();
        }
```

Same for _setMaxQuorumVotesBPS():

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L698

```solidity
     *     Must be lower than `MAX_QUORUM_VOTES_BPS_UPPER_BOUND`
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L705-L708

```solidity
        require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
```

## Recommended Mitigation Steps

Consider correcting it in the both cases:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L744

```solidity
-    *     Must be lower than `MAX_QUORUM_VOTES_BPS_UPPER_BOUND`
+    *     Must be lower than or equal to `MAX_QUORUM_VOTES_BPS_UPPER_BOUND`
```

### 2. proposalThresholdBPS required range description doesn't fully match the logic (low)

_setProposalThresholdBPS() description isn't fully correct.

## Proof of Concept

The description states that it's `newProposalThresholdBPS > MIN_PROPOSAL_THRESHOLD_BPS`:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L651

```solidity
     * @dev newProposalThresholdBPS must be greater than the hardcoded min
```

It's in fact should be in the range with boundaries included:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L656-L660

```solidity
        require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
```

## Recommended Mitigation Steps

Update to match `newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS && newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS` logic.


### 3. MAX_QUORUM_VOTES_BPS_UPPER_BOUND comment doesn't match the logic (low)

Incorrect number of points is stated:

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L85-L86

```solidity
    /// @notice The upper bound of maximum quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```

## Recommended Mitigation Steps

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L85-L86

```solidity
    /// @notice The upper bound of maximum quorum votes basis points
-   uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
+   uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 6,000 basis points or 60%
```
