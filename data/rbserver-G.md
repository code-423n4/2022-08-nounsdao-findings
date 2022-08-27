## [G-01] STATE VARIABLES THAT ARE ACCESSED FOR MULTIPLE TIMES CAN BE CACHED IN MEMORY
State variables that are accessed for multiple times can be cached in memory to save gas by using `mload` instead of `sload`.

`newProposal.id` can be cached in memory for the following code.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L247-L278
```solidity
        latestProposalIds[newProposal.proposer] = newProposal.id;

        /// @notice Maintains backwards compatibility with GovernorBravo events
        emit ProposalCreated(
            newProposal.id,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            description
        );

        /// @notice Updated event with `proposalThreshold` and `minQuorumVotes`
        /// @notice `minQuorumVotes` is always zero since V2 introduces dynamic quorum with checkpoints
        emit ProposalCreatedWithRequirements(
            newProposal.id,
            msg.sender,
            targets,
            values,
            signatures,
            calldatas,
            newProposal.startBlock,
            newProposal.endBlock,
            newProposal.proposalThreshold,
            minQuorumVotes(),
            description
        );

        return newProposal.id;
```

`proposal.proposer` can be cached in memory for the following code.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L350-L354
```solidity
        require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );
```

`proposal.id` can be cached in memory for the following code.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L466-L469
```solidity
                id: proposal.id,
                proposer: proposal.proposer,
                proposalThreshold: proposal.proposalThreshold,
                quorumVotes: quorumVotes(proposal.id),
```

`proposal.creationBlock` can be cached in memory for the following code.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L867-L870
```solidity
        if (proposal.creationBlock == 0) {
            return proposal.startBlock - votingDelay;
        }
        return proposal.creationBlock;
```


## [G-02] VARIABLE DOES NOT NEED TO BE INITIALIZED TO ITS DEFAULT VALUE
Explicitly initializing a variable with its default value costs more gas than uninitializing it. For example, `uint256 i` can be used instead of `uint256 i = 0` in the following code.
```solidity
contracts\governance\NounsDAOLogicV1.sol
  281: for (uint256 i = 0; i < proposal.targets.length; i++) {
  319: for (uint256 i = 0; i < proposal.targets.length; i++) {
  346: for (uint256 i = 0; i < proposal.targets.length; i++) {
  371: for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts\governance\NounsDAOLogicV2.sol
  292: for (uint256 i = 0; i < proposal.targets.length; i++) {
  330: for (uint256 i = 0; i < proposal.targets.length; i++) {
  357: for (uint256 i = 0; i < proposal.targets.length; i++) {
  382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-03] ARRAY LENGTH CAN BE CACHED OUTSIDE OF LOOP
Caching the array length outside of the loop and using the cached length in the loop costs less gas than reading the array length for each iteration. For example, `proposal.targets.length` in the following code can be cached outside of the loop like `uint256 proposalTargetsLength = proposal.targets.length`, and `i < proposalTargetsLength` can be used for each iteration.
```solidity
contracts\governance\NounsDAOLogicV1.sol
  281: for (uint256 i = 0; i < proposal.targets.length; i++) {
  319: for (uint256 i = 0; i < proposal.targets.length; i++) {
  346: for (uint256 i = 0; i < proposal.targets.length; i++) {
  371: for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts\governance\NounsDAOLogicV2.sol
  292: for (uint256 i = 0; i < proposal.targets.length; i++) {
  330: for (uint256 i = 0; i < proposal.targets.length; i++) {
  357: for (uint256 i = 0; i < proposal.targets.length; i++) {
  382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-04] ++VARIABLE CAN BE USED INSTEAD OF VARIABLE++
++variable costs less gas than variable++. For example, `i++` can be changed to `++i` in the following code.
```solidity
contracts\governance\NounsDAOLogicV1.sol
  281: for (uint256 i = 0; i < proposal.targets.length; i++) {
  319: for (uint256 i = 0; i < proposal.targets.length; i++) {
  346: for (uint256 i = 0; i < proposal.targets.length; i++) {
  371: for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts\governance\NounsDAOLogicV2.sol
  292: for (uint256 i = 0; i < proposal.targets.length; i++) {
  330: for (uint256 i = 0; i < proposal.targets.length; i++) {
  357: for (uint256 i = 0; i < proposal.targets.length; i++) {
  382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-05] ARITHMETIC OPERATIONS THAT DO NOT OVERFLOW CAN BE UNCHECKED
Explicitly unchecking arithmetic operations that do not overflow by wrapping these in `unchecked {}` costs less gas than implicitly checking these.

For the following loops, `unchecked {++i}` at the end of the loop block can be used, where `i` is the counter variable.
```solidity
contracts\governance\NounsDAOLogicV1.sol
  281: for (uint256 i = 0; i < proposal.targets.length; i++) {
  319: for (uint256 i = 0; i < proposal.targets.length; i++) {
  346: for (uint256 i = 0; i < proposal.targets.length; i++) {
  371: for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts\governance\NounsDAOLogicV2.sol
  292: for (uint256 i = 0; i < proposal.targets.length; i++) {
  330: for (uint256 i = 0; i < proposal.targets.length; i++) {
  357: for (uint256 i = 0; i < proposal.targets.length; i++) {
  382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## [G-06] REVERT REASON STRINGS CAN BE SHORTENED TO FIT IN 32 BYTES
Revert reason strings that are longer than 32 bytes need more memory space and more mstore operation(s) than these that are shorter than 32 bytes when reverting. Please consider shortening the following revert reason strings.

```solidity
contracts\governance\NounsDAOLogicV1.sol
  303: 'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'  

contracts\governance\NounsDAOLogicV2.sol
  133: require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once'); 
  135: require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address'); 
  136: require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address'); 
  139: 'NounsDAO::initialize: invalid voting period' 
  143: 'NounsDAO::initialize: invalid voting delay' 
  147: 'NounsDAO::initialize: invalid proposal threshold bps' 
  199: 'NounsDAO::propose: proposer votes below proposal threshold'
  205: 'NounsDAO::propose: proposal function information arity mismatch'    
  207: require(targets.length != 0, 'NounsDAO::propose: must provide actions');    
  208: require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');    
  215: 'NounsDAO::propose: one live proposal per proposer, found an already active proposal'    
  219: 'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'    
  288: 'NounsDAO::queue: proposal can only be queued if it is succeeded'   
  314: 'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'  
  326: 'NounsDAO::execute: proposal can only be executed if it is queued'  
  347: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');  
  353: 'NounsDAO::cancel: proposer above threshold'  
  375: require(vetoer != address(0), 'NounsDAO::veto: veto power burned');  
  377: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');  
  433: require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');   
  577: require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature'); 
  593: require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed'); 
  594: require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type'); 
  597: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted'); 
  622: require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only'); 
  625: 'NounsDAO::_setVotingDelay: invalid voting delay' 
  638: require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only'); 
  641: 'NounsDAO::_setVotingPeriod: invalid voting period' 
  655: require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only'); 
  659: 'NounsDAO::_setProposalThreshold: invalid proposal threshold bps' 
  674: require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only'); 
  680: 'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps' 
  684: 'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max' 
  702: require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only'); 
  707: 'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps' 
  711: 'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max' 
  727: require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only'); 
  801: require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only'); 
  819: require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
  840: require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only'); 
  853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only'); 
  923: uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');  

contracts\governance\NounsDAOProxy.sol
  79: require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');  
  80: require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');  
```