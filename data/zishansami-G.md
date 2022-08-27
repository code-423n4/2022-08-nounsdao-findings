## Gas Optimizations
 *** 


### G-01: pre-increment `++i/--i` costs less gas than post-increment `i++/i--`
Saves 6 gas per loop in a for loop

Total instances of this issue: 8

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
```solidity
contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
```solidity
contracts/governance/NounsDAOLogicV2.sol

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
```solidity
contracts/governance/NounsDAOLogicV2.sol

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #4
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```solidity
contracts/governance/NounsDAOLogicV2.sol

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #5
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #6
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
```solidity
contracts/governance/NounsDAOLogicV1.sol

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #7
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
```solidity
contracts/governance/NounsDAOLogicV1.sol

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #8
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
```solidity
contracts/governance/NounsDAOLogicV1.sol

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

 *** 


### G-02: `++i/i++` should be placed in unchecked blocks to save gas as it is impossible for them to overflow in for and while loops
Unchecked keyword is available in solidity version `0.8.0` or higher and can be applied to iterator variables to save gas. 
Saves more than `30 gas` per loop.

Total instances of this issue: 8

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
```solidity
contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
```solidity
contracts/governance/NounsDAOLogicV2.sol

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
```solidity
contracts/governance/NounsDAOLogicV2.sol

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #4
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```solidity
contracts/governance/NounsDAOLogicV2.sol

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #5
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #6
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
```solidity
contracts/governance/NounsDAOLogicV1.sol

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #7
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
```solidity
contracts/governance/NounsDAOLogicV1.sol

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #8
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
```solidity
contracts/governance/NounsDAOLogicV1.sol

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

 *** 


### G-03: Length of the array (`<array>.length`) need not be looked up in every iteration of a for-loop
Reading array length at each iteration of the loop takes total 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the `array.length` saves around `3 gas` per iteration.

Total instances of this issue: 8

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
```solidity
contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
```solidity
contracts/governance/NounsDAOLogicV2.sol

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
```solidity
contracts/governance/NounsDAOLogicV2.sol

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #4
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```solidity
contracts/governance/NounsDAOLogicV2.sol

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #5
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #6
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
```solidity
contracts/governance/NounsDAOLogicV1.sol

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #7
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
```solidity
contracts/governance/NounsDAOLogicV1.sol

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #8
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
```solidity
contracts/governance/NounsDAOLogicV1.sol

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

 *** 


### G-04: No need to compare boolean expressions with boolean literals, directly use the expression
if (<x> == true) ==> if (<x>)
if (<x> == false) => if (!<x>)

Total instances of this issue: 2

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597
```solidity
contracts/governance/NounsDAOLogicV2.sol

597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505
```solidity
contracts/governance/NounsDAOLogicV1.sol

505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

 *** 


### G-05: No need to initialize non-constant/non-immutable variables to zero
Since the default value is already zero, overwriting is not required.
Saves `8 gas` per instance.

Total instances of this issue: 10

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292
```solidity
contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330
```solidity
contracts/governance/NounsDAOLogicV2.sol

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357
```solidity
contracts/governance/NounsDAOLogicV2.sol

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #4
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382
```solidity
contracts/governance/NounsDAOLogicV2.sol

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #5
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L948
```solidity
contracts/governance/NounsDAOLogicV2.sol

948:        uint256 lower = 0;

```

instance #6
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281
```solidity
contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #7
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319
```solidity
contracts/governance/NounsDAOLogicV1.sol

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #8
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346
```solidity
contracts/governance/NounsDAOLogicV1.sol

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #9
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371
```solidity
contracts/governance/NounsDAOLogicV1.sol

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```

instance #10
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181
```solidity
contracts/base/ERC721Checkpointable.sol

181:        uint32 lower = 0;

```

 *** 


### G-06: `require()` containing multiple checks joined with && should be broken into multiple require statements

Total instances of this issue: 19

instance #1
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137-L140
```solidity
contracts/governance/NounsDAOLogicV2.sol

137:        require(
138:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:            'NounsDAO::initialize: invalid voting period'
140:        );

```

instance #2
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141-L144
```solidity
contracts/governance/NounsDAOLogicV2.sol

141:        require(
142:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:            'NounsDAO::initialize: invalid voting delay'
144:        );

```

instance #3
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145-L148
```solidity
contracts/governance/NounsDAOLogicV2.sol

145:        require(
146:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:            'NounsDAO::initialize: invalid proposal threshold bps'
148:        );

```

