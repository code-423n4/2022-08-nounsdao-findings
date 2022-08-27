# Executive Summary

The codebase can benefit by a more aggressive use of the `unchecked` keyword, additionally a packing opportunity will save plenty of gas.

Below findings are sorted by effectiveness as well as uniqueness, the bottom of the report will contain findings most other wardens will have submitted.

Each finding lists estimated gas savings. These are estimates based on EVM Opcodes.

## Pack booleans with address to save storage slots - 15k write, 2k read

Address uses 20 bytes, each bool 1, by moving the `bool` declaration for `Proposal` to be below `address proposer` you can pack them all in one Storage Slot

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L203-L208

```solidity
        /// @notice Creator of the proposal
        /// @audit Move bools below proposer for packing
        address proposer;
        /// @notice Flag marking whether the proposal has been canceled
        bool canceled;
        /// @notice Flag marking whether the proposal has been vetoed
        bool vetoed;
        /// @notice Flag marking whether the proposal has been executed
        bool executed;
```

Will save one storage slot, meaning each proposal creation will cost 15k less gas, and reading that data will cost 2000 less gas each time

Also applies to:
- [`ProposalCondensed`](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L368-L369)


## Avoid emitting variables from Storage, use Memory Cache instead - 100 gas per instance

The code below is emitting an event using the Storage variable `admin`

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOExecutor.sol#L94-L95

```solidity
        emit NewAdmin(admin);

```

This will cost 100 gas (SLOAD), it would be cheaper to use `msg.sender` (the accepting admin) to save 98 gas (100 - 2 gas for CALLER).

This pattern is used pretty widely, so this change will bring hundreds of gas savings throughout the codebase

Also see:

- [`NounsDAOExecutor.setDelay`](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOExecutor.sol#L86-L87)

- All ["Compound" setters in NounsaDAOLogicV2](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L621-L664)


## Unchecked Addition - 40 gas per operation
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L601-L608

The below additions can be made unchecked, saving around 40 gas per operation (between 20 and 80)

```solidity

        if (support == 0) {
            proposal.againstVotes = proposal.againstVotes + votes;
        } else if (support == 1) {
            proposal.forVotes = proposal.forVotes + votes;
        } else if (support == 2) {
            proposal.abstainVotes = proposal.abstainVotes + votes;
        }
```

## Cache length - 3 gas per instance

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L192-L193

```solidity
            targets.length == values.length &&

```

Instead of recomputing `targets.length` each time (which costs an extra 3 gas), you can store the value in a local variable


# Usual Gas Savings

## Unchecked Addition and Increment - 180 gas

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L222-L226

```solidity

        temp.startBlock = block.number + votingDelay;
        temp.endBlock = temp.startBlock + votingPeriod;

        proposalCount++;
```

Also save the return value and read from memory

```
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L228

```solidity
// Delete
// proposalCount++;

// Use
unchecked {
        newProposal.id = ++proposalCount;
}
```

Saves an SLOAD - 100 gas, and the unchecked math

## Unchecked for Binary Search - 100+

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L948-L960

```solidity
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
```


## Usual Loop Optimizations - 25 per iteration

- You don't need to assign the default value to `i` - Saves 3 gas
uint256 i = 0; => `uint256 i;`

- Cache the Array Length - 100 gas per iteration (SLOAD) 
proposal.targets.length => `uint256 length = proposal.targets.length;`

- Change the increment to unchecked pre-increment
i++ => `unchecked { ++i; }`

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L282

```solidity
        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

## Use `< 3` to save 3 gas as `<=` costs twice as much - 3 gas

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L502-L503

```solidity
        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

```

Can be changed to 
```solidity
        require(support < 3, 'NounsDAO::castVoteInternal: invalid vote type');

```

Saving 3 gas