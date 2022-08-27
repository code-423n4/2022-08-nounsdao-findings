1.
Title: Gas savings for using solidity 0.8.10

Proof of Concept:
All contract in scope

Recommended Mitigation Steps:
Consider to upgrade pragma to at least 0.8.10.

Solidity 0.8.10 has a useful change which reduced gas costs of external calls
Reference: [here](https://blog.soliditylang.org/2021/11/09/solidity-0.8.10-release-announcement/)
______________________________________________________________________

2.
Title: Reduce the size of error messages (Long revert Strings)

Impact:
Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.

Proof of Concept:
[NounsDAOProxy.sol#L79-L80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79-L80)
[ERC721Checkpointable.sol#L140-L142](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140-L142)
[NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol) (various line)

Recommended Mitigation Steps:
Consider shortening the revert strings to fit in 32 bytes
________________________________________________________________________

3.
Title: Consider make constant as private to save gas

Proof of Concept:
[ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)
[ERC721Checkpointable.sol#L59-L63](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59-L63)
[NounsDAOLogicV1.sol#L67-L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67-L101)

Recommended Mitigation Steps:
I suggest changing the visibility from `public` to `internal` or `private`
________________________________________________________________________

4.
Title: `>=` is cheaper than `>`

Impact:
Strict inequalities (`>`) are more expensive than non-strict ones (`>=`). This is due to some supplementary checks (ISZERO, 3 gas)

Proof of Concept:
[ERC721Checkpointable.sol#L153](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L153)

Recommended Mitigation Steps:
Consider using `>=` instead of `>` to avoid some opcodes
________________________________________________________________________

5.
Title: Expression for `constant` values such as a call to `keccak256()`, should use `immutable` rather than `constant`

Proof of Concept:
[ERC721Checkpointable.sol#L59-L63](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59-L63)
[NounsDAOLogicV1.sol#L97-L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L97-L101)

Recommended Mitigation Steps:
Change from `constant` to `immutable`
reference: [here](https://github.com/ethereum/solidity/issues/9232)
________________________________________________________________________

6.
Title: Gas improvement on returning `chainId` value

Proof of Concept:
[ERC721Checkpointable.sol#L283](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L283)

Recommended Mitigation Steps:
by set `chainId` in returns L#282 and delete L#283 can save gas

```
function getChainId() internal view returns (uint256 chainId) { //@audit-info: set `chainId` here L#282
        //@audit-info: delete this L#283
        assembly {
            chainId := chainid()
        }
        return chainId;
    }
```
________________________________________________________________________

7.
Title: Comparison operators

Proof of Concept:
[ERC721Checkpointable.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L142)
[NounsDAOLogicV1.sol#L126-L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L141)

Recommended Mitigation Steps:
Replace `<=` with `<`, and `>=` with `>` for gas optimization
________________________________________________________________________

8.
Title: Custom errors from Solidity 0.8.4 are cheaper than revert strings

Impact:
Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information

Custom errors are defined using the error statement
reference: https://blog.soliditylang.org/2021/04/21/custom-errors/

Proof of Concept:
[NounsDAOProxy.sol#L79-L80](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79-L80)
[ERC721Checkpointable.sol#L140-L142](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140-L142)
[NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol) (various line)

Recommended Mitigation Steps:
Replace require statements with custom errors.
________________________________________________________________________

9.
Title: Gas optimization to dividing by 2

Proof of Concept:
[ERC721Checkpointable.sol#L184](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184)

Recommended Mitigation Steps:
Replace `/ 2` with `>> 1`

Reference: [here](https://github.com/byterocket/c4-common-issues/blob/main/0-Gas-Optimizations.md#g008---use-shift-rightleft-instead-of-divisionmultiplication-if-possible)
________________________________________________________________________

10.
Title: Using multiple `require` instead `&&` can save gas

Proof of Concept:
[NounsDAOLogicV1.sol#L126-L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L141)

Recommended Mitigation Steps:
Change to:

```
	require(votingPeriod_ >= MIN_VOTING_PERIOD, 'NounsDAO::initialize: invalid voting period');
	require(votingPeriod_ <= MAX_VOTING_PERIOD, 'NounsDAO::initialize: invalid voting period');
```
________________________________________________________________________

11.
Title: Using `== false` cost more gas

Proof of Concept:
[NounsDAOLogicV1.sol#L505](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505)

Recommended Mitigation Steps:
Change to:

```
	require(!receipt.hasVoted, 'NounsDAO::castVoteInternal: voter already voted');
```
________________________________________________________________________

12.
Title: Consider remove empty block

impact:
The code should be refactored such that they no longer exist, or the block should do something useful, such as emitting an event or reverting.

Proof of Concept:
[ounsDAOLogicV2.sol#L1030](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030)
________________________________________________________________________

13.
Title: Default value initialization

Impact:
If a variable is not set/initialized, it is assumed to have the default value (0, false, 0x0 etc depending on the data type). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

Proof of Concept:
[NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
[NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
[NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
[NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)
[NounsDAOLogicV2.sol#L948](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L948)

Recommended Mitigation Steps:
Remove explicit initialization for default values.
________________________________________________________________________

14.
Title: abi.encode() is less efficient than abi.encodePacked()

Proof of Concept:
[NounsDAOLogicV2.sol#L313](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L313)
________________________________________________________________________

15.
Title: Using unchecked and prefix increment is more effective for gas saving:

Proof of Concept:
[NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)
[NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)
[NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)
[NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)

Recommended Mitigation Steps:
Change to:
```
    for (uint256 i = 0; i < proposal.targets.length;) {
          // ... 
    unchecked { ++i; }
      }
```
________________________________________________________________________