# Gas Optimizations
* Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met. Revert strings that are longer than 32 bytes require at least one additional MSTORE, along with additional overhead for computing memory offset, etc.
    An even better and gas efficient approach will be to use [Solidity Custom Errors](https://blog.soliditylang.org/2021/04/21/custom-errors/) instead of revert strings.
* Change the type of `totalSupply` and `creationBlock` to `uint96` and `uint32` respectively so they can fit in the same word and won't require 2 words in the storage (we know totalSupply is a uint96 so it'll be safe)
* Change the `DynamicQuorumParamsCheckpoint` to not contain another struct. The current struct definition requires 2 words in the storage, while the data can be stored in one word (as written in [the documentation](https://docs.soliditylang.org/en/v0.8.13/internals/layout_in_storage.html#:~:text=Structs%20and%20array%20data%20always%20start%20a%20new%20slot%20and%20their%20items%20are%20packed%20tightly%20according%20to%20these%20rules), a struct member always opens a new word in storage). The current struct:
    ```sol
    /// @notice A checkpoint for storing dynamic quorum params from a given block
    struct DynamicQuorumParamsCheckpoint {
        /// @notice The block at which the new values were set
        uint32 fromBlock;
        /// @notice The parameter values of this checkpoint
        DynamicQuorumParams params;
    }
    ```
    The more efficient struct:
    ```solidity
    /// @notice A checkpoint for storing dynamic quorum params from a given block
    struct DynamicQuorumParamsCheckpoint {
        /// @notice The block at which the new values were set
        uint32 fromBlock;
        /// @notice The minimum basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed.
        uint16 minQuorumVotesBPS;
        /// @notice The maximum basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed.
        uint16 maxQuorumVotesBPS;
        /// @notice The dynamic quorum coefficient
        /// @dev Assumed to be fixed point integer with 6 decimals, i.e 0.2 is represented as 0.2 * 1e6 = 200000
        uint32 quorumCoefficient;
    }
    ```


## NounsDAOLogicV2
* Use prefix incrementation instead of incrementation to save gas:
  * [line 226](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226)
  * [line 292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)
  * [line 330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330)
  * [line 357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357)
  * [line 382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)
* Cache storage variables to reduce the amount of SLOADs:
  * cache `nouns`, `proposalCount` in the `propose` function
  * Use memory values instead of storage ones to get the data to store or for the events in the `propose` function:
    * replace `newProposal.id` with the cached value of `proposalCount`
    * replace `newProposal.proposer` with `msg.sender`
    * replace `newProposal.startBlock`, `newProposal.endBlock` and `newProposal.proposalThreshold` with `temp.startBlock`, `temp.endBlock` and `newProposal.proposalThreshold`
  * cache `timelock` in the `queueOrRevertInternal` function
  * cache `proposal.proposer` in the `cancel` function
  * cache `vetoer` in the `veto` function
  * cache `pendingAdmin` and use it before the require in the `_acceptAdmin` function
  * use the cached `oldAdmin` and `oldPendingAdmin` variables instead of accessing the storage multiple times
* Use `calldata` when passing array and struct arguments instead of `memory`
* Avoid initialing a variable to its default value - it will be done automatically and will cost more gas if also done manually.
  * [line 292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)
  * [line 330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330)
  * [line 357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357)
  * [line 382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)
  * [line 948](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L948)
  * [line 948](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L948)
* Cache the array's length outside the loop to avoid accessing it in every iteration.
  * [line 292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292)
  * [line 330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330)
  * [line 357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357)
  * [line 382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)
* Use `!= 0` instead of `> 0` when comparing `uint`s to save gas
  * [line 541](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L541)
  * [line 969](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L969)
* Use right shift instead of dividing by powers of 2 (`x >> i == x / (2 ** i)`).
  * [line 953](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L953) - replace `uint256 center = upper - (upper - lower) / 2;` with `uint256 center = upper - ((upper - lower) >> 1);`

## ERC721Enumerable
* Cache the results of `from != to` instead of calculating it twice in `_beforeTokenTransfer` 
    ```sol
    function _beforeTokenTransfer(
        address from,
        address to,
        uint256 tokenId
    ) internal virtual override {
        super._beforeTokenTransfer(from, to, tokenId);

        bool diffFromTo = from != to;

        if (from == address(0)) {
            _addTokenToAllTokensEnumeration(tokenId);
        } else if (diffFromTo) {
            _removeTokenFromOwnerEnumeration(from, tokenId);
        }
        if (to == address(0)) {
            _removeTokenFromAllTokensEnumeration(tokenId);
        } else if (diffFromTo) {
            _addTokenToOwnerEnumeration(to, tokenId);
        }
    }
    ```
## ERC721Checkpointable
* Avoid initialing a variable to its default value - it will be done automatically and will cost more gas if also done manually.
  * [line 181](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L181)
* Use `!= 0` instead of `> 0` when comparing `uint`s to save gas
  * [line 153](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L153)
  * [line 215](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L215)
  * [line 218](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L218)
  * [line 225](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L225)
  * [line 243](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L243)
* Use right shift instead of dividing by powers of 2 (`x >> i == x / (2 ** i)`).
  * [line 184](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L184)