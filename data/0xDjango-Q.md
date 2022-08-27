### Low Risk Findings Overview
|        | Finding                                    |  Instances  |
|:-------|:-------------------------------------------|:-----------:|
| [L-01] | Floating Pragma                            |      1      |
| [L-02] | Confusing function name “delegates()”      |      1      |
| [L-03] | Proposal Threshold is not inclusive        |      1      |
| [L-04] | Lack of validation on quorum params setter |      1      |
| [L-05] | Single-step Vetoer transfer is unsafe      |      1      |
| [L-06] | Empty `fallback()`/`receive()` function    |      1      |
### Non-critical Findings Overview
|        | Finding                                     |  Instances  |
|:-------|:--------------------------------------------|:-----------:|
| [N-01] | The use of magic numbers is not recommended |      2      |
| [N-02] | Incorrect spec                              |      2      |
| [N-03] | Typos                                       |      2      |
### QA overview per contract
| Contract                                                                                                                                                         |  Total Instances  |  Total Findings  |  Low Findings  |  Low Instances  |  NC Findings  |  NC Instances  |
|:-----------------------------------------------------------------------------------------------------------------------------------------------------------------|:-----------------:|:----------------:|:--------------:|:---------------:|:-------------:|:--------------:|
| [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol)     |         5         |        5         |       3        |        3        |       2       |       2        |
| [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol)     |         3         |        3         |       0        |        0        |       3       |       3        |
| [NounsDAOLogicV1.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol)     |         2         |        2         |       1        |        1        |       1       |       1        |
| [ERC721Checkpointable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol) |         1         |        1         |       1        |        1        |       0       |       0        |
| [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol)     |         1         |        1         |       1        |        1        |       0       |       0        |
## Low Risk Findings
### [L-01] Floating Pragma
A floating pragma might result in contract being tested/deployed with different compiler versions possibly leading to unexpected behaviour.
*1 instance of this issue has been found:*
###### [L-01] [NounsDAOLogicV2.sol#L53-L54](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L53-L54)
```solidity

pragma solidity ^0.8.6;

```
### [L-02] Confusing function name “delegates()”
It seems confusing to name the function `delegates()` (plural) when a delegator will only have, at most, 1 delegatee at a time.
*1 instance of this issue has been found:*
###### [L-02] [ERC721Checkpointable.sol#L88](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L88)
```solidity

function delegates(address delegator) public view returns (address) {
    address current = _delegates[delegator];
    return current == address(0) ? delegator : current;
}
```
### [L-03] Proposal Threshold is not inclusive
Proposers with the exact number of votes required by the proposal threshold should not be reverted.
*1 instance of this issue has been found:*
###### [L-03] [NounsDAOLogicV1.sol#L187-L190](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L187-L190)
```solidity

require(
    nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
    'NounsDAO::propose: proposer votes below proposal threshold'
);
```
### [L-04] Lack of validation on quorum params setter
The quorum coeefficient should be "a fixed point integer with 6 decimals" but its value is not validated. This could have unexpected effects on the dynamic quorum calculation.
*1 instance of this issue has been found:*
###### [L-04] [NounsDAOLogicV2.sol#L726](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L726)
```solidity

function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {
    require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
    DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number);


    uint32 oldQuorumCoefficient = params.quorumCoefficient;
    params.quorumCoefficient = newQuorumCoefficient;


    _writeQuorumParamsCheckpoint(params);


    emit QuorumCoefficientSet(oldQuorumCoefficient, newQuorumCoefficient);
}
```
### [L-05] Single-step Vetoer transfer is unsafe
If the `newVetoer` is set to the incorrect address, the functionality will be unretrievable.
*1 instance of this issue has been found:*
###### [L-05] [NounsDAOLogicV2.sol#L839-L845](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L839-L845)
```solidity

function _setVetoer(address newVetoer) public {
    require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');


    emit NewVetoer(vetoer, newVetoer);


    vetoer = newVetoer;
}
```
### [L-06] Empty `fallback()`/`receive()` function
If intended functionality is to make use of `ETH` the `receive()` function should implement some operation, if not it should revert the transaction.
*1 instance of this issue has been found:*
###### [L-06] [NounsDAOLogicV2.sol#L1030-L1031](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L1030-L1031)
```solidity

    receive() external payable {}

```

## Non-critical Findings
### [N-01] The use of magic numbers is not recommended
Consider setting constant numbers as a `constant` variable for better readability and clarity.
*2 instances of this issue have been found:*
###### [N-01] [NounsDAOLogicV2.sol#L908-L909](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L908-L909)
```solidity

        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;

```
###### [N-01b] [NounsDAOLogicV1.sol#L673-L674](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L673-L674)
```solidity

        return (number * bps) / 10000;

```
### [N-02] Incorrect spec
Remove or correct the comment so it refelcts the actual and intended functionality of the code.
*2 instances of this issue have been found:*
###### [N-02] [NounsDAOLogicV2.sol#L851-L856](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L851-L856)
```solidity

    function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

        _setVetoer(address(0));
    }
```
###### [N-02b] [NounsDAOLogicV1.sol#L649-L654](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L649-L654)
```solidity

    function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

        _setVetoer(address(0));
    }
```
### [N-03] Typos
Please fix typos.
*2 instances of this issue have been found:*
###### [N-03] [NounsDAOLogicV1.sol#L46](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L46)
```solidity

// - Veto ability which allows `veteor` to halt any proposal at any stage unless -> vetoer
```
###### [N-03b] [NounsDAOLogicV1.sol#L104-L105](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L104-L105)
```solidity

     * @notice Used to initialize the contract during delegator contructor -> constructor

```
