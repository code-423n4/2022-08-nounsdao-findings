1. For-Loops: Cache array length outside of loops

Reading an array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.
Caching the array length in the stack saves around 3 gas per iteration.

contracts/governance/NounsDAOLogicV2.sol
(Before) 292: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 292: uint256 len = proposal.targets.length;
        for (uint256 i = 0; i < len; i++) {

(Before) 330: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 330: uint256 len = proposal.targets.length;
        for (uint256 i = 0; i < len; i++) {

(Before) 357: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 357: uint256 len = proposal.targets.length;
        for (uint256 i = 0; i < len; i++) {

(Before) 382: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 382: uint256 len = proposal.targets.length;
        for (uint256 i = 0; i < len; i++) {

2. For-Loops: Index increments can be left unchecked

From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks.
In for-loops, as it is impossible for the index to overflow, it can be left unchecked to save gas every iteration.

contracts/governance/NounsDAOLogicV2.sol
(Before) 292: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 292: for (uint256 i = 0; i < proposal.targets.length;) {
unchecked { ++i; }

(Before) 330: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 330:  for (uint256 i = 0; i < proposal.targets.length;) {
unchecked { ++i; }

(Before) 357:  for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 357:  for (uint256 i = 0; i < proposal.targets.length;) {
unchecked { ++i; }

(Before) 382: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 382: for (uint256 i = 0; i < proposal.targets.length;) {
unchecked { ++i; }

3. Arithmetics: ++i costs less gas compared to i++ or i += 1

++i costs less gas compared to i++ or i += 1 for unsigned integers, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

contracts/governance/NounsDAOLogicV2.sol
226: proposalCount++;

4. Arithmetics: Use != 0 instead of > 0 for unsigned integers

uint will never go below 0. Thus, > 0 is gas inefficient in comparisons as checking if != 0 is sufficient and costs less gas.

contracts/governance/NounsDAOLogicV2.sol
541: if (votes > 0) {
967: if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {

5. Arithmetics: Unchecking arithmetic operations that cannot underflow/overflow

From Solidity v0.8 onwards, all arithmetic operations come with implicit overflow and underflow checks. In some instances, an overflow/underflow is impossible, such as:
1.A check is in place before the arithmetic operation
2.The value realistically cannot overflow, such as amount of ETH sent

As such, gas can be saved by using an unchecked block to remove the implicit checks:

contracts/governance/NounsDAOLogicV2.sol
(Before) 949: uint256 upper = len - 1;
(After) 949: unchecked { uint256 upper = len - 1; }

(Before) 958: upper = center - 1;
(After) 958: unchecked { upper = center - 1; }

6. Visibility: Consider declaring constants as non-public to save gas

If a constant is not used outside of its contract, declaring it as private or internal instead of public can save gas.

contracts/governance/NounsDAOLogicV2.sol
59: string public constant name = 'Nouns DAO';
62: uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;
65: uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;
68: uint256 public constant MIN_VOTING_PERIOD = 5_760;
71: uint256 public constant MAX_VOTING_PERIOD = 80_640;
74: uint256 public constant MIN_VOTING_DELAY = 1;
77: uint256 public constant MAX_VOTING_DELAY = 40_320;
80: uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200;
83: uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000;
86: uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000;
89: uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;
92: uint256 public constant proposalMaxOperations = 10;
95: uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
98: uint256 public constant REFUND_BASE_GAS = 36000;
101: bytes32 public constant DOMAIN_TYPEHASH =
105: bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

7. Visibility: public functions can be set to external

Calls to external functions are cheaper than public functions. Thus, if a function is not used internally in any contract, it should be set to external to save gas and improve code readability.

contracts/governance/NounsDAOLogicV2.sol
184: function propose(
432: function state(uint256 proposalId) public view returns (ProposalState) {
748: function _setDynamicQuorumParams(
839: function _setVetoer(address newVetoer) public {
851: function _burnVetoPower() public {
862: function proposalThreshold() public view returns (uint256) {
877: function quorumVotes(uint256 proposalId) public view returns (uint256) {
903: function dynamicQuorumVotes(
922: function getDynamicQuorumParamsAt(uint256 blockNumber_) public view returns (DynamicQuorumParams memory) {
995: function minQuorumVotes() public view returns (uint256) {
1002: function maxQuorumVotes() public view returns (uint256) {

8. Unecessary initialization of variables with default values

Uninitialized variables are assigned with a default value depending on its type:

1.uint: 0
2.bool: false
3.address: address(0)

contracts/governance/NounsDAOLogicV2.sol
(Before) 330: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 330: for (uint256 i; i < proposal.targets.length; i++) {

(Before) 357: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 357: for (uint256 i; i < proposal.targets.length; i++) {

(Before) 382: for (uint256 i = 0; i < proposal.targets.length; i++) {
(After) 382: for (uint256 i; i < proposal.targets.length; i++) {

(Before) 948: uint256 lower = 0;
(After) 948: uint256 lower;

9. Errors: Reduce the length of error messages (long revert strings)

Shortening revert strings to fit in 32 bytes will decrease deployment time gas and will decrease runtime gas when the revert condition is met.
Revert strings that are longer than 32 bytes require at least one additional mstore, along with additional overhead for computing memory offset, etc.
In these instances, consider shortening the revert strings to fit within 32 bytes, or using custom errors:

contracts/governance/NounsDAOLogicV2.sol
133: require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
134: require(msg.sender == admin, 'NounsDAO::initialize: admin only');
135: require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
136: require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
137: require(
141: require(
145: require(
197: require(
201: require(
207: require(targets.length != 0, 'NounsDAO::propose: must provide actions');
208: require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
213: require(
217: require(
286: require(
312: require(
324: require(
347: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
350: require(
375: require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
376: require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
377: require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');
433: require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
577: require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
593: require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
594: require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
597: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
622: require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
623:  require(
638: require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
639: require(
655: require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
656: require(
674: require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
677: require(
682: require(
702: require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
705: require(
709: require(
727: require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
801: require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
819: require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
840: require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
853: require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

10. >= is cheaper than > (and <= cheaper than <)

Strict inequalities (>) are more expensive than non-strict ones (>=). This is due to some supplementary checks (ISZERO, 3 gas). This also holds true between <= and <.
Consider replacing strict inequalities with non-strict ones to save some gas here:

contracts/governance/NounsDAOLogicV2.sol
(Before) 541: if (votes > 0) {
(After) 541: if (votes >= 1) {

(Before) 967: if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
(After) 967: if (pos >= 1 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {

11. Use shift right/left instead of division/multiplication if possible

While the DIV / MUL opcode uses 5 gas, the SHR / SHL opcode only uses 3 gas. Furthermore, beware that Solidity's division operation also includes a division-by-0 prevention which is bypassed using shifting. Eventually, overflow checks are never performed for shift operations as they are done for arithmetic operations. Instead, the result is always truncated.

Use >> 1 instead of / 2
Use >> 2 instead of / 4
Use << 3 instead of * 8

contracts/governance/NounsDAOLogicV2.sol
951: uint256 center = upper - (upper - lower) / 2;

12. Splitting require() statements that use && saves gas

Instead of using the && operator in a single require statement to check multiple conditions,using multiple require statements with 1 condition per require statement will save 3 GAS per &&:

contracts/governance/NounsDAOLogicV2.sol
137: require(
141: require(
145: require(
201: require(
623: require(
639: require(
656: require(
677: require(

13. Use calldata instead of memory to save gas for read-only arguments

contracts/governance/NounsDAOLogicV2.sol
189: string memory description
536: string memory reason

14. Internal functions only called once can be inlined to save gas

contracts/governance/NounsDAOLogicV2.sol
866: function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
974: function _refundGas(uint256 startGas) internal {
1010: function getChainIdInternal() internal view returns (uint256) {

15. 