## [L-01] Inaccurate block time after The Merge
[NounsDAOLogicV2.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L68)
[NounsDAOLogicV2.sol#L71](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L71)
[NounsDAOLogicV2.sol#L77](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L77)
[NounsDAOLogicV1.sol#L76](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L76)
[NounsDAOLogicV1.sol#L79](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L79)
[NounsDAOLogicV1.sol#L85](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L85)

```
/// @notice The minimum setable voting period
uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

/// @notice The max setable voting period
uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

/// @notice The max setable voting delay
uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
```
`MIN_VOTING_PERIOD`, `MAX_VOTING_PERIOD`, and `MAX_VOTING_DELAY` assume that block time would be around 15 seconds. However, once Ethereum transitioned to Proof of Stake in less than a month, blocks will come in exactly 12 seconds. This will cause a 20% deviation between the  expected and the actual timing. 

For example, `MIN_VOTING_PERIOD` which should be 24 hours (`5760 * 15 / 3600`) will become 19 hours (`5760 * 12 / 3600`). This discrepancy might surprise users that expect the minimum voting period to last for a day, causing them to miss voting on proposals.

Consider adjusting the value of these constants to match the new block time.

References:
- [Beacon Chain Spec](https://github.com/ethereum/consensus-specs/blob/v0.11.1/specs/phase0/beacon-chain.md#time-parameters:~:text=seconds-,12%20seconds,-MIN_ATTESTATION_INCLUSION_DELAY)
- [Ethereum Blog](https://blog.ethereum.org/2021/11/29/how-the-merge-impacts-app-layer#:~:text=blocks%20come%20in%20exactly%20each%2012%20seconds)