## Function naming: external / public function name should not start with _

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L529

```
  function _setVotingDelay(uint256 newVotingDelay) external {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L545

```
  function _setVotingPeriod(uint256 newVotingPeriod) external {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L562

```
   function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L580

```
   function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external {
```


https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L597

```
  function _setPendingAdmin(address newPendingAdmin) external {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L615

```
    function _acceptAdmin() external {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L637

```
   function _setVetoer(address newVetoer) public {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L649

```
    function _burnVetoPower() public {
```

## Unchecked external call return.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L983

```
  (bool refundSent, ) = msg.sender.call{ value: refundAmount }('');
  emit RefundableVote(msg.sender, refundAmount, refundSent);
```

can add require(refundSent, "refund gas failed") check

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L789

```
    (bool sent, ) = msg.sender.call{ value: amount }('');
    emit Withdraw(amount, sent);
```

can add require(sent, "withdraw failed") check

## Use Enum to represent type instead of handcoded integer to represent type to improve readability.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L602

```
   if (support == 0) {
     proposal.againstVotes = proposal.againstVotes + votes;
   } else if (support == 1) {
     proposal.forVotes = proposal.forVotes + votes;
   } else if (support == 2) {
     proposal.abstainVotes = proposal.abstainVotes + votes;
   }
```

## Incorrect comment

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L86

```
    /// @notice The upper bound of maximum quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
```

should be 

```
   uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 6,000 basis points or 60%
```

## Uncheck zero admin address

pendingAdmin can be zero address

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L804

```

   require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

   // Save current value, if any, for inclusion in log
   address oldPendingAdmin = pendingAdmin;

```