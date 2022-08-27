### Long single line comments 
In theory, comments over 79 characters should wrap using multi-line comment syntax. Even if somewhat longer comments are acceptable, there are cases where very long comments interfere with readability. Below are instances of extra-long comments whose readability could be improved by wrapping, as shown:
___
[NounsDAOInterfaces.sol: L179-182](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L179-L182)
```solidity
        /// @notice The number of votes needed to create a proposal at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 proposalThreshold;
        /// @notice The number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed at the time of proposal creation. *DIFFERS from GovernerBravo
        uint256 quorumVotes;
```
Suggestion:
```solidity
        /// @notice The number of votes needed to create a proposal at the time
        ///   of proposal creation — *DIFFERS from GovernerBravo.
        uint256 proposalThreshold;
        /// @notice The minimum number of votes in support of a proposal required
        ///   in order for a quorum to be reached and for a vote to succeed at the time 
        ///   of proposal creation — *DIFFERS from GovernerBravo.
        uint256 quorumVotes;
```
Similarly for the following:

[NounsDAOInterfaces.sol: L279-282](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L279-L282)

[NounsDAOInterfaces.sol: L373-376](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L373-L376)
___
[NounsDAOInterface.sol: L156](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L156)
```solidity
    /// @notice The basis point number of votes in support of a proposal required in order for a quorum to be reached and for a vote to succeed. *DIFFERS from GovernerBravo
```
Suggestion:
```solidity
    /// @notice The basis point number of votes in support of a proposal required in order 
    ///   for a quorum to be reached and for a vote to succeed — *DIFFERS from GovernerBravo.
```
Similarly for the following:

[NounsDAOInterface.sol: L153](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L153)

[NounsDAOInterface.sol: L253](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L253)

[NounsDAOInterface.sol: L256](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L256)
___
[NounsDAOLogicV2.sol: L599](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L599)
```solidity
        /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics
```
Suggestion:
```solidity
        /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal
        ///   was created in order to normalize quorumVotes and proposalThreshold metrics.
```
Similarly for the following:

[NounsDAOLogicV2.sol: L97](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L97)

[NounsDAOLogicV1.sol: L507](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L507)

[NounsDAOInterfaces.sol: L351](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L351)

[NounsDAOInterfaces.sol: L353](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L353)

[ERC721Checkpointable.sol: L40](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L40)

[ERC721Checkpointable.sol: L104](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L104)

[ERC721Checkpointable.sol: L198](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L198)
___
[NounsDAOLogicV2.sol: L900](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L900)
```solidity
     * @param params Configurable parameters for calculating the quorum based on againstVotes. See `DynamicQuorumParams` definition for additional details.
```
Suggestion:
```solidity
     * @param params Configurable parameters for calculating the quorum based on againstVotes — 
     *   see `DynamicQuorumParams` definition for additional details.
```
___
[NounsDAOLogicV2.sol: L796](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L796)
```solidity
     * @dev Admin function to begin change of admin. The newPendingAdmin must call `_acceptAdmin` to finalize the transfer.
```
Suggestion:
```solidity
     * @dev Admin function to begin change of admin — the newPendingAdmin 
     *   must call `_acceptAdmin` to finalize the transfer.
```
Similarly for the following:

[NounsDAOLogicV1.sol: L594](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L594)
___
___

### `Require` message is inadequate
A revert string should provide enough information for users to understand reason for failure
___
[NounsDAOLogicV2.sol: L1018-1021](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1018-L1021)
```solidity
    function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
        require(n <= type(uint32).max, errorMessage);
        return uint32(n);
    }
```
Similarly for the following:

[ERC721Checkpointable.sol: L253-256](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L253-L256)

[ERC721Checkpointable.sol: L258-261](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L258-L261)

[ERC721Checkpointable.sol: L263-271](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L263-L271)

[ERC721Checkpointable.sol: L273-280](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L273-L280)
___
___

### Typos
___
The same typo (`setable`) occurs in all fifteen lines referenced below:

[NounsDAOLogicV2.sol: L61](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L61)

[NounsDAOLogicV2.sol: L64](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L64)

[NounsDAOLogicV2.sol: L67](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L67)

[NounsDAOLogicV2.sol: L70](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L70)

[NounsDAOLogicV2.sol: L73](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L73)

[NounsDAOLogicV2.sol: L76](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L76)

[NounsDAOLogicV2.sol: L88](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L88)

[NounsDAOLogicV1.sol: L69](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L69)

[NounsDAOLogicV1.sol: L72](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L72)

[NounsDAOLogicV1.sol: L75](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L75)

[NounsDAOLogicV1.sol: L78](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L78)

[NounsDAOLogicV1.sol: L81](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L81)

[NounsDAOLogicV1.sol: L84](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L84)

[NounsDAOLogicV1.sol: L87](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L87)

[NounsDAOLogicV1.sol: L90](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L90)

*Example ([NounsDAOLogicV2.sol: L61](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L61)):*
```solidity
    /// @notice The minimum setable proposal threshold
```
Change `setable` to `settable` in each case
___
The same typo (`contructor`) occurs in both lines below:

[NounsDAOLogicV2.sol: L115](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L115)

[NounsDAOLogicV1.sol: L104](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L104)
```solidity
     * @notice Used to initialize the contract during delegator contructor
```
Change `contructor` to `constructor` in both cases
___
The same typo (`priviledges`) occurs in both lines below:

[NounsDAOLogicV2.sol: L848](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L848)

[NounsDAOLogicV1.sol: L646](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L646)
```solidity
     * @notice Burns veto priviledges
```
Change `priviledges` to `privileges` in both cases
___
___
