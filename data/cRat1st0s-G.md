# 2022-08-nounsdao-code4rena Gas Report

- [2022-08-nounsdao-code4rena Gas Report](#2022-08-nounsdao-code4rena-gas-report)
  - [Files Description Table](#files-description-table)
  - [Gas Optimizations](#gas-optimizations)
    - [[G-01]: Pre-increments cost less gas compared to post-increments](#g-01-pre-increments-cost-less-gas-compared-to-post-increments)
      - [Impact](#impact)
      - [Code Affected](#code-affected)
      - [Mitigation](#mitigation)
      - [Tools used](#tools-used)
    - [[G-02]: For-Loops: Increments can be unchecked](#g-02-for-loops-increments-can-be-unchecked)
      - [Impact](#impact-1)
      - [Code Affected](#code-affected-1)
      - [Mitigation](#mitigation-1)
      - [Tools used](#tools-used-1)
    - [[G-03]: For-Loops: No need to explicitly initialize variables with default values](#g-03-for-loops-no-need-to-explicitly-initialize-variables-with-default-values)
      - [Impact](#impact-2)
      - [Code Affected](#code-affected-2)
      - [Mitigation](#mitigation-2)
      - [Tools used](#tools-used-2)
    - [[G-04]: For-Loops: <array>.length should not be looked up in every loop of a for-loop](#g-04-for-loops-arraylength-should-not-be-looked-up-in-every-loop-of-a-for-loop)
      - [Impact](#impact-3)
      - [Code Affected](#code-affected-3)
      - [Mitigation](#mitigation-3)
      - [Tools used](#tools-used-3)
    - [[G-05]: Variables: No need to explicitly initialize variables with default values](#g-05-variables-no-need-to-explicitly-initialize-variables-with-default-values)
      - [Impact](#impact-4)
      - [Code Affected](#code-affected-4)
      - [Mitigation](#mitigation-4)
      - [Tools used](#tools-used-4)
    - [[G-06]: Use Custom Errors](#g-06-use-custom-errors)
      - [Impact](#impact-5)
      - [Mitigation](#mitigation-5)
      - [Tools used](#tools-used-5)
    - [[G-07]: Use Shift Right/Left instead of Division/Multiplication if possible](#g-07-use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
      - [Impact](#impact-6)
      - [Code Affected](#code-affected-5)
      - [Mitigation](#mitigation-6)
      - [Tools used](#tools-used-6)
    - [[G-08]: Use multiple require statements instead of `&&`](#g-08-use-multiple-require-statements-instead-of-)
      - [Impact](#impact-7)
      - [Code Affected](#code-affected-6)
      - [Mitigation](#mitigation-7)
      - [Tools used](#tools-used-7)
    - [[G-09]: Use `abi.encodePacked()`](#g-09-use-abiencodepacked)
      - [Impact](#impact-8)
      - [Code Affected](#code-affected-7)
      - [Mitigation](#mitigation-8)
      - [Tools used](#tools-used-8)

##  Files Description Table

| File Name                                                    | SHA-1 Hash                               |
| ------------------------------------------------------------ | ---------------------------------------- |
| 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol    | e0e939a91c5d5c3148ae744741646e3f440d1e3d |
| 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol    | 96223a722bf49513779653adff38b72db55fce3f |
| 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol | 798ddc7b42dff8950e968af112bba2df70a9efe6 |
| 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol      | 8f1078c179bb62ed1da7eb176adea138571e9b6e |
| 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol     | 6cf98771a9206dda38a0900791b2d2e1f6556334 |
| 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol         | 0552a2f4170e2f3fad483ab0a90a0d9c50a92377 |

##  Gas Optimizations

### [G-01]: Pre-increments cost less gas compared to post-increments

#### Impact
Pre-increments cost less gas compared to post-increments.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L226

```solidity
proposalCount++;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L216

```solidity
proposalCount++;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
Change `i++` to `++i`.

#### Tools used
VS Code

### [G-02]: For-Loops: Increments can be unchecked

#### Impact
In Solidity 0.8+, there’s a default overflow check on unsigned integers.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
One example is the code would go from:

```solidity
        for (uint256 i = 0; i < proposal.targets.length; i++) {
            queueOrRevertInternal(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                eta
            );
        }
```
to:
```solidity
        for (uint256 i = 0; i < proposal.targets.length;) {
            queueOrRevertInternal(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                eta
            );
            unchecked {
                i++;
            }
        }
```

#### Tools used
VS Code

### [G-03]: For-Loops: No need to explicitly initialize variables with default values

#### Impact
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0`, etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
Do not initialize variables with default values.

#### Tools used
VS Code

### [G-04]: For-Loops: <array>.length should not be looked up in every loop of a for-loop

#### Impact
Storage arrays incur a Gwarmaccess (100 gas), memory arrays use MLOAD (3 gas) and calldata arrays use CALLDATALOAD (3 gas).

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371

```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
```

#### Mitigation
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

#### Tools used
VS Code

### [G-05]: Variables: No need to explicitly initialize variables with default values

#### Impact
If a variable is not set/initialized, it is assumed to have the default value (`0`, `false`, `0x0`, etc depending on the data type). If you explicitly initialize it with its default value, you are just wasting gas.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L948

```solidity
uint256 lower = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L41

```solidity
uint8 public constant decimals = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L181

```solidity
uint32 lower = 0;
```

#### Mitigation
Do not initialize variables with default values.

#### Tools used
VS Code

### [G-06]: Use Custom Errors

#### Impact
[Less expensive and able to use dynamic information in them](https://blog.soliditylang.org/2021/04/21/custom-errors/).

#### Mitigation
Use custom errors.

#### Tools used
VS Code

### [G-07]: Use Shift Right/Left instead of Division/Multiplication if possible

#### Impact
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

While the DIV opcode uses 5 gas, the SHR opcode only uses 3 gas. Furthermore, Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L951

```solidity
uint256 center = upper - (upper - lower) / 2;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L184

```solidity
uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```

#### Mitigation
A division/multiplication by any number x being a power of 2 can be calculated by shifting log2(x) to the right/left.

#### Tools used
VS Code

### [G-08]: Use multiple require statements instead of `&&`

#### Impact
Using multiple require statements can save gas.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L137-L140

```solidity
require(
    votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
    'NounsDAO::initialize: invalid voting period'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L141-L144

```solidity
require(
    votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
    'NounsDAO::initialize: invalid voting delay'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L145-L148

```solidity
require(
    proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
    'NounsDAO::initialize: invalid proposal threshold bps'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L201-L206

```solidity
require(
    targets.length == values.length &&
        targets.length == signatures.length &&
        targets.length == calldatas.length,
    'NounsDAO::propose: proposal function information arity mismatch'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L623-L626

```solidity
require(
    newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
    'NounsDAO::_setVotingDelay: invalid voting delay'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L639-L642

```solidity
require(
    newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
    'NounsDAO::_setVotingPeriod: invalid voting period'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L656-L660

```solidity
require(
    newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
        newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
    'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L677-L681

```solidity
require(
    newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
        newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
    'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L819

```solidity
require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L126-L129

```solidity
require(
    votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
    'NounsDAO::initialize: invalid voting period'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L130-L133

```solidity
require(
    votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
    'NounsDAO::initialize: invalid voting delay'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L134-L137

```solidity
require(
    proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
    'NounsDAO::initialize: invalid proposal threshold'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L138-L141

```solidity
require(
    quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
    'NounsDAO::initialize: invalid proposal threshold'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L191-L196

```solidity
require(
    targets.length == values.length &&
        targets.length == signatures.length &&
        targets.length == calldatas.length,
    'NounsDAO::propose: proposal function information arity mismatch'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L531-L534

```solidity
require(
    newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
    'NounsDAO::_setVotingDelay: invalid voting delay'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L547-L550

```solidity
require(
    newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
    'NounsDAO::_setVotingPeriod: invalid voting period'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L564-L568

```solidity
require(
    newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
        newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
    'NounsDAO::_setProposalThreshold: invalid proposal threshold'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L582-L585

```solidity
require(
    newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
    'NounsDAO::_setProposalThreshold: invalid proposal threshold'
);
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L617

```solidity
require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

#### Mitigation
Use multiple require statements.

#### Tools used
VS Code

### [G-09]: Use `abi.encodePacked()`

#### Impact
`abi.encode()` is less efficient than `abi.encodePacked()`.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L313

```solidity
!timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L572

```solidity
abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L574

```solidity
bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L302

```solidity
!timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L480

```solidity
abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L482

```solidity
bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

#### Mitigation
Replace `abi.encode()` with `abi.encodePacked()`.

#### Tools used
VS Code