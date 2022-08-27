## \[G-01\] Splitting `require()` statements that use `&&` saves gas

If you're using the Optimizer at 200, instead of using the `&&` operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement:

```solidity
governance/NounsDAOLogicV2.sol:

	137:   require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );


	141:    require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );

	145:    require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );

	201:      require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
```
 ```solidity  
	623:      require(
        		newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
	639:    require(
        newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
        'NounsDAO::_setVotingPeriod: invalid voting period'
        );

	656:    require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );

	677:    require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );

	819:    require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
 
```solidity
governance/NounsDAOLogicV2.sol:

	126:    require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );

	130:     require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );

	134:     require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );

	138:     require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );

	191:    require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );

	547:      require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );

	564:       require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );

	582:    require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );

	617:     require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
	
```


## \[G-2\] `++i` costs less gas compared to `i++` or `i += 1`

 ++i costs less gas than i++, especially when it's used in for-loops (--i/i-- too)
```solidity
Instances include:

governance/NounsDAOLogicV1.sol:281          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:381          for (uint256 i = 0; i < proposal.targets.length; i++) {

```

## \[G-3\] No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (`0` for `uint`, `false` for `bool`, `address(0)` for address...).

```solidity
base/ERC721Checkpointable.sol:41            uint8 public constant decimals = 0;
base/ERC721Checkpointable.sol:181           uint32 lower = 0;
governance/NounsDAOLogicV1.sol:281          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:381          for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## \[G-4\] array.length should not be looked up in every loop of a for-loop

```solidity
governance/NounsDAOLogicV1.sol:281          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:292          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357          for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:381          for (uint256 i = 0; i < proposal.targets.length; i++) {
```