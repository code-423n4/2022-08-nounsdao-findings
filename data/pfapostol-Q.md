##### Summary

Low Risk Issues
||Issue| Instances|
|:---:|:---|:---:|
|**1**|Misleading comments|2|
|**2**|Unused receive() function will lock Ether in contract|1|
|**3**|Misleading error messages|2|

Non-critical Issues
||Issue| Instances|
|:---:|:---|:---:|
|**1**|Comparison to boolean value|2|

---

Low Risk Issues

1. **Misleading comments**

   - contracts/governance/NounsDAOLogicV1.sol:[650](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L650)

   ```solidity
   650     // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[852](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L852)

   ```solidity
   852         // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
   ```

2. **Unused receive() function will lock Ether in contract**

   If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

   - contracts/governance/NounsDAOLogicV2.sol:[1030](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1030)

   ```solidity
   1030    receive() external payable {}
   ```

3. **Misleading error messages**.

   - contracts/governance/NounsDAOLogicV1.sol:[138-141](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L138-L141), [582-585](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L582-L585)

   ```solidity
   138        require(
    139            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
    140            'NounsDAO::initialize: invalid proposal threshold'
    141        );
   ...
   582        require(
    583            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
    584            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
    585        );
   ```

Non-critical Issues

1. **Comparisons to boolean constant**

   code style, clarity
   A boolean comparison with a constant is not necessary. They can be directly used.

   - contracts/governance/NounsDAOLogicV1.sol:[505](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505)

   ```solidity
   505         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[597](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597)

   ```solidity
   597         require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
   ```