instance #4
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201-L206
```solidity
contracts/governance/NounsDAOLogicV2.sol

201:        require(
202:            targets.length == values.length &&
203:                targets.length == signatures.length &&
204:                targets.length == calldatas.length,
205:            'NounsDAO::propose: proposal function information arity mismatch'
206:        );

```

instance #5
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623-L626
```solidity
contracts/governance/NounsDAOLogicV2.sol

623:        require(
624:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:            'NounsDAO::_setVotingDelay: invalid voting delay'
626:        );

```

instance #6
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639-L642
```solidity
contracts/governance/NounsDAOLogicV2.sol

639:        require(
640:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:            'NounsDAO::_setVotingPeriod: invalid voting period'
642:        );

```

instance #7
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656-L660
```solidity
contracts/governance/NounsDAOLogicV2.sol

656:        require(
657:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:        );

```

instance #8
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677-L681
```solidity
contracts/governance/NounsDAOLogicV2.sol

677:        require(
678:            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:        );

```

instance #9
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819
```solidity
contracts/governance/NounsDAOLogicV2.sol

819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

instance #10
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L129
```solidity
contracts/governance/NounsDAOLogicV1.sol

126:        require(
127:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:            'NounsDAO::initialize: invalid voting period'
129:        );

```

instance #11
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130-L133
```solidity
contracts/governance/NounsDAOLogicV1.sol

130:        require(
131:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:            'NounsDAO::initialize: invalid voting delay'
133:        );

```

instance #12
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134-L137
```solidity
contracts/governance/NounsDAOLogicV1.sol

134:        require(
135:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:            'NounsDAO::initialize: invalid proposal threshold'
137:        );

```

instance #13
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138-L141
```solidity
contracts/governance/NounsDAOLogicV1.sol

138:        require(
139:            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:            'NounsDAO::initialize: invalid proposal threshold'
141:        );

```

instance #14
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191-L196
```solidity
contracts/governance/NounsDAOLogicV1.sol

191:        require(
192:            targets.length == values.length &&
193:                targets.length == signatures.length &&
194:                targets.length == calldatas.length,
195:            'NounsDAO::propose: proposal function information arity mismatch'
196:        );

```

instance #15
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531-L534
```solidity
contracts/governance/NounsDAOLogicV1.sol

531:        require(
532:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:            'NounsDAO::_setVotingDelay: invalid voting delay'
534:        );

```

instance #16
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547-L550
```solidity
contracts/governance/NounsDAOLogicV1.sol

547:        require(
548:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:            'NounsDAO::_setVotingPeriod: invalid voting period'
550:        );

```

instance #17
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564-L568
```solidity
contracts/governance/NounsDAOLogicV1.sol

564:        require(
565:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:        );

```

instance #18
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582-L585
```solidity
contracts/governance/NounsDAOLogicV1.sol

582:        require(
583:            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:        );

```

instance #19
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617
```solidity
contracts/governance/NounsDAOLogicV1.sol

617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

 *** 


### G-07: Using uints/ints smaller than 256 bits increases overhead
Gas usage becomes higher with uint/int smaller than 256 bits because EVM operates on 32 bytes and uses additional operations to reduce the size from 32 bytes to the target size.

Total instances of this issue: 59

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L105
```solidity
contracts/governance/NounsDAOLogicV2.sol

105:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L489
```solidity
contracts/governance/NounsDAOLogicV2.sol

489:    function castVote(uint256 proposalId, uint8 support) external {

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L503
```solidity
contracts/governance/NounsDAOLogicV2.sol

503:    function castRefundableVote(uint256 proposalId, uint8 support) external {

```

instance #4
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L518-L522
```solidity
contracts/governance/NounsDAOLogicV2.sol

518:    function castRefundableVoteWithReason(
519:        uint256 proposalId,
520:        uint8 support,
521:        string calldata reason
522:    ) external {

```

instance #5
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L533-L537
```solidity
contracts/governance/NounsDAOLogicV2.sol

533:    function castRefundableVoteInternal(
534:        uint256 proposalId,
535:        uint8 support,
536:        string memory reason
537:    ) internal {

```

instance #6
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L539
```solidity
contracts/governance/NounsDAOLogicV2.sol

539:        uint96 votes = castVoteInternal(msg.sender, proposalId, support);

```

instance #7
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L552-L556
```solidity
contracts/governance/NounsDAOLogicV2.sol

552:    function castVoteWithReason(
553:        uint256 proposalId,
554:        uint8 support,
555:        string calldata reason
556:    ) external {

```

instance #8
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L564-L570
```solidity
contracts/governance/NounsDAOLogicV2.sol

564:    function castVoteBySig(
565:        uint256 proposalId,
566:        uint8 support,
567:        uint8 v,
568:        bytes32 r,
569:        bytes32 s
570:    ) external {

```

instance #9
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L588-L592
```solidity
contracts/governance/NounsDAOLogicV2.sol

588:    function castVoteInternal(
589:        address voter,
590:        uint256 proposalId,
591:        uint8 support
592:    ) internal returns (uint96) {

```

instance #10
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L600
```solidity
contracts/governance/NounsDAOLogicV2.sol

600:        uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));

```

instance #11
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L673
```solidity
contracts/governance/NounsDAOLogicV2.sol

673:    function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {

```

instance #12
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L687
```solidity
contracts/governance/NounsDAOLogicV2.sol

687:        uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;

```

instance #13
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L701
```solidity
contracts/governance/NounsDAOLogicV2.sol

701:    function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {

```

instance #14
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L714
```solidity
contracts/governance/NounsDAOLogicV2.sol

714:        uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;

```

instance #15
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726
```solidity
contracts/governance/NounsDAOLogicV2.sol

726:    function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {

```

instance #16
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L730
```solidity
contracts/governance/NounsDAOLogicV2.sol

730:        uint32 oldQuorumCoefficient = params.quorumCoefficient;

```

instance #17
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L748-L752
```solidity
contracts/governance/NounsDAOLogicV2.sol

748:    function _setDynamicQuorumParams(
749:        uint16 newMinQuorumVotesBPS,
750:        uint16 newMaxQuorumVotesBPS,
751:        uint32 newQuorumCoefficient
752:    ) public {

```

instance #18
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L923
```solidity
contracts/governance/NounsDAOLogicV2.sol

923:        uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');

```

instance #19
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L965
```solidity
contracts/governance/NounsDAOLogicV2.sol

965:        uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');

```

instance #20
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L101
```solidity
contracts/governance/NounsDAOLogicV1.sol

101:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```

instance #21
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L450
```solidity
contracts/governance/NounsDAOLogicV1.sol

450:    function castVote(uint256 proposalId, uint8 support) external {

```

instance #22
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L460-L464
```solidity
contracts/governance/NounsDAOLogicV1.sol

460:    function castVoteWithReason(
461:        uint256 proposalId,
462:        uint8 support,
463:        string calldata reason
464:    ) external {

```

instance #23
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L472-L478
```solidity
contracts/governance/NounsDAOLogicV1.sol

472:    function castVoteBySig(
473:        uint256 proposalId,
474:        uint8 support,
475:        uint8 v,
476:        bytes32 r,
477:        bytes32 s
478:    ) external {

```

instance #24
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L496-L500
```solidity
contracts/governance/NounsDAOLogicV1.sol

496:    function castVoteInternal(
497:        address voter,
498:        uint256 proposalId,
499:        uint8 support
500:    ) internal returns (uint96) {

```

instance #25
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L508
```solidity
contracts/governance/NounsDAOLogicV1.sol

508:        uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);

```

instance #26
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L70
```solidity
contracts/governance/NounsDAOInterfaces.sol

70:    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

```

instance #27
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L111
```solidity
contracts/governance/NounsDAOInterfaces.sol

111:    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

```

instance #28
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L114
```solidity
contracts/governance/NounsDAOInterfaces.sol

114:    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

```

instance #29
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L117
```solidity
contracts/governance/NounsDAOInterfaces.sol

117:    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);

```

instance #30
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L218
```solidity
contracts/governance/NounsDAOInterfaces.sol

218:        uint8 support;

```

instance #31
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L220
```solidity
contracts/governance/NounsDAOInterfaces.sol

220:        uint96 votes;

```

instance #32
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322
```solidity
contracts/governance/NounsDAOInterfaces.sol

322:        uint8 support;

```

instance #33
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L324
```solidity
contracts/governance/NounsDAOInterfaces.sol

324:        uint96 votes;

```

instance #34
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L352
```solidity
contracts/governance/NounsDAOInterfaces.sol

352:        uint16 minQuorumVotesBPS;

```

instance #35
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L354
```solidity
contracts/governance/NounsDAOInterfaces.sol

354:        uint16 maxQuorumVotesBPS;

```

instance #36
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L357
```solidity
contracts/governance/NounsDAOInterfaces.sol

357:        uint32 quorumCoefficient;

```

instance #37
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L363
```solidity
contracts/governance/NounsDAOInterfaces.sol

363:        uint32 fromBlock;

```

instance #38
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41
```solidity
contracts/base/ERC721Checkpointable.sol

41:    uint8 public constant decimals = 0;

```

instance #39
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L48
```solidity
contracts/base/ERC721Checkpointable.sol

48:        uint32 fromBlock;

```

instance #40
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L49
```solidity
contracts/base/ERC721Checkpointable.sol

49:        uint96 votes;

```

instance #41
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L53
```solidity
contracts/base/ERC721Checkpointable.sol

53:    mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

```

instance #42
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L126-L133
```solidity
contracts/base/ERC721Checkpointable.sol

126:    function delegateBySig(
127:        address delegatee,
128:        uint256 nonce,
129:        uint256 expiry,
130:        uint8 v,
131:        bytes32 r,
132:        bytes32 s
133:    ) public {

```

instance #43
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L152
```solidity
contracts/base/ERC721Checkpointable.sol

152:        uint32 nCheckpoints = numCheckpoints[account];

```

instance #44
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L166
```solidity
contracts/base/ERC721Checkpointable.sol

166:        uint32 nCheckpoints = numCheckpoints[account];

```

instance #45
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181
```solidity
contracts/base/ERC721Checkpointable.sol

181:        uint32 lower = 0;

```

instance #46
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L182
```solidity
contracts/base/ERC721Checkpointable.sol

182:        uint32 upper = nCheckpoints - 1;

```

instance #47
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L205
```solidity
contracts/base/ERC721Checkpointable.sol

205:        uint96 amount = votesToDelegate(delegator);

```

instance #48
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L210-L214
```solidity
contracts/base/ERC721Checkpointable.sol

210:    function _moveDelegates(
211:        address srcRep,
212:        address dstRep,
213:        uint96 amount
214:    ) internal {

```

instance #49
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L217
```solidity
contracts/base/ERC721Checkpointable.sol

217:                uint32 srcRepNum = numCheckpoints[srcRep];

```

instance #50
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L218
```solidity
contracts/base/ERC721Checkpointable.sol

218:                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

```

instance #51
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L219
```solidity
contracts/base/ERC721Checkpointable.sol

219:                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');

```

instance #52
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L224
```solidity
contracts/base/ERC721Checkpointable.sol

224:                uint32 dstRepNum = numCheckpoints[dstRep];

```

instance #53
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L225
```solidity
contracts/base/ERC721Checkpointable.sol

225:                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

```

instance #54
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L226
```solidity
contracts/base/ERC721Checkpointable.sol

226:                uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows');

```

instance #55
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L232-L237
```solidity
contracts/base/ERC721Checkpointable.sol

232:    function _writeCheckpoint(
233:        address delegatee,
234:        uint32 nCheckpoints,
235:        uint96 oldVotes,
236:        uint96 newVotes
237:    ) internal {

```

instance #56
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L238-L241
```solidity
contracts/base/ERC721Checkpointable.sol

238:        uint32 blockNumber = safe32(
239:            block.number,
240:            'ERC721Checkpointable::_writeCheckpoint: block number exceeds 32 bits'
241:        );

```

instance #57
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L263-L267
```solidity
contracts/base/ERC721Checkpointable.sol

263:    function add96(
264:        uint96 a,
265:        uint96 b,
266:        string memory errorMessage
267:    ) internal pure returns (uint96) {

```

instance #58
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L268
```solidity
contracts/base/ERC721Checkpointable.sol

268:        uint96 c = a + b;

```

instance #59
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L273-L277
```solidity
contracts/base/ERC721Checkpointable.sol

273:    function sub96(
274:        uint96 a,
275:        uint96 b,
276:        string memory errorMessage
277:    ) internal pure returns (uint96) {

```

 *** 


### G-08: Using custom errors rather than revert()/require() strings will save deployment gas
Custom errors are available from solidity version 0.8.4.

Total instances of this issue: 95

instance #1
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L133
```solidity
contracts/governance/NounsDAOLogicV2.sol

133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```

instance #2
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L134
```solidity
contracts/governance/NounsDAOLogicV2.sol

134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');

```

instance #3
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135
```solidity
contracts/governance/NounsDAOLogicV2.sol

135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

```

instance #4
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L136
```solidity
contracts/governance/NounsDAOLogicV2.sol

136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

```

instance #5
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137-L140
```solidity
contracts/governance/NounsDAOLogicV2.sol

137:        require(
138:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:            'NounsDAO::initialize: invalid voting period'
140:        );

```

instance #6
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141-L144
```solidity
contracts/governance/NounsDAOLogicV2.sol

141:        require(
142:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:            'NounsDAO::initialize: invalid voting delay'
144:        );

```

instance #7
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145-L148
```solidity
contracts/governance/NounsDAOLogicV2.sol

145:        require(
146:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:            'NounsDAO::initialize: invalid proposal threshold bps'
148:        );

```

instance #8
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L197-L200
```solidity
contracts/governance/NounsDAOLogicV2.sol

197:        require(
198:            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:            'NounsDAO::propose: proposer votes below proposal threshold'
200:        );

```

instance #9
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201-L206
```solidity
contracts/governance/NounsDAOLogicV2.sol

201:        require(
202:            targets.length == values.length &&
203:                targets.length == signatures.length &&
204:                targets.length == calldatas.length,
205:            'NounsDAO::propose: proposal function information arity mismatch'
206:        );

```

instance #10
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L207
```solidity
contracts/governance/NounsDAOLogicV2.sol

207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

```

instance #11
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L208
```solidity
contracts/governance/NounsDAOLogicV2.sol

208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

```

instance #12
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L213-L216
```solidity
contracts/governance/NounsDAOLogicV2.sol

213:            require(
214:                proposersLatestProposalState != ProposalState.Active,
215:                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:            );

```

instance #13
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L217-L220
```solidity
contracts/governance/NounsDAOLogicV2.sol

217:            require(
218:                proposersLatestProposalState != ProposalState.Pending,
219:                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:            );

```

instance #14
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L286-L289
```solidity
contracts/governance/NounsDAOLogicV2.sol

286:        require(
287:            state(proposalId) == ProposalState.Succeeded,
288:            'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:        );

```

instance #15
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L312-L315
```solidity
contracts/governance/NounsDAOLogicV2.sol

312:        require(
313:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314:            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:        );

```

instance #16
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L324-L327
```solidity
contracts/governance/NounsDAOLogicV2.sol

324:        require(
325:            state(proposalId) == ProposalState.Queued,
326:            'NounsDAO::execute: proposal can only be executed if it is queued'
327:        );

```

instance #17
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347
```solidity
contracts/governance/NounsDAOLogicV2.sol

347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

```

instance #18
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L350-L354
```solidity
contracts/governance/NounsDAOLogicV2.sol

350:        require(
351:            msg.sender == proposal.proposer ||
352:                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353:            'NounsDAO::cancel: proposer above threshold'
354:        );

```

instance #19
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375
```solidity
contracts/governance/NounsDAOLogicV2.sol

375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

```

instance #20
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L376
```solidity
contracts/governance/NounsDAOLogicV2.sol

376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

```

instance #21
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377
```solidity
contracts/governance/NounsDAOLogicV2.sol

377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

```

instance #22
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L433
```solidity
contracts/governance/NounsDAOLogicV2.sol

433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

```

instance #23
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L577
```solidity
contracts/governance/NounsDAOLogicV2.sol

577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

```

instance #24
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L593
```solidity
contracts/governance/NounsDAOLogicV2.sol

593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

```

instance #25
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L594
```solidity
contracts/governance/NounsDAOLogicV2.sol

594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

```

instance #26
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597
```solidity
contracts/governance/NounsDAOLogicV2.sol

597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

instance #27
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L622
```solidity
contracts/governance/NounsDAOLogicV2.sol

622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

```

instance #28
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623-L626
```solidity
contracts/governance/NounsDAOLogicV2.sol

623:        require(
624:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:            'NounsDAO::_setVotingDelay: invalid voting delay'
626:        );

```

instance #29
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L638
```solidity
contracts/governance/NounsDAOLogicV2.sol

638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

```

instance #30
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639-L642
```solidity
contracts/governance/NounsDAOLogicV2.sol

639:        require(
640:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:            'NounsDAO::_setVotingPeriod: invalid voting period'
642:        );

```

instance #31
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L655
```solidity
contracts/governance/NounsDAOLogicV2.sol

655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

```

instance #32
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656-L660
```solidity
contracts/governance/NounsDAOLogicV2.sol

656:        require(
657:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:        );

```

instance #33
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L674
```solidity
contracts/governance/NounsDAOLogicV2.sol

674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

```

instance #34
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677-L681
```solidity
contracts/governance/NounsDAOLogicV2.sol

677:        require(
678:            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:        );

```

instance #35
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L682-L685
```solidity
contracts/governance/NounsDAOLogicV2.sol

682:        require(
683:            newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:            'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:        );

```

instance #36
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L702
```solidity
contracts/governance/NounsDAOLogicV2.sol

702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

```

instance #37
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L705-L708
```solidity
contracts/governance/NounsDAOLogicV2.sol

705:        require(
706:            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:        );

```

instance #38
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L709-L712
```solidity
contracts/governance/NounsDAOLogicV2.sol

709:        require(
710:            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:        );

```

instance #39
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L727
```solidity
contracts/governance/NounsDAOLogicV2.sol

727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

```

instance #40
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L801
```solidity
contracts/governance/NounsDAOLogicV2.sol

801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

```

instance #41
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819
```solidity
contracts/governance/NounsDAOLogicV2.sol

819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

instance #42
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L840
```solidity
contracts/governance/NounsDAOLogicV2.sol

840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

```

instance #43
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L853
```solidity
contracts/governance/NounsDAOLogicV2.sol

853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```

instance #44
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1019
```solidity
contracts/governance/NounsDAOLogicV2.sol

1019:        require(n <= type(uint32).max, errorMessage);

```

instance #45
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L122
```solidity
contracts/governance/NounsDAOLogicV1.sol

122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

```

instance #46
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L123
```solidity
contracts/governance/NounsDAOLogicV1.sol

123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');

```

instance #47
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124
```solidity
contracts/governance/NounsDAOLogicV1.sol

124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

```

instance #48
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L125
```solidity
contracts/governance/NounsDAOLogicV1.sol

125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

```

instance #49
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L129
```solidity
contracts/governance/NounsDAOLogicV1.sol

126:        require(
127:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:            'NounsDAO::initialize: invalid voting period'
129:        );

```

instance #50
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130-L133
```solidity
contracts/governance/NounsDAOLogicV1.sol

130:        require(
131:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:            'NounsDAO::initialize: invalid voting delay'
133:        );

```

instance #51
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134-L137
```solidity
contracts/governance/NounsDAOLogicV1.sol

134:        require(
135:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:            'NounsDAO::initialize: invalid proposal threshold'
137:        );

```

instance #52
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138-L141
```solidity
contracts/governance/NounsDAOLogicV1.sol

138:        require(
139:            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:            'NounsDAO::initialize: invalid proposal threshold'
141:        );

```

instance #53
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L187-L190
```solidity
contracts/governance/NounsDAOLogicV1.sol

187:        require(
188:            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189:            'NounsDAO::propose: proposer votes below proposal threshold'
190:        );

```

instance #54
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191-L196
```solidity
contracts/governance/NounsDAOLogicV1.sol

191:        require(
192:            targets.length == values.length &&
193:                targets.length == signatures.length &&
194:                targets.length == calldatas.length,
195:            'NounsDAO::propose: proposal function information arity mismatch'
196:        );

```

instance #55
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L197
```solidity
contracts/governance/NounsDAOLogicV1.sol

197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');

```

instance #56
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L198
```solidity
contracts/governance/NounsDAOLogicV1.sol

198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

```

instance #57
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L203-L206
```solidity
contracts/governance/NounsDAOLogicV1.sol

203:            require(
204:                proposersLatestProposalState != ProposalState.Active,
205:                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
206:            );

```

instance #58
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L207-L210
```solidity
contracts/governance/NounsDAOLogicV1.sol

207:            require(
208:                proposersLatestProposalState != ProposalState.Pending,
209:                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
210:            );

```

instance #59
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L275-L278
```solidity
contracts/governance/NounsDAOLogicV1.sol

275:        require(
276:            state(proposalId) == ProposalState.Succeeded,
277:            'NounsDAO::queue: proposal can only be queued if it is succeeded'
278:        );

```

instance #60
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L301-L304
```solidity
contracts/governance/NounsDAOLogicV1.sol

301:        require(
302:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
303:            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
304:        );

```

instance #61
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L313-L316
```solidity
contracts/governance/NounsDAOLogicV1.sol

313:        require(
314:            state(proposalId) == ProposalState.Queued,
315:            'NounsDAO::execute: proposal can only be executed if it is queued'
316:        );

```

instance #62
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L336
```solidity
contracts/governance/NounsDAOLogicV1.sol

336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

```

instance #63
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L339-L343
```solidity
contracts/governance/NounsDAOLogicV1.sol

339:        require(
340:            msg.sender == proposal.proposer ||
341:                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
342:            'NounsDAO::cancel: proposer above threshold'
343:        );

```

instance #64
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L364
```solidity
contracts/governance/NounsDAOLogicV1.sol

364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

```

instance #65
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L365
```solidity
contracts/governance/NounsDAOLogicV1.sol

365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

```

instance #66
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L366
```solidity
contracts/governance/NounsDAOLogicV1.sol

366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

```

instance #67
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L422
```solidity
contracts/governance/NounsDAOLogicV1.sol

422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

```

instance #68
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L485
```solidity
contracts/governance/NounsDAOLogicV1.sol

485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

```

instance #69
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L501
```solidity
contracts/governance/NounsDAOLogicV1.sol

501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

```

instance #70
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L502
```solidity
contracts/governance/NounsDAOLogicV1.sol

502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

```

instance #71
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505
```solidity
contracts/governance/NounsDAOLogicV1.sol

505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

instance #72
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L530
```solidity
contracts/governance/NounsDAOLogicV1.sol

530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

```

instance #73
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531-L534
```solidity
contracts/governance/NounsDAOLogicV1.sol

531:        require(
532:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:            'NounsDAO::_setVotingDelay: invalid voting delay'
534:        );

```

instance #74
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L546
```solidity
contracts/governance/NounsDAOLogicV1.sol

546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

```

instance #75
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547-L550
```solidity
contracts/governance/NounsDAOLogicV1.sol

547:        require(
548:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:            'NounsDAO::_setVotingPeriod: invalid voting period'
550:        );

```

instance #76
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L563
```solidity
contracts/governance/NounsDAOLogicV1.sol

563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

```

instance #77
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564-L568
```solidity
contracts/governance/NounsDAOLogicV1.sol

564:        require(
565:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:        );

```

instance #78
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L581
```solidity
contracts/governance/NounsDAOLogicV1.sol

581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

```

instance #79
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582-L585
```solidity
contracts/governance/NounsDAOLogicV1.sol

582:        require(
583:            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:        );

```

instance #80
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L599
```solidity
contracts/governance/NounsDAOLogicV1.sol

599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

```

instance #81
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617
```solidity
contracts/governance/NounsDAOLogicV1.sol

617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```

instance #82
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L638
```solidity
contracts/governance/NounsDAOLogicV1.sol

638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

```

instance #83
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L651
```solidity
contracts/governance/NounsDAOLogicV1.sol

651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

```

instance #84
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L79
```solidity
contracts/governance/NounsDAOProxy.sol

79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

```

instance #85
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L80
```solidity
contracts/governance/NounsDAOProxy.sol

80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

```

instance #86
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L98-L99
```solidity
contracts/governance/NounsDAOProxy.sol

98:                revert(add(returnData, 0x20), returndatasize())
99:            }

```

instance #87
Permalink: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L118-L119
```solidity
contracts/governance/NounsDAOProxy.sol

118:                revert(free_mem_ptr, returndatasize())
119:            }

```

instance #88
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140
```solidity
contracts/base/ERC721Checkpointable.sol

140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

```

instance #89
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L141
```solidity
contracts/base/ERC721Checkpointable.sol

141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

```

instance #90
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L142
```solidity
contracts/base/ERC721Checkpointable.sol

142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

```

instance #91
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L164
```solidity
contracts/base/ERC721Checkpointable.sol

164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');

```

instance #92
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L254
```solidity
contracts/base/ERC721Checkpointable.sol

254:        require(n < 2**32, errorMessage);

```

instance #93
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L259
```solidity
contracts/base/ERC721Checkpointable.sol

259:        require(n < 2**96, errorMessage);

```

instance #94
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L269
```solidity
contracts/base/ERC721Checkpointable.sol

269:        require(c >= a, errorMessage);

```

instance #95
Link: https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L278
```solidity
contracts/base/ERC721Checkpointable.sol

278:        require(b <= a, errorMessage);

```

