The scope of this report is NounsDAOLogicV1.sol .

# propose function
Original code:
```
require(
    targets.length == values.length &&
        targets.length == signatures.length &&
        targets.length == calldatas.length,
    'NounsDAO::propose: proposal function information arity mismatch'
);
require(targets.length != 0, 'NounsDAO::propose: must provide actions');
require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```
It is recommended to change to:
```jsx
uint256 targetsLen = targets.length;
uint256 valuesLen = values.length;
uint256 signaturesLen = signatures.length;
uint256 calldatasLen = calldatas.length;

require(
    targetsLen == valuesLen &&
        targetsLen == signaturesLen &&
        targetsLen == calldatasLen,
    'NounsDAO::propose: proposal function information arity mismatch'
);
require(targetsLen != 0, 'NounsDAO::propose: must provide actions');
require(targetsLen <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
```

# queue, execute, cancel, veto functions
Original code:
```solidity
for (uint256 i = 0; i < proposal.targets.length; i++) {
    ...
}
```

It is recommended to change to:
```jsx
uint256 targetsLen = proposal.targets.length;
for (uint256 i = 0; i < targetsLen; i++) {
    ...
}
```

# castVoteBySig, castVote, castVoteWithReason functions
Original code:
```
emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
```

It is recommended to change to:
```jsx
uint96 votes = castVoteInternal(signatory, proposalId, support);
emit VoteCast(signatory, proposalId, support, votes, '');
```