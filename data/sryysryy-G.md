## [G-01] `<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A `FOR`-LOOP

_There are 8 instances of this issue:_

```solidity
// contracts/governance/NounsDAOLogicV1.sol
281:  for (uint256 i = 0; i < proposal.targets.length; i++) {
319:  for (uint256 i = 0; i < proposal.targets.length; i++) {
346:  for (uint256 i = 0; i < proposal.targets.length; i++) {
371:  for (uint256 i = 0; i < proposal.targets.length; i++) {

// contracts/governance/NounsDAOLogicV2.sol
292:  for (uint256 i = 0; i < proposal.targets.length; i++) {
330:  for (uint256 i = 0; i < proposal.targets.length; i++) {
357:  for (uint256 i = 0; i < proposal.targets.length; i++) {
382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292

&nbsp;
&nbsp;

## [G-02] USING `PRIVATE` RATHER THAN `PUBLIC` FOR CONSTANTS, SAVES GAS
If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

_There are 30 instance of this issue:_

```solidity
// contracts/base/ERC721Checkpointable.sol
41:    uint8 public constant decimals = 0;
59:    bytes32 public constant DOMAIN_TYPEHASH =
60:        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

// contracts/governance/NounsDAOLogicV1.sol
67:    string public constant name = 'Nouns DAO';
70:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
73:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
76:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
79:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
82:    uint256 public constant MIN_VOTING_DELAY = 1;
85:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
88:    uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
91:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
94:    uint256 public constant proposalMaxOperations = 10; // 10 actions
97:    bytes32 public constant DOMAIN_TYPEHASH =
98:        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
101:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
    
// contracts/governance/NounsDAOLogicV2.sol
59:    string public constant name = 'Nouns DAO';
62:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
65:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
68:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
71:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
74:    uint256 public constant MIN_VOTING_DELAY = 1;
77:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
80:    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
83:    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
86:    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
89:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
92:    uint256 public constant proposalMaxOperations = 10; // 10 actions
95:    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
98:    uint256 public constant REFUND_BASE_GAS = 36000;
101:    bytes32 public constant DOMAIN_TYPEHASH =
102:        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
105:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L59

&nbsp;
&nbsp;


## [G-03] IT COSTS MORE GAS TO INITIALIZE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

_There are 10 instances of this issue:_

```solidity
// contracts/base/ERC721Checkpointable.sol
181:  uint32 lower = 0;

// contracts/governance/NounsDAOLogicV1.sol
281:  for (uint256 i = 0; i < proposal.targets.length; i++) {
319:  for (uint256 i = 0; i < proposal.targets.length; i++) {
346:  for (uint256 i = 0; i < proposal.targets.length; i++) {
371:  for (uint256 i = 0; i < proposal.targets.length; i++) {

// contracts/governance/NounsDAOLogicV2.sol
292:  for (uint256 i = 0; i < proposal.targets.length; i++) {
330:  for (uint256 i = 0; i < proposal.targets.length; i++) {
357:  for (uint256 i = 0; i < proposal.targets.length; i++) {
382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
948:  uint256 lower = 0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292

&nbsp;
&nbsp;

## [G-04] `++I` COSTS LESS GAS THAN `I++`, ESPECIALLY WHEN IT’S USED IN `FOR`-LOOPS (`--I`/`I--` TOO)

Saves 6 gas _PER LOOP_

_There are 8 instances of this issue:_

```solidity
// contracts/governance/NounsDAOLogicV1.sol
281:  for (uint256 i = 0; i < proposal.targets.length; i++) {
319:  for (uint256 i = 0; i < proposal.targets.length; i++) {
346:  for (uint256 i = 0; i < proposal.targets.length; i++) {
371:  for (uint256 i = 0; i < proposal.targets.length; i++) {

// contracts/governance/NounsDAOLogicV2.sol
292:  for (uint256 i = 0; i < proposal.targets.length; i++) {
330:  for (uint256 i = 0; i < proposal.targets.length; i++) {
357:  for (uint256 i = 0; i < proposal.targets.length; i++) {
382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```


https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292

&nbsp;
&nbsp;

## [G-05] Use shift operator instead of division 

_There are 2 instances of this issue:_

```solidity
// contracts/base/ERC721Checkpointable.sol
184:  uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

// contracts/governance/NounsDAOLogicV2.sol
951:  uint256 center = upper - (upper - lower) / 2;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L951

&nbsp;
&nbsp;

## [G-06] SPLITTING `REQUIRE()` STATEMENTS THAT USE `&&` SAVES GAS

_There are 19 instances of this issue:

```solidity

// contracts/governance/NounsDAOLogicV1.sol
126:        require(
127:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:            'NounsDAO::initialize: invalid voting period'
129:        );

130:        require(
131:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:            'NounsDAO::initialize: invalid voting delay'
133:        );

134:        require(
135:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:            'NounsDAO::initialize: invalid proposal threshold'
137:        );

138:        require(
139:            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:            'NounsDAO::initialize: invalid proposal threshold'
141:        );

191:        require(
192:            targets.length == values.length &&
193:                targets.length == signatures.length &&
194:                targets.length == calldatas.length,
195:            'NounsDAO::propose: proposal function information arity mismatch'
196:        );

531:        require(
532:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:            'NounsDAO::_setVotingDelay: invalid voting delay'
534:        );

547:        require(
548:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:            'NounsDAO::_setVotingPeriod: invalid voting period'
550:        );

564:        require(
565:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:        );

582:        require(
583:            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:        );

617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

// contracts/governance/NounsDAOLogicV2.sol
137:        require(
138:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:            'NounsDAO::initialize: invalid voting period'
140:        );

141:        require(
142:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:            'NounsDAO::initialize: invalid voting delay'
144:        );
        
145:        require(
146:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:            'NounsDAO::initialize: invalid proposal threshold bps'
148:        );

201:        require(
202:            targets.length == values.length &&
203:                targets.length == signatures.length &&
204:                targets.length == calldatas.length,
205:            'NounsDAO::propose: proposal function information arity mismatch'
206:        );

623:        require(
624:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:            'NounsDAO::_setVotingDelay: invalid voting delay'
626:        );

639:        require(
640:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:            'NounsDAO::_setVotingPeriod: invalid voting period'
642:        );

656:        require(
657:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:        );

678:        require(
679:            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
680:                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
681:            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
682:        );

819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L129
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137-L140


&nbsp;
&nbsp;


## [G-07] USE A MORE RECENT VERSION OF SOLIDITY

Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There is 1 instance of this issue:

```solidity
// contracts/base/ERC721Enumerable.sol
28: pragma solidity ^0.8.0;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28

&nbsp;
&nbsp;

## [G-08] DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

`if (<x> == true)` => `if (<x>)`, `if (<x> == false)` => `if (!<x>)`

_There are 2 instances of this issue:

```solidity
// contracts/governance/NounsDAOLogicV1.sol
505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');


// contracts/governance/NounsDAOLogicV2.sol
597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L505
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597

## [G-09] `++I`/`I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN `FOR`- AND `WHILE`-LOOPS

_There are 8 instances of this issue:_

```solidity
// contracts/governance/NounsDAOLogicV1.sol
281:  for (uint256 i = 0; i < proposal.targets.length; i++) {
319:  for (uint256 i = 0; i < proposal.targets.length; i++) {
346:  for (uint256 i = 0; i < proposal.targets.length; i++) {
371:  for (uint256 i = 0; i < proposal.targets.length; i++) {

// contracts/governance/NounsDAOLogicV2.sol
292:  for (uint256 i = 0; i < proposal.targets.length; i++) {
330:  for (uint256 i = 0; i < proposal.targets.length; i++) {
357:  for (uint256 i = 0; i < proposal.targets.length; i++) {
382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
