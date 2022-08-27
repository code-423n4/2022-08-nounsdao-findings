## make i++ into ++i to save a m copy
```
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
## make array.length into a memory var to save gas 
```
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
## make i into uncecked to save gas 
```
governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {

```
## make require into errors to save gas 

```
governance/NounsDAOLogicV2.sol:133:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
governance/NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
governance/NounsDAOLogicV2.sol:135:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
governance/NounsDAOLogicV2.sol:136:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
governance/NounsDAOLogicV2.sol:137:        require(
governance/NounsDAOLogicV2.sol:141:        require(
governance/NounsDAOLogicV2.sol:145:        require(
governance/NounsDAOLogicV2.sol:197:        require(
governance/NounsDAOLogicV2.sol:201:        require(
governance/NounsDAOLogicV2.sol:207:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
governance/NounsDAOLogicV2.sol:208:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
governance/NounsDAOLogicV2.sol:213:            require(
governance/NounsDAOLogicV2.sol:217:            require(
governance/NounsDAOLogicV2.sol:286:        require(
governance/NounsDAOLogicV2.sol:312:        require(
governance/NounsDAOLogicV2.sol:324:        require(
governance/NounsDAOLogicV2.sol:347:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
governance/NounsDAOLogicV2.sol:350:        require(
governance/NounsDAOLogicV2.sol:375:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
governance/NounsDAOLogicV2.sol:376:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
governance/NounsDAOLogicV2.sol:377:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
governance/NounsDAOLogicV2.sol:433:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
governance/NounsDAOLogicV2.sol:501:     * @dev Reentrancy is defended against in `castVoteInternal` at the `receipt.hasVoted == false` require statement.
governance/NounsDAOLogicV2.sol:516:     * @dev Reentrancy is defended against in `castVoteInternal` at the `receipt.hasVoted == false` require statement.
governance/NounsDAOLogicV2.sol:531:     * @dev Reentrancy is defended against in `castVoteInternal` at the `receipt.hasVoted == false` require statement.
governance/NounsDAOLogicV2.sol:577:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
governance/NounsDAOLogicV2.sol:593:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
governance/NounsDAOLogicV2.sol:594:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
governance/NounsDAOLogicV2.sol:597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
governance/NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
governance/NounsDAOLogicV2.sol:623:        require(
governance/NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
governance/NounsDAOLogicV2.sol:639:        require(
governance/NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
governance/NounsDAOLogicV2.sol:656:        require(
governance/NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:677:        require(
governance/NounsDAOLogicV2.sol:682:        require(
governance/NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:705:        require(
governance/NounsDAOLogicV2.sol:709:        require(
governance/NounsDAOLogicV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
governance/NounsDAOLogicV2.sol:801:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
governance/NounsDAOLogicV2.sol:819:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV2.sol:840:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
governance/NounsDAOLogicV2.sol:853:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
governance/NounsDAOLogicV2.sol:874:     * @notice Quorum votes required for a specific proposal to succeed
governance/NounsDAOLogicV2.sol:892:     * @notice Calculates the required quorum of for-votes based on the amount of against-votes
governance/NounsDAOLogicV2.sol:893:     *     The more against-votes there are for a proposal, the higher the required quorum is.
governance/NounsDAOLogicV2.sol:901:     * @return quorumVotes The required quorum
governance/NounsDAOLogicV2.sol:1019:        require(n <= type(uint32).max, errorMessage);
governance/NounsDAOProxy.sol:79:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
governance/NounsDAOProxy.sol:80:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
governance/NounsDAOProxyV2.sol:82:        require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
governance/NounsDAOProxyV2.sol:83:        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
governance/NounsDAOLogicV1.sol:122:        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
governance/NounsDAOLogicV1.sol:123:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
governance/NounsDAOLogicV1.sol:124:        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
governance/NounsDAOLogicV1.sol:125:        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
governance/NounsDAOLogicV1.sol:126:        require(
governance/NounsDAOLogicV1.sol:130:        require(
governance/NounsDAOLogicV1.sol:134:        require(
governance/NounsDAOLogicV1.sol:138:        require(
governance/NounsDAOLogicV1.sol:187:        require(
governance/NounsDAOLogicV1.sol:191:        require(
governance/NounsDAOLogicV1.sol:197:        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
governance/NounsDAOLogicV1.sol:198:        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
governance/NounsDAOLogicV1.sol:203:            require(
governance/NounsDAOLogicV1.sol:207:            require(
governance/NounsDAOLogicV1.sol:275:        require(
governance/NounsDAOLogicV1.sol:301:        require(
governance/NounsDAOLogicV1.sol:313:        require(
governance/NounsDAOLogicV1.sol:336:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
governance/NounsDAOLogicV1.sol:339:        require(
governance/NounsDAOLogicV1.sol:364:        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
governance/NounsDAOLogicV1.sol:365:        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
governance/NounsDAOLogicV1.sol:366:        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
governance/NounsDAOLogicV1.sol:422:        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
governance/NounsDAOLogicV1.sol:485:        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
governance/NounsDAOLogicV1.sol:501:        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
governance/NounsDAOLogicV1.sol:502:        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
governance/NounsDAOLogicV1.sol:505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
governance/NounsDAOLogicV1.sol:530:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
governance/NounsDAOLogicV1.sol:531:        require(
governance/NounsDAOLogicV1.sol:546:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
governance/NounsDAOLogicV1.sol:547:        require(
governance/NounsDAOLogicV1.sol:563:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
governance/NounsDAOLogicV1.sol:564:        require(
governance/NounsDAOLogicV1.sol:581:        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
governance/NounsDAOLogicV1.sol:582:        require(
governance/NounsDAOLogicV1.sol:599:        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
governance/NounsDAOLogicV1.sol:617:        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV1.sol:638:        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
governance/NounsDAOLogicV1.sol:651:        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
NounsDescriptor.sol:65:        require(!arePartsLocked, 'Parts are locked');


```
## make require statement under 32 bytes to save gas
```
  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
governance/NounsDAOLogicV2.sol:819: 
```
## make onlyowner function payable to save gas there is no check on msg.value 
these functions 
```
governance/NounsDAOLogicV2.sol:134:        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
governance/NounsDAOLogicV2.sol:622:        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
governance/NounsDAOLogicV2.sol:638:        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
governance/NounsDAOLogicV2.sol:655:        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
governance/NounsDAOLogicV2.sol:674:        require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:702:        require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
governance/NounsDAOLogicV2.sol:727:        require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
```
## 