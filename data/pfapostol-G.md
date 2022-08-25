##### Summary

Gas savings are estimated using the gas report of existing `yarn test` tests (the sum of all deployment costs and the sum of the costs of calling all methods) and may vary depending on the implementation of the fix. I keep my version of the fix for each finding and can provide them if you need.
Some optimizations (mostly logical) cannot be scored with a exact gas quantity, so no score is displayed for them.

Gas Optimizations
||Issue|Instances|Estimated gas(deployments)|Estimated gas(method call)|
|:---:|:---|:---:|:---:|:---:|
|**1**|Use Custom Errors instead of Revert/Require Strings to save Gas|94|2 041 729|-|
|**2**|`require()`/`revert()` strings longer than 32 bytes cost extra gas|90|1 291 762|-|
|**3**|Use modifiers instead of repeating require|36|427 492|-|
|**4**|Using `private` rather than `public` for constants, saves gas|28|453 903|-|
|**5**|Do not manually check overflow in solidity version >= 0.8.0||193 752|5 892|
|**6**|`_burnVetoPower` repeats `_setVetoer` by calling it|1|93 814|-|
|**7**|Increment optimization|10|-|-|
|**7.1**|Prefix increments are cheaper than postfix increments, especially when it's used in for-loops|10|7 692|-|
|**7.2**|\<x\> = \<x\> + 1 even more efficient than pre increment|10|50 842|-|
|**7.3**|Expression can be unchecked when overflow is not possible|8|83 728|-|
|**8**|It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied|24|63 727|22 188|
|**9**|Division by two should use bit shifting|2|11 480|-|
|**10**|Cache storage variable|22|3 504|792|
|**11**|Use built in block.chainid instead of assembler functions|3|2 064||
|**12**|Use `type(uintXXX).max` instead of `2\*\*XXX`|2|228||
|**13**|Change the order of checking input parameters|15|-|-|

**Total: instances over issues**

---

1. **Use Custom Errors instead of Revert/Require Strings to save Gas (94 instances)**

   Deployment Gas Saved: **2041729**
   Solidity 0.8.4 introduced custom errors. They are more gas efficient than revert strings, when it comes to deploy cost as well as runtime cost when the revert condition is met. Use custom errors instead of revert strings for gas savings.

   Custom errors save ~50 gas each time they"re hitby avoiding having to allocate and store the revert string.
   Additional gas is saved due to the lack of defining string. [https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth](https://blog.soliditylang.org/2021/04/21/custom-errors/#errors-in-depth)

   - contracts/governance/NounsDAOLogicV1.sol:[122-141](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122-L141), [187-198](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L187-L198), [203-210](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L203-L210), [275-278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L275-L278), [301-304](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L301-L304), [313-316](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L313-L316), [336](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L336), [339-343](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L339-L343), [364-366](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L364-L366), [422](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L422), [485](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L485), [501-502](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L501-L502), [505](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505), [530-534](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L530-L534), [546-550](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L546-L550), [563-568](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L563-L568), [581-585](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L581-L585), [599](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L599), [617](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L617), [638](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L638), [651](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L651)

   ```solidity
   122        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
   123        require(msg.sender == admin, 'NounsDAO::initialize: admin only');
   124        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
   125        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
   126        require(
   127            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   128            'NounsDAO::initialize: invalid voting period'
   129        );
   130        require(
   131            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   132            'NounsDAO::initialize: invalid voting delay'
   133        );
   134        require(
   135            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   136            'NounsDAO::initialize: invalid proposal threshold'
   137        );
   138        require(
   139            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
   140            'NounsDAO::initialize: invalid proposal threshold'
   141        );
   ...
   187        require(
   188            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
   189            'NounsDAO::propose: proposer votes below proposal threshold'
   190        );
   191        require(
   192            targets.length == values.length &&
   193                targets.length == signatures.length &&
   194                targets.length == calldatas.length,
   195            'NounsDAO::propose: proposal function information arity mismatch'
   196        );
   197        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
   198        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
   ...
   203            require(
   204                proposersLatestProposalState != ProposalState.Active,
   205                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
   206            );
   207            require(
   208                proposersLatestProposalState != ProposalState.Pending,
   209                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
   210            );
   ...
   275        require(
   276            state(proposalId) == ProposalState.Succeeded,
   277            'NounsDAO::queue: proposal can only be queued if it is succeeded'
   278        );
   ...
   301        require(
   302            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
   303            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
   304        );
   ...
   313        require(
   314            state(proposalId) == ProposalState.Queued,
   315            'NounsDAO::execute: proposal can only be executed if it is queued'
   316        );
   ...
   336        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
   ...
   339        require(
   340            msg.sender == proposal.proposer ||
   341                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
   342            'NounsDAO::cancel: proposer above threshold'
   343        );
   ...
   364        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
   365        require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
   366        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
   ...
   422        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
   ...
   485        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
   ...
   501        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
   502        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
   ...
   505        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
   ...
   530        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
   531        require(
   532            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   533            'NounsDAO::_setVotingDelay: invalid voting delay'
   534        );
   ...
   546        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
   547        require(
   548            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   549            'NounsDAO::_setVotingPeriod: invalid voting period'
   550        );
   ...
   563        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
   564        require(
   565            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   566                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   567            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
   568        );
   ...
   581        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
   582        require(
   583            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
   584            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
   585        );
   ...
   599        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
   ...
   617        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
   ...
   638        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
   ...
   651        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[133-148](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133-L148), [197-208](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197-L208), [213-220](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L213-L220), [286-289](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L286-L289), [312-315](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L312-L315), [324-327](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L324-L327), [347](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347), [350-354](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L350-L354), [375-377](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L375-L377), [433](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L433), [577](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L577), [593-594](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L593-L594), [597](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597), [622-626](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L622-L626), [638-642](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L638-L642), [655-660](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L655-L660), [674](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L674), [677-685](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L677-L685), [702](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L702), [705-712](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L705-L712), [727](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L727), [801](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L801), [819](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L819), [840](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840), [853](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853), [1019](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1019)

   ```solidity
   133         require(address(timelock) == address(0), "NounsDAO::initialize: can only initialize once");
   134         require(msg.sender == admin, "NounsDAO::initialize: admin only");
   135         require(timelock_ != address(0), "NounsDAO::initialize: invalid timelock address");
   136         require(nouns_ != address(0), "NounsDAO::initialize: invalid nouns address");
   137         require(
   138             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   139             "NounsDAO::initialize: invalid voting period"
   140         );
   141         require(
   142             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   143             "NounsDAO::initialize: invalid voting delay"
   144         );
   145         require(
   146             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   147             "NounsDAO::initialize: invalid proposal threshold bps"
   148         );
   ...
   197         require(
   198             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
   199             "NounsDAO::propose: proposer votes below proposal threshold"
   200         );
   201         require(
   202             targets.length == values.length &&
   203                 targets.length == signatures.length &&
   204                 targets.length == calldatas.length,
   205             "NounsDAO::propose: proposal function information arity mismatch"
   206         );
   207         require(targets.length != 0, "NounsDAO::propose: must provide actions");
   208         require(targets.length <= proposalMaxOperations, "NounsDAO::propose: too many actions");
   ...
   213             require(
   214                 proposersLatestProposalState != ProposalState.Active,
   215                 "NounsDAO::propose: one live proposal per proposer, found an already active proposal"
   216             );
   217             require(
   218                 proposersLatestProposalState != ProposalState.Pending,
   219                 "NounsDAO::propose: one live proposal per proposer, found an already pending proposal"
   220             );
   ...
   286         require(
   287             state(proposalId) == ProposalState.Succeeded,
   288             "NounsDAO::queue: proposal can only be queued if it is succeeded"
   289         );
   ...
   312         require(
   313             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
   314             "NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta"
   315         );
   ...
   324         require(
   325             state(proposalId) == ProposalState.Queued,
   326             "NounsDAO::execute: proposal can only be executed if it is queued"
   327         );
   ...
   347         require(state(proposalId) != ProposalState.Executed, "NounsDAO::cancel: cannot cancel executed proposal");
   ...
   350         require(
   351             msg.sender == proposal.proposer ||
   352                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
   353             "NounsDAO::cancel: proposer above threshold"
   354         );
   ...
   375         require(vetoer != address(0), "NounsDAO::veto: veto power burned");
   376         require(msg.sender == vetoer, "NounsDAO::veto: only vetoer");
   377         require(state(proposalId) != ProposalState.Executed, "NounsDAO::veto: cannot veto executed proposal");
   ...
   433         require(proposalCount >= proposalId, "NounsDAO::state: invalid proposal id");
   ...
   577         require(signatory != address(0), "NounsDAO::castVoteBySig: invalid signature");
   ...
   593         require(state(proposalId) == ProposalState.Active, "NounsDAO::castVoteInternal: voting is closed");
   594         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
   ...
   597         require(receipt.hasVoted == false, "NounsDAO::castVoteInternal: voter already voted");
   ...
   622         require(msg.sender == admin, "NounsDAO::_setVotingDelay: admin only");
   623         require(
   624             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   625             "NounsDAO::_setVotingDelay: invalid voting delay"
   626         );
   ...
   638         require(msg.sender == admin, "NounsDAO::_setVotingPeriod: admin only");
   639         require(
   640             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   641             "NounsDAO::_setVotingPeriod: invalid voting period"
   642         );
   ...
   655         require(msg.sender == admin, "NounsDAO::_setProposalThresholdBPS: admin only");
   656         require(
   657             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   658                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   659             "NounsDAO::_setProposalThreshold: invalid proposal threshold bps"
   660         );
   ...
   674         require(msg.sender == admin, "NounsDAO::_setMinQuorumVotesBPS: admin only");
   ...
   677         require(
   678             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
   679                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
   680             "NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps"
   681         );
   682         require(
   683             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
   684             "NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max"
   685         );
   ...
   702         require(msg.sender == admin, "NounsDAO::_setMaxQuorumVotesBPS: admin only");
   ...
   705         require(
   706             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
   707             "NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps"
   708         );
   709         require(
   710             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
   711             "NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max"
   712         );
   ...
   727         require(msg.sender == admin, "NounsDAO::_setQuorumCoefficient: admin only");
   ...
   801         require(msg.sender == admin, "NounsDAO::_setPendingAdmin: admin only");
   ...
   819         require(msg.sender == pendingAdmin && msg.sender != address(0), "NounsDAO::_acceptAdmin: pending admin only");
   ...
   840         require(msg.sender == vetoer, "NounsDAO::_setVetoer: vetoer only");
   ...
   853         require(msg.sender == vetoer, "NounsDAO::_burnVetoPower: vetoer only");
   ...
   1019        require(n <= type(uint32).max, errorMessage);
   ```

   - contracts/governance/NounsDAOProxy.sol:[79-80](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79-L80)

   ```solidity
   79         require(msg.sender == admin, "NounsDAOProxy::_setImplementation: admin only");
   80         require(implementation_ != address(0), "NounsDAOProxy::_setImplementation: invalid implementation address");
   ```

   - contracts/base/ERC721Enumerable.sol:[77](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L77)

   ```solidity
   77          require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
   ```

   - contracts/base/ERC721Checkpointable.sol:[140-142](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140-L142), [164](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L164), [254](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L254), [259](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L259), [269](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L269), [278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L278)

   ```solidity
   140         require(signatory != address(0), "ERC721Checkpointable::delegateBySig: invalid signature");
   141         require(nonce == nonces[signatory]++, "ERC721Checkpointable::delegateBySig: invalid nonce");
   142         require(block.timestamp <= expiry, "ERC721Checkpointable::delegateBySig: signature expired");
   ...
   164         require(blockNumber < block.number, "ERC721Checkpointable::getPriorVotes: not yet determined");
   ...
   254         require(n < 2**32, errorMessage);
   ...
   259         require(n < 2**96, errorMessage);
   ...
   269         require(c >= a, errorMessage);
   ...
   278         require(b <= a, errorMessage);
   ```

2. **`require()`/`revert()` strings longer than 32 bytes cost extra gas (90 instances)**

   Deployment Gas Saved: **1291762**

   - contracts/governance/NounsDAOLogicV1.sol:[122-141](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122-L141), [187-198](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L187-L198), [203-210](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L203-L210), [275-278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L275-L278), [301-304](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L301-L304), [313-316](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L313-L316), [336](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L336), [339-343](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L339-L343), [364-366](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L364-L366), [422](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L422), [485](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L485), [501-502](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L501-L502), [505](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L505), [530-534](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L530-L534), [546-550](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L546-L550), [563-568](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L563-L568), [581-585](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L581-L585), [599](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L599), [617](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L617), [638](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L638), [651](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L651)

   ```solidity
   122        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
   ...
   124        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
   125        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
   126        require(
   127            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   128            'NounsDAO::initialize: invalid voting period'
   129        );
   130        require(
   131            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   132            'NounsDAO::initialize: invalid voting delay'
   133        );
   134        require(
   135            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   136            'NounsDAO::initialize: invalid proposal threshold'
   137        );
   138        require(
   139            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
   140            'NounsDAO::initialize: invalid proposal threshold'
   141        );
   ...
   187        require(
   188            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
   189            'NounsDAO::propose: proposer votes below proposal threshold'
   190        );
   191        require(
   192            targets.length == values.length &&
   193                targets.length == signatures.length &&
   194                targets.length == calldatas.length,
   195            'NounsDAO::propose: proposal function information arity mismatch'
   196        );
   197        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
   198        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
   ...
   203            require(
   204                proposersLatestProposalState != ProposalState.Active,
   205                'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
   206            );
   207            require(
   208                proposersLatestProposalState != ProposalState.Pending,
   209                'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
   210            );
   ...
   275        require(
   276            state(proposalId) == ProposalState.Succeeded,
   277            'NounsDAO::queue: proposal can only be queued if it is succeeded'
   278        );
   ...
   301        require(
   302            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
   303            'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
   304        );
   ...
   313        require(
   314            state(proposalId) == ProposalState.Queued,
   315            'NounsDAO::execute: proposal can only be executed if it is queued'
   316        );
   ...
   336        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
   ...
   339        require(
   340            msg.sender == proposal.proposer ||
   341                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
   342            'NounsDAO::cancel: proposer above threshold'
   343        );
   ...
   364        require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
   ...
   366        require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
   ...
   422        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
   ...
   485        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
   ...
   501        require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
   502        require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
   ...
   505        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
   ...
   530        require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
   531        require(
   532            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   533            'NounsDAO::_setVotingDelay: invalid voting delay'
   534        );
   ...
   546        require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
   547        require(
   548            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   549            'NounsDAO::_setVotingPeriod: invalid voting period'
   550        );
   ...
   563        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
   564        require(
   565            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   566                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   567            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
   568        );
   ...
   581        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
   582        require(
   583            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
   584            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
   585        );
   ...
   599        require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
   ...
   617        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
   ...
   638        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
   ...
   651        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[133-148](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133-L148), [197-208](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197-L208), [213-220](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L213-L220), [286-289](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L286-L289), [312-315](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L312-L315), [324-327](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L324-L327), [347](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347), [350-354](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L350-L354), [375-377](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L375-L377), [433](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L433), [577](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L577), [593-594](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L593-L594), [597](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597), [622-626](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L622-L626), [638-642](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L638-L642), [655-660](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L655-L660), [674](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L674), [677-685](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L677-L685), [702](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L702), [705-712](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L705-L712), [727](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L727), [801](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L801), [819](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L819), [840](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840), [853](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853), [1019](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1019)

   ```solidity
   133         require(address(timelock) == address(0), "NounsDAO::initialize: can only initialize once");
   135         require(timelock_ != address(0), "NounsDAO::initialize: invalid timelock address");
   136         require(nouns_ != address(0), "NounsDAO::initialize: invalid nouns address");
   137         require(
   138             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   139             "NounsDAO::initialize: invalid voting period"
   140         );
   141         require(
   142             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   143             "NounsDAO::initialize: invalid voting delay"
   144         );
   145         require(
   146             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   147             "NounsDAO::initialize: invalid proposal threshold bps"
   148         );
   ...
   197         require(
   198             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
   199             "NounsDAO::propose: proposer votes below proposal threshold"
   200         );
   201         require(
   202             targets.length == values.length &&
   203                 targets.length == signatures.length &&
   204                 targets.length == calldatas.length,
   205             "NounsDAO::propose: proposal function information arity mismatch"
   206         );
   207         require(targets.length != 0, "NounsDAO::propose: must provide actions");
   208         require(targets.length <= proposalMaxOperations, "NounsDAO::propose: too many actions");
   ...
   213             require(
   214                 proposersLatestProposalState != ProposalState.Active,
   215                 "NounsDAO::propose: one live proposal per proposer, found an already active proposal"
   216             );
   217             require(
   218                 proposersLatestProposalState != ProposalState.Pending,
   219                 "NounsDAO::propose: one live proposal per proposer, found an already pending proposal"
   220             );
   ...
   286         require(
   287             state(proposalId) == ProposalState.Succeeded,
   288             "NounsDAO::queue: proposal can only be queued if it is succeeded"
   289         );
   ...
   312         require(
   313             !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
   314             "NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta"
   315         );
   ...
   324         require(
   325             state(proposalId) == ProposalState.Queued,
   326             "NounsDAO::execute: proposal can only be executed if it is queued"
   327         );
   ...
   347         require(state(proposalId) != ProposalState.Executed, "NounsDAO::cancel: cannot cancel executed proposal");
   ...
   350         require(
   351             msg.sender == proposal.proposer ||
   352                 nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
   353             "NounsDAO::cancel: proposer above threshold"
   354         );
   ...
   375         require(vetoer != address(0), "NounsDAO::veto: veto power burned");
   377         require(state(proposalId) != ProposalState.Executed, "NounsDAO::veto: cannot veto executed proposal");
   ...
   433         require(proposalCount >= proposalId, "NounsDAO::state: invalid proposal id");
   ...
   577         require(signatory != address(0), "NounsDAO::castVoteBySig: invalid signature");
   ...
   593         require(state(proposalId) == ProposalState.Active, "NounsDAO::castVoteInternal: voting is closed");
   594         require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
   ...
   597         require(receipt.hasVoted == false, "NounsDAO::castVoteInternal: voter already voted");
   ...
   622         require(msg.sender == admin, "NounsDAO::_setVotingDelay: admin only");
   623         require(
   624             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   625             "NounsDAO::_setVotingDelay: invalid voting delay"
   626         );
   ...
   638         require(msg.sender == admin, "NounsDAO::_setVotingPeriod: admin only");
   639         require(
   640             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   641             "NounsDAO::_setVotingPeriod: invalid voting period"
   642         );
   ...
   655         require(msg.sender == admin, "NounsDAO::_setProposalThresholdBPS: admin only");
   656         require(
   657             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   658                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   659             "NounsDAO::_setProposalThreshold: invalid proposal threshold bps"
   660         );
   ...
   674         require(msg.sender == admin, "NounsDAO::_setMinQuorumVotesBPS: admin only");
   ...
   677         require(
   678             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
   679                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
   680             "NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps"
   681         );
   682         require(
   683             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
   684             "NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max"
   685         );
   ...
   702         require(msg.sender == admin, "NounsDAO::_setMaxQuorumVotesBPS: admin only");
   ...
   705         require(
   706             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
   707             "NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps"
   708         );
   709         require(
   710             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
   711             "NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max"
   712         );
   ...
   727         require(msg.sender == admin, "NounsDAO::_setQuorumCoefficient: admin only");
   ...
   801         require(msg.sender == admin, "NounsDAO::_setPendingAdmin: admin only");
   ...
   819         require(msg.sender == pendingAdmin && msg.sender != address(0), "NounsDAO::_acceptAdmin: pending admin only");
   ...
   840         require(msg.sender == vetoer, "NounsDAO::_setVetoer: vetoer only");
   ...
   853         require(msg.sender == vetoer, "NounsDAO::_burnVetoPower: vetoer only");
   ...
   1019        require(n <= type(uint32).max, errorMessage);
   ```

   - contracts/governance/NounsDAOProxy.sol:[79-80](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79-L80)

   ```solidity
   79         require(msg.sender == admin, "NounsDAOProxy::_setImplementation: admin only");
   80         require(implementation_ != address(0), "NounsDAOProxy::_setImplementation: invalid implementation address");
   ```

   - contracts/base/ERC721Enumerable.sol:[77](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Enumerable.sol#L77)

   ```solidity
   77          require(index < ERC721Enumerable.totalSupply(), "ERC721Enumerable: global index out of bounds");
   ```

   - contracts/base/ERC721Checkpointable.sol:[140-142](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140-L142), [164](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L164), [254](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L254), [259](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L259), [269](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L269), [278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L278)

   ```solidity
   140         require(signatory != address(0), "ERC721Checkpointable::delegateBySig: invalid signature");
   141         require(nonce == nonces[signatory]++, "ERC721Checkpointable::delegateBySig: invalid nonce");
   142         require(block.timestamp <= expiry, "ERC721Checkpointable::delegateBySig: signature expired");
   ...
   164         require(blockNumber < block.number, "ERC721Checkpointable::getPriorVotes: not yet determined");
   ...
   254         require(n < 2**32, errorMessage);
   ...
   259         require(n < 2**96, errorMessage);
   ...
   269         require(c >= a, errorMessage);
   ...
   278         require(b <= a, errorMessage);
   ```

3. **Use modifiers instead of repeating require** (36 instances)

   Deployment Gas Saved: **427492**

   **IsAdmin**:

   Can be included in `NounsDAOProxyStorage` from `contracts/governance/NounsDAOInterfaces.sol`

   Deployment Gas Saved: **182804**

   - contracts/governance/NounsDAOLogicV1.sol:[123](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L123), [530](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L530), [546](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L546), [563](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L563), [581](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L581), [599](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L599)

   ```solidity
   123        require(msg.sender == admin, "NounsDAO::initialize: admin only");
   ...
   530        require(msg.sender == admin, "NounsDAO::_setVotingDelay: admin only");
   ...
   546        require(msg.sender == admin, "NounsDAO::_setVotingPeriod: admin only");
   ...
   563        require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
   ...
   581        require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
   ...
   599        require(msg.sender == admin, "NounsDAO::_setPendingAdmin: admin only");
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[134](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L134), [622](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L622), [638](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L638), [655](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L655), [674](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L674), [702](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L702), [727](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L727), [801](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L801)

   ```solidity
   134         require(msg.sender == admin, 'NounsDAO::initialize: admin only');
   ...
   622         require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
   ...
   638         require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
   ...
   655         require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
   ...
   674         require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
   ...
   702         require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
   ...
   727         require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
   ...
   801         require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
   ```

   - contracts/governance/NounsDAOProxy.sol:[79](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79)

   ```solidity
   79         require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
   ```

   **ValidateVotingPeriod**:

   - contracts/governance/NounsDAOLogicV1.sol:[126-129](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L126-L129), [547-550](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L547-L550)

   ```solidity
   126        require(
   127            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   128            'NounsDAO::initialize: invalid voting period'
   129        );
   ...
   547        require(
   548            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   549            'NounsDAO::_setVotingPeriod: invalid voting period'
   550        );
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[137-140](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L137-L140), [639-642](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L639-L642)

   ```solidity
   137         require(
   138             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
   139             'NounsDAO::initialize: invalid voting period'
   140         );
   ...
   639         require(
   640             newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
   641             'NounsDAO::_setVotingPeriod: invalid voting period'
   642         );
   ```

   **ValidateVotingDelay**:

   - contracts/governance/NounsDAOLogicV1.sol:[130-133](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L130-L133), [531-534](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L531-L534)

   ```solidity
   130        require(
   131            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   132            'NounsDAO::initialize: invalid voting delay'
   133        );
   ...
   531        require(
   532            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   533            'NounsDAO::_setVotingDelay: invalid voting delay'
   534        );
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[141-144](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L141-L144), [623-626](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L623-L626)

   ```solidity
   141         require(
   142             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
   143             'NounsDAO::initialize: invalid voting delay'
   144         );
   ...
   623         require(
   624             newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
   625             'NounsDAO::_setVotingDelay: invalid voting delay'
   626         );
   ```

   **ValidateProposalThreshold**:

   - contracts/governance/NounsDAOLogicV1.sol:[134-137](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L134-L137), [564-568](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L564-L568)

   ```solidity
   134        require(
   135            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   136            'NounsDAO::initialize: invalid proposal threshold'
   137        );
   ...
   564        require(
   565            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   566                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   567            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
   568        );
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[145-148](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L145-L148), [656-660](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L656-L660)

   ```solidity
   145         require(
   146             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
   147             'NounsDAO::initialize: invalid proposal threshold bps'
   148         );
   ...
   656         require(
   657             newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
   658                 newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
   659             'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
   660         );
   ```

   **ValidateQuorumVotes**:

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

   **IsVetoer**:

   - contracts/governance/NounsDAOLogicV1.sol:[638](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L638), [651](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L651)

   ```solidity
   638        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
   ...
   651        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[376](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L376), [840](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840), [853](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853)

   ```solidity
   376         require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
   ...
   840         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
   ...
   853         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
   ```

   **Optional, not included in the gas estimate**:

   ```solidity
   modifier ZeroAddress(address _address) {
       require(_address != address(0), 'NounsDAO ZeroAddress');
       _;
   }
   ```

4. **Using private rather than public for constants, saves gas (28 instances)**

   If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

   Deployment Gas Saved: **453903**

   - contracts/governance/NounsDAOLogicV1.sol:[66-101](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L66-L101)

   ```solidity
   66     /// @notice The name of this contract
   67     string public constant name = 'Nouns DAO';
   68
   69     /// @notice The minimum setable proposal threshold
   70     uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
   71
   72     /// @notice The maximum setable proposal threshold
   73     uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
   74
   75     /// @notice The minimum setable voting period
   76     uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
   77
   78     /// @notice The max setable voting period
   79     uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
   80
   81     /// @notice The min setable voting delay
   82     uint256 public constant MIN_VOTING_DELAY = 1;
   83
   84     /// @notice The max setable voting delay
   85     uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
   86
   87     /// @notice The minimum setable quorum votes basis points
   88     uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
   89
   90     /// @notice The maximum setable quorum votes basis points
   91     uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
   92
   93     /// @notice The maximum number of actions that can be included in a proposal
   94     uint256 public constant proposalMaxOperations = 10; // 10 actions
   95
   96     /// @notice The EIP-712 typehash for the contract's domain
   97     bytes32 public constant DOMAIN_TYPEHASH =
   98         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
   99
   100    /// @notice The EIP-712 typehash for the ballot struct used by the contract
   101    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[58-105](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L58-L105)

   ```solidity
   58      /// @notice The name of this contract
   59      string public constant name = 'Nouns DAO';
   60
   61      /// @notice The minimum setable proposal threshold
   62      uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%
   63
   64      /// @notice The maximum setable proposal threshold
   65      uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%
   66
   67      /// @notice The minimum setable voting period
   68      uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
   69
   70      /// @notice The max setable voting period
   71      uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
   72
   73      /// @notice The min setable voting delay
   74      uint256 public constant MIN_VOTING_DELAY = 1;
   75
   76      /// @notice The max setable voting delay
   77      uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
   78
   79      /// @notice The lower bound of minimum quorum votes basis points
   80      uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
   81
   82      /// @notice The upper bound of minimum quorum votes basis points
   83      uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
   84
   85      /// @notice The upper bound of maximum quorum votes basis points
   86      uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
   87
   88      /// @notice The maximum setable quorum votes basis points
   89      uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
   90
   91      /// @notice The maximum number of actions that can be included in a proposal
   92      uint256 public constant proposalMaxOperations = 10; // 10 actions
   93
   94      /// @notice The maximum priority fee used to cap gas refunds in `castRefundableVote`
   95      uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
   96
   97      /// @notice The vote refund gas overhead, including 7K for ETH transfer and 29K for general transaction overhead
   98      uint256 public constant REFUND_BASE_GAS = 36000;
   99
   100     /// @notice The EIP-712 typehash for the contract's domain
   101     bytes32 public constant DOMAIN_TYPEHASH =
   102         keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
   103
   104     /// @notice The EIP-712 typehash for the ballot struct used by the contract
   105     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
   ```

5. **Do not manually check overflow in solidity >= 0.8.0**

   Deployment Gas Saved: **193752**
   Method Call Gas Saved: **5892**

   Solidity version 0.8+ already implements overflow and underflow checks by default.
   Using the SafeMath library from OpenZeppelin (which is more gas expensive than the 0.8+ overflow checks) is therefore redundant.

   - contracts/governance/NounsDAOLogicV2.sol:[1018-1028](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1018-L1028)

   ```solidity
   1018    function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
   1019        require(n <= type(uint32).max, errorMessage);
   1020        return uint32(n);
   1021    }
   1022
   1023    function safe16(uint256 n) internal pure returns (uint16) {
   1024        if (n > type(uint16).max) {
   1025            revert UnsafeUint16Cast();
   1026        }
   1027        return uint16(n);
   1028    }
   ```

   - contracts/base/ERC721Checkpointable.sol:[253-280](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L253-L280)

   ```solidity
   253    function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
   254        require(n < 2**32, errorMessage);
   255        return uint32(n);
   256    }
   257
   258    function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {
   259        require(n < 2**96, errorMessage);
   260        return uint96(n);
   261    }
   262
   263    function add96(
   264        uint96 a,
   265        uint96 b,
   266        string memory errorMessage
   267    ) internal pure returns (uint96) {
   268        uint96 c = a + b;
   269        require(c >= a, errorMessage);
   270        return c;
   271    }
   272
   273    function sub96(
   274        uint96 a,
   275        uint96 b,
   276        string memory errorMessage
   277    ) internal pure returns (uint96) {
   278        require(b <= a, errorMessage);
   279        return a - b;
   280    }
   ```

6. **`_burnVetoPower` repeats `_setVetoer` by calling it (1 instances)**

   Deployment Gas Saved: **93814**

   `_burnVetoPower` checks for sender == vetoer, then calls `_setVetoer` and checks sender == vetoer again.

   - contracts/governance/NounsDAOLogicV1.sol and contracts/governance/NounsDAOLogicV2.sol

   ```solidity
   function _setVetoer(address newVetoer) public {
       require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

       emit NewVetoer(vetoer, newVetoer);

       vetoer = newVetoer;
   }
   ...
   function _burnVetoPower() public {
       // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
       require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

       _setVetoer(address(0));
   }
   ```

   fix:

   1. Remove `require`

   ```solidity
   function _burnVetoPower() public {
       _setVetoer(address(0));
   }
   ```

   2. Do not call `_setVetoer` - **expensive**

   ```solidity
   function _burnVetoPower() public {
       require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

       emit NewVetoer(vetoer, address(0)); // Or better to use new event like VetoPowerBurned();
       delete vetoer;
   }
   ```

7. **Increment optimization (10 instances)**

   1. **Prefix increments are cheaper than postfix increments, especially when it's used in for-loops (10 instances).**

      Deployment Gas Saved: **7692**

   2. **\<x\> = \<x\> + 1 even more efficient than pre increment.(10 instances) Gas saved:**

      Deployment Gas Saved: **50842**

      - contracts/governance/NounsDAOLogicV1.sol:[216](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L216), [281](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281), [319](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319), [346](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L346), [371](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371)

      ```solidity
      216        proposalCount++;
      ...
      281        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      319        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      346        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      371        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ```

      - contracts/governance/NounsDAOLogicV2.sol:[226](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226), [292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292), [330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330), [357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357), [382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)

      ```solidity
      226         proposalCount++;
      ...
      292         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      330         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      357         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      382         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ```

   3. **Expression can be unchecked when overflow is not possible.(8 instances) Gas saved:**

      Deployment Gas Saved: **83728**

      - contracts/governance/NounsDAOLogicV1.sol:[281](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281), [319](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319), [346](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L346), [371](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371)

      ```solidity
      281        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      319        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      346        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      371        for (uint256 i = 0; i < proposal.targets.length; i++) {
      ```

      - contracts/governance/NounsDAOLogicV2.sol:[292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292), [330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330), [357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357), [382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382)

      ```solidity
      292         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      330         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      357         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ...
      382         for (uint256 i = 0; i < proposal.targets.length; i++) {
      ```

8. **It costs more gas to initialize non-constant/non-immutable variables to zero than to let the default of zero be applied (24 instances)**

   Deployment Gas Saved: **63727**
   Method Call Gas Saved: **22188**

   - contracts/governance/NounsDAOLogicV1.sol:[223](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L223), [230-235](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L230-L235), [281](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281), [319](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319), [346](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L346), [371](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371)

   ```solidity
   223        newProposal.eta = 0;
   ...
   230        newProposal.forVotes = 0;
   231        newProposal.againstVotes = 0;
   232        newProposal.abstainVotes = 0;
   233        newProposal.canceled = false;
   234        newProposal.executed = false;
   235        newProposal.vetoed = false;
   ...
   281        for (uint256 i = 0; i < proposal.targets.length; i++) {
    ...
    319        for (uint256 i = 0; i < proposal.targets.length; i++) {
    ...
    346        for (uint256 i = 0; i < proposal.targets.length; i++) {
    ...
    371        for (uint256 i = 0; i < proposal.targets.length; i++) {
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[231](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L231), [238-243](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L238-L243), [292](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292), [330](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330), [357](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357), [382](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382), [948](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L948)

   ```solidity
   231         newProposal.eta = 0;
   ...
   238         newProposal.forVotes = 0;
   239         newProposal.againstVotes = 0;
   240         newProposal.abstainVotes = 0;
   241         newProposal.canceled = false;
   242         newProposal.executed = false;
   243         newProposal.vetoed = false;
   ...
   292         for (uint256 i = 0; i < proposal.targets.length; i++) {
   ...
   330         for (uint256 i = 0; i < proposal.targets.length; i++) {
   ...
   357         for (uint256 i = 0; i < proposal.targets.length; i++) {
   ...
   382         for (uint256 i = 0; i < proposal.targets.length; i++) {
   ...
   948         uint256 lower = 0;
   ```

   - contracts/base/ERC721Checkpointable.sol:[181](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L181)

   ```solidity
   181         uint32 lower = 0;
   ```

9. **Division by two should use bit shifting**

   Deployment Gas Saved: **11480**

   \<x\> / 2 is the same as \<x\> >> 1. The DIV opcode costs 5 gas, whereas SHR only costs 3 gas

   - contracts/base/ERC721Checkpointable.sol:[184](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L184)

   ```solidity
   184      uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
   ```

   - contracts/governance/NounsDAOLogicV2.sol:[951](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L951)

   ```solidity
   951      uint256 center = upper - (upper - lower) / 2;
   ```

10. **Cache storage variable (22 instances)**

    Deployment Gas Saved: **3504**
    Method Call Gas Saved: **792**

    - contracts/governance/NounsDAOLogicV1.sol:[216-217](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L216-L217), [219](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L219), [237](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L237), [240-241](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L240-L241), [253-254](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L253-L254), [267](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L267)

    ```solidity
    216        proposalCount++;
    217        Proposal storage newProposal = proposals[proposalCount];
    ...
    219        newProposal.id = proposalCount;
    ...
    237        latestProposalIds[newProposal.proposer] = newProposal.id; // Can use proposalCount
    ...
    240        emit ProposalCreated(
    241            newProposal.id,
    ...
    253        emit ProposalCreatedWithRequirements(
    254            newProposal.id,
    ...
    267        return newProposal.id;
    ```

    fix:

    ```solidity
    uint256 l_proposalCount = ++proposalCount; // @note First increment proposalCount then store it in a local variable.
    Proposal storage newProposal = proposals[l_proposalCount];
    newProposal.id = l_proposalCount;
    latestProposalIds[newProposal.proposer] = l_proposalCount;
    emit ProposalCreated(
                   l_proposalCount,
    emit ProposalCreatedWithRequirements(
                   l_proposalCount,
    return l_proposalCount;
    ```

    - contracts/governance/NounsDAOLogicV2.sol:[226-228](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L226-L228), [247](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L247), [251](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L251), [265](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L265), [278](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L278)

    ```solidity
    226         proposalCount++;
    227         Proposal storage newProposal = _proposals[proposalCount];
    228         newProposal.id = proposalCount;
    ...
    247         latestProposalIds[newProposal.proposer] = newProposal.id;
    ...
    251             newProposal.id,
    ...
    265             newProposal.id,
    ...
    278         return newProposal.id;
    ```

    fix:

    ```solidity
    uint256 l_proposalCount = ++proposalCount; // @note First increment proposalCount then store it in a local variable.
    Proposal storage newProposal = proposals[l_proposalCount];
    newProposal.id = l_proposalCount;
    latestProposalIds[newProposal.proposer] = l_proposalCount;
    emit ProposalCreated(
                   l_proposalCount,
    emit ProposalCreatedWithRequirements(
                   l_proposalCount,
    return l_proposalCount;
    ```

    Optional, caching the length of actions in a loop increases gas cost, but should save gas in the long run if a proposal contains more than two actions.

    ```solidity
    8 instances in 2 files         for (uint256 i = 0; i < proposal.targets.length; i++) {
    ```

    fix:

    ```solidity
    uint256 length = proposal.targets.length;
    for (uint256 i = 0; i < length; i++) {
    ```

11. **Use built in block.chainid instead of assembler functions (3 instances)**

    Deployment Gas Saved: **2064**

    - contracts/base/ERC721Checkpointable.sol:[282-288](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L282-L288)

    ```solidity
    282    function getChainId() internal view returns (uint256) {
    283        uint256 chainId;
    284        assembly {
    285            chainId := chainid()
    286        }
    287        return chainId;
    288    }
    ```

    - contracts/governance/NounsDAOLogicV1.sol:[676-682](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L676-L682)

    ```solidity
    676    function getChainIdInternal() internal view returns (uint256) {
    677        uint256 chainId;
    678        assembly {
    679            chainId := chainid()
    680        }
    681        return chainId;
    682    }
    ```

    - contracts/governance/NounsDAOLogicV2.sol:[1010-1016](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1010-L1016)

    ```solidity
    1010    function getChainIdInternal() internal view returns (uint256) {
    1011        uint256 chainId;
    1012        assembly {
    1013            chainId := chainid()
    1014        }
    1015        return chainId;
    1016    }
    ```

12. **Use `type(uintxxx).max` instead of `2\*\*xxx`**

    Deployment Gas Saved: **228**

    - contracts/base/ERC721Checkpointable.sol:[254](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L254), [259](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L259)

    ```solidity
    254     require(n < 2**32, errorMessage);
    ...
    259     require(n < 2**96, errorMessage);
    ```

13. **Change the order of checking input parameters**

    - It's cheaper to first compare with constants, then local variables, then storage. Otherwise, if the transaction first checks the storage variable and then reverts, the user will not refund the gas spent reading from storage.

    - Extcalls are usually very expensive, so this should be the last possible check.

    - It's also better to put RBAC checks at the beginning. Because if a non-admin user calls an admin-only function, it doesn't matter if the input parameters are valid

    **Read the notes in the code for fixes.**

    - contracts/governance/NounsDAOLogicV1.sol:[122-141](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122-L141),[187-198](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L187-L198)

    ```solidity
    122        require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
    123        require(msg.sender == admin, 'NounsDAO::initialize: admin only'); // @note put at the beginning ↑
    124        require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
    125        require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
    126        require(
    127            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
    128            'NounsDAO::initialize: invalid voting period'
    129        ); // @note put before checks with address(0) ↑
    130        require(
    131            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
    132            'NounsDAO::initialize: invalid voting delay'
    133        ); // @note put before checks with address(0) ↑
    134        require(
    135            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
    136            'NounsDAO::initialize: invalid proposal threshold'
    137        ); // @note put before checks with address(0) ↑
    138        require(
    139            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
    140            'NounsDAO::initialize: invalid proposal threshold'
    141        ); // @note put before checks with address(0) ↑
    ...
    187        require(
    188            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
    189            'NounsDAO::propose: proposer votes below proposal threshold'
    190        ); // @note extcall but checks for an eligible user, choose whatever is best for you
    191        require(
    192            targets.length == values.length &&
    193                targets.length == signatures.length &&
    194                targets.length == calldatas.length,
    195            'NounsDAO::propose: proposal function information arity mismatch'
    196        );// @note put under check for 0 and check for a constant ↓
    197        require(targets.length != 0, 'NounsDAO::propose: must provide actions');
    198        require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
    ```

    - contracts/governance/NounsDAOLogicV2.sol:[133-148](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133-L148), [197-208](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197-L208), [675-685](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L675-L685), [703-712](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L703-L712)

    ```solidity
    133         require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
    134         require(msg.sender == admin, 'NounsDAO::initialize: admin only'); // @note put at the beginning ↑
    135         require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
    136         require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
    137         require(
    138             votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
    139             'NounsDAO::initialize: invalid voting period'
    140         ); // @note put before checks with address(0) ↑
    141         require(
    142             votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
    143             'NounsDAO::initialize: invalid voting delay'
    144         ); // @note put before checks with address(0) ↑
    145         require(
    146             proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
    147             'NounsDAO::initialize: invalid proposal threshold bps'
    148         ); // @note put before checks with address(0) ↑
    ...
    197         require(
    198             nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
    199             'NounsDAO::propose: proposer votes below proposal threshold'
    200         ); // @note extcall but checks for an eligible user, choose whatever is best for you
    201         require(
    202             targets.length == values.length &&
    203                 targets.length == signatures.length &&
    204                 targets.length == calldatas.length,
    205             'NounsDAO::propose: proposal function information arity mismatch'
    206         ); // @note put under check for 0 and check for a constant ↓
    207         require(targets.length != 0, 'NounsDAO::propose: must provide actions');
    208         require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
    ...
    675         DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number); // @note put between require ↓ because if the first one fails, then this line is useless
    676
    677         require(
    678             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
    679                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
    680             'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
    681         );
    682         require(
    683             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
    684             'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
    685         );
    ...
    703         DynamicQuorumParams memory params = getDynamicQuorumParamsAt(block.number); // @note put between require ↓ because if the first one fails, then this line is useless
    704
    705         require(
    706             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
    707             'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
    708         );
    709         require(
    710             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
    711             'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
    712         );
    ```
