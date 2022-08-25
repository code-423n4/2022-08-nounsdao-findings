- [Low](#low)
    - [**1. Mixing and Outdated compiler**](#1-mixing-and-outdated-compiler)
    - [**2. Outdated packages**](#2-outdated-packages)
    - [**3. Denial of serivce by wrong initialization**](#3-denial-of-serivce-by-wrong-initialization)
    - [**4. Unsecure state order**](#4-unsecure-state-order)
    - [**5. Lack of returns check**](#5-lack-of-returns-check)
- [Non critical](#non-critical)
    - [**6. Wrong desing**](#6-wrong-desing)
    - [**7. Bad nomenclature**](#7-bad-nomenclature)
    - [**8. Use abstract for base contracts**](#8-use-abstract-for-base-contracts)
    - [**9. Optimize hierarchy in order to improve the readibility**](#9-optimize-hierarchy-in-order-to-improve-the-readibility)
    - [**10. Lack of event emmit**](#10-lack-of-event-emmit)
    - [**11. Lack of modifier**](#11-lack-of-modifier)
    - [**12. Unify codding style**](#12-unify-codding-style)
    - [**13. Unify error handling**](#13-unify-error-handling)

# Low

## **1. Mixing and Outdated compiler**

```
pragma solidity ^0.8.0;
pragma solidity ^0.8.6;
```

*Note that mixing pragma are not recommended. Because different compiler versions have different meanings and behaviors, it also significantly raises maintenance costs. As a result, depending on the compiler version selected for any given file, deployed contracts may have security issues.*

The minimum required version must be [0.8.16](https://github.com/ethereum/solidity/releases/tag/v0.8.16); otherwise, contracts will be affected by the following **important bug fixes**:

[0.8.3](https://blog.soliditylang.org/2021/03/23/keccak-optimizer-bug/):

- Optimizer: Fix bug on incorrect caching of Keccak-256 hashes.

[0.8.4](https://blog.soliditylang.org/2021/04/21/solidity-0.8.4-release-announcement/):

- ABI Decoder V2: For two-dimensional arrays and specially crafted data in memory, the result of abi.decode can depend on data elsewhere in memory. Calldata decoding is not affected.

[0.8.9](https://blog.soliditylang.org/2021/09/29/solidity-0.8.9-release-announcement/):

- Immutables: Properly perform sign extension on signed immutables.
- User Defined Value Type: Fix storage layout of user defined value types for underlying types shorter than 32 bytes.

[0.8.13](https://blog.soliditylang.org/2022/03/16/solidity-0.8.13-release-announcement/):
- Code Generator: Correctly encode literals used in `abi.encodeCall` in place of fixed bytes arguments.

[0.8.14](https://blog.soliditylang.org/2022/05/18/solidity-0.8.14-release-announcement/):

- ABI Encoder: When ABI-encoding values from calldata that contain nested arrays, correctly validate the nested array length against `calldatasize()` in all cases.
- Override Checker: Allow changing data location for parameters only when overriding external functions.

[0.8.15](https://blog.soliditylang.org/2022/06/15/solidity-0.8.15-release-announcement/)

- Code Generation: Avoid writing dirty bytes to storage when copying `bytes` arrays.
- Yul Optimizer: Keep all memory side-effects of inline assembly blocks.

[0.8.16](https://blog.soliditylang.org/2022/08/08/solidity-0.8.16-release-announcement/)

- Code Generation: Fix data corruption that affected ABI-encoding of calldata values represented by tuples: structs at any nesting level; argument lists of external functions, events and errors; return value lists of external functions. The 32 leading bytes of the first dynamically-encoded value in the tuple would get zeroed when the last component contained a statically-encoded array.

Apart from these, there are several minor bug fixes and improvements.

## **2. Outdated packages**

Some used packages are out of date, it is good practice to use the latest version of these packages:

```
    "@openzeppelin/contracts": "^4.1.0",
    "@openzeppelin/contracts-upgradeable": "^4.1.0",
```

**Affected source code:**

- [package.json:32-33](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/package.json#L32-L33)

## **3. Denial of serivce by wrong initialization**

In `NounsDAOLogicV1` contract, the method `initialize` doesn't check that the provided value of `vetoer_` is valid and not empty, and this could produce a denial of service because the method `_setVetoer` checks that the sender is `vetoer` and if this property is empty, it can never be set.

**Recommended change:**

```diff
    function initialize(
        address timelock_,
        address nouns_,
        address vetoer_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThresholdBPS_,
        uint256 quorumVotesBPS_
    ) public virtual {
        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
+       require(vetoer_ != address(0), 'NounsDAO::initialize: invalid timelock address');
        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
        ...
```

**Affected source code:**

- [NounsDAOLogicV1.sol:116](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L116)
- [NounsDAOLogicV2.sol:156](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L156)

## **4. Unsecure state order**

Negative states must be processed before positive states, otherwise, a proposal that is `Expired` or `Executed`, but is `Active` or `Pending` will be returned as `Active` or `Pending`, this makes it necessary to check that the state is correct from outside this method. I mean, changing outside the variables that alter this state in the correct way.

```javascript
    function state(uint256 proposalId) public view returns (ProposalState) {
        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
        Proposal storage proposal = proposals[proposalId];
        if (proposal.vetoed) {
            return ProposalState.Vetoed;
        } else if (proposal.canceled) {
            return ProposalState.Canceled;
        } else if (block.number <= proposal.startBlock) {
            return ProposalState.Pending;
        } else if (block.number <= proposal.endBlock) {
            return ProposalState.Active;
        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {
            return ProposalState.Defeated;
        } else if (proposal.eta == 0) {
            return ProposalState.Succeeded;
        } else if (proposal.executed) {
            return ProposalState.Executed;
        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
            return ProposalState.Expired;
        } else {
            return ProposalState.Queued;
        }
    }
```

**Affected source code:**

- [NounsDAOLogicV1.sol:422-442](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L422-L442)
- [NounsDAOLogicV2.sol:432-454](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L432-L454)

## **5. Lack of returns check**

The `NounsDAOLicV2._withdraw` method sends ether to the `admin` account, but the return of `sent` is not verified, so it is possible to emit the `Withdraw` event with an error. This could affect dApps that process these types of events.

**Affected source code:**

- [NounsDAOLogicV2.sol:789](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L789)

# Non critical

## **6. Wrong desing**

`decimals` is not related to `ERC721Checkpointable`, and it should me moved to `ERC721` contract.

```javascript
abstract contract ERC721Checkpointable is ERC721Enumerable {
    /// @notice Defines decimals as per ERC-20 convention to make integrations with 3rd party governance platforms easier
    uint8 public constant decimals = 0;
```

**Affected source code:**

- [ERC721Checkpointable.sol:41](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L41)

## **7. Bad nomenclature**

The use of `_` before the name in `public` or `external` methods is not recommended, since by default it is used for `private` methods.

**Affected source code:**

- [NounsDAOProxy.sol:78](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L78)
- [NounsDAOLogicV1.sol:529](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L529)
- [NounsDAOLogicV1.sol:545](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L545)
- [NounsDAOLogicV1.sol:562](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L562)
- [NounsDAOLogicV1.sol:580](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L580)
- [NounsDAOLogicV1.sol:597](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L597)
- [NounsDAOLogicV1.sol:615](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L615)
- [NounsDAOLogicV1.sol:637](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L637)
- [NounsDAOLogicV1.sol:649](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L649)
- [NounsDAOLogicV2.sol:621](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L621)
- [NounsDAOLogicV2.sol:637](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L637)
- [NounsDAOLogicV2.sol:654](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L654)
- [NounsDAOLogicV2.sol:673](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L673)
- [NounsDAOLogicV2.sol:701](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L701)
- [NounsDAOLogicV2.sol:726](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L726)
- [NounsDAOLogicV2.sol:748](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L748)
- [NounsDAOLogicV2.sol:783](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L783)
- [NounsDAOLogicV2.sol:799](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L799)
- [NounsDAOLogicV2.sol:817](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L817)
- [NounsDAOLogicV2.sol:839](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L839)
- [NounsDAOLogicV2.sol:851](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L851)

## **8. Use `abstract` for base contracts**

`abstract` contracts are contracts that have at least one function without its implementation. **An instance of an abstract cannot be created.**

**Reference:**

- https://docs.soliditylang.org/en/v0.6.2/contracts.html#abstract-contracts

**Affected source code:**

- [NounsDAOInterfaces.sol:35](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L35)
- [NounsDAOInterfaces.sol:109](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L109)
- [NounsDAOInterfaces.sol:126](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L126)
- [NounsDAOInterfaces.sol:143](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L143)
- [NounsDAOInterfaces.sol:243](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L243)
- [NounsDAOInterfaces.sol:347](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L347)

## **9. Optimize hierarchy in order to improve the readibility**

The `NounsDAOStorageV1Adjusted` contract defines a `Proposal` struct that is exactly the same as the `Proposal` struct of `NounsDAOStorageV1`, so it is easier for conversions and readability to use the old structure and extend the fields outside of the new structure.

```javascript
    struct Proposal {
        /// @notice proposal
        NounsDAOStorageV1.Proposal proposal;
        /// @notice The total supply at the time of proposal creation
        uint256 totalSupply;
        /// @notice The block at which this proposal was created
        uint256 creationBlock;
    }
```

**Affected source code:**

- [NounsDAOInterfaces.sol:274-315](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L274-L315)

## **10. Lack of event emmit**

The `initialize` method does not emit an event when setting the `vetoer` state variable, as it does for the rest of them.

**Recommended change:**

```diff
    function initialize(
        address timelock_,
        address nouns_,
        address vetoer_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThresholdBPS_,
        uint256 quorumVotesBPS_
    ) public virtual {
        ...
        emit VotingPeriodSet(votingPeriod, votingPeriod_);
        emit VotingDelaySet(votingDelay, votingDelay_);
        emit ProposalThresholdBPSSet(proposalThresholdBPS, proposalThresholdBPS_);
        emit QuorumVotesBPSSet(quorumVotesBPS, quorumVotesBPS_);
+       emit NewVetoer(address(0), vetoer_);
        ...
    }
```

**Affected source code:**

- [NounsDAOLogicV1.sol:150](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L150)
- [NounsDAOLogicV2.sol:156](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L156)

## **11. Lack of `modifier`**

It is recommended to use modifiers in order to improve readability, order and avoid problems of logic failures due to duplication or modification of logics, where at a specific point it has been forgotten to modify. 

Unify all these behaviors, as is the case of authentications, using a `modifier`, is highly recommended.

```javascript
require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
```

**Affected source code:**

- [NounsDAOLogicV1.sol:123](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L123)
- [NounsDAOLogicV1.sol:530](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L530)
- [NounsDAOLogicV1.sol:546](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L546)
- [NounsDAOLogicV1.sol:563](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L563)
- [NounsDAOLogicV1.sol:581](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L581)
- [NounsDAOLogicV1.sol:599](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L599)
- [NounsDAOLogicV2.sol:134](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L134)
- [NounsDAOLogicV2.sol:622](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L622)
- [NounsDAOLogicV2.sol:638](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L638)
- [NounsDAOLogicV2.sol:655](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L655)
- [NounsDAOLogicV2.sol:674](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L674)
- [NounsDAOLogicV2.sol:702](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L702)
- [NounsDAOLogicV2.sol:727](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L727)
- [NounsDAOLogicV2.sol:784](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L784)
- [NounsDAOLogicV2.sol:801](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L801)

## **12. Unify codding style**

At the beginning of the `NounsDAOLogicV1` contract you can see how `_` is used in the numerical values to delimit the thousands in a more visual way. 

```javascript
/// @notice The minimum setable voting period
uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
/// @notice The max setable voting period
uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
```

However there are areas in the code where this does not apply.

**Reference:**

- https://docs.soliditylang.org/en/v0.8.16/grammar.html#a4.SolidityLexer.DecimalNumber

**Affected source code:**

- [NounsDAOLogicV1.sol:673](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L673)
- [NounsDAOLogicV2.sol:98](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L98)
- [NounsDAOLogicV2.sol:908](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L908)
- [NounsDAOLogicV2.sol:1007](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L1007)

## **13. Unify error handling**

You see how the errors have been implemented differently in the `safe32` and `safe16` methods of the `NounsDAOLogicV2` contract, where `require` is used in one and custom errors in another.

```javascript
function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
    require(n <= type(uint32).max, errorMessage);
    return uint32(n);
}

function safe16(uint256 n) internal pure returns (uint16) {
    if (n > type(uint16).max) {
        revert UnsafeUint16Cast();
    }
    return uint16(n);
}
```

**Affected source code:**

- [NounsDAOLogicV2.sol:1018-1028](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L1018-L1028)
