https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L263-L274
The comment on line 263 of `NounsDAOLogicV2.sol` states that `minQuorumVotes()` will always return zero. So it would be more gas efficient to simply replace that call with the literal value `0`.
___
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L107
If the contract size of `NounsDAOLogicV2` is too large, one obvious opportunity to save space is the many lines such as 134,622,638,655,674,702,727,801 which are checking `require(msg.sender == admin)`. These could all be replaced by a modifier making the same check. This would lose a few custom error strings, but they simply name the function that was called: there is no real loss of utility.
___
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L676-L683
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1010-L1016
`getChainIdInternal()` in both `NounsDAOLogicV1` and `NounsDAOLogicV2` is more gas efficient if rewritten as `chainTest2()` below:
```
contract ChainTest {

    function chainTest1() external view returns (uint256) {
        uint256 chainId;
        assembly {
            chainId := chainid()
        }
        return chainId;
    }

    function chainTest2() external view returns (uint256 chainId) {
      assembly {
        chainId := chainid()
      }
  }
  
}
```
___
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L535-L538
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L627-L630
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L551-L554
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L643-L646
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L569-L572
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L661-L664
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L586-L589
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L687-L692
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L714-L719
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L730-L735
Affected functions:
```
NounsDAOLogicV1._setVotingDelay()
NounsDAOLogicV2._setVotingDelay()
NounsDAOLogicV1._setVotingPeriod()
NounsDAOLogicV2._setVotingPeriod()
NounsDAOLogicV1._setProposalThresholdBPS()
NounsDAOLogicV2._setProposalThresholdBPS()
NounsDAOLogicV1._setQuorumVotesBPS()
NounsDAOLogicV2._setMinQuorumVotesBPS()
NounsDAOLogicV2._setMaxQuorumVotesBPS()
NounsDAOLogicV2._setQuorumCoefficient()
```
Test contract:
```
contract VotingDelaySetTest {
    uint public votingDelay = 123_456;
    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

    function setVotingDelay1(uint256 newVotingDelay) external {
        uint256 oldVotingDelay = votingDelay;
        votingDelay = newVotingDelay;
        emit VotingDelaySet(oldVotingDelay, votingDelay);
    }

    function setVotingDelay2(uint256 newVotingDelay) external {
        emit VotingDelaySet(votingDelay, newVotingDelay);
        votingDelay = newVotingDelay;
    }
}
```
The listed functions all have variable updates and events in the format shown in the test contract in `setVotingDelay1()`. Switching this round to emit the event first and then updating, without using the memory variable, as in `setVotingDelay2()`, gave consistent lower gas costs.
