# 2022-08-NOUNSDAO
## Gas Optimizations Report

18 total findings

### The usage of `++i` will cost less gas than `i++`. The same change can be applied to `i--` as well.
This change would save up to 6 gas per instance/loop.

_There are **10** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

216:  proposalCount++;

281:  for (uint256 i = 0; i < proposal.targets.length; i++) {

319:  for (uint256 i = 0; i < proposal.targets.length; i++) {

346:  for (uint256 i = 0; i < proposal.targets.length; i++) {

371:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

226:  proposalCount++;

292:  for (uint256 i = 0; i < proposal.targets.length; i++) {

330:  for (uint256 i = 0; i < proposal.targets.length; i++) {

357:  for (uint256 i = 0; i < proposal.targets.length; i++) {

382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### `internal` and `private` functions that are called only once should be inlined.
The execution of a non-inlined function would cost up to 40 more gas because of two extra `jump`s as well as some other instructions.

_There are **4** instances of this issue:_

```solidity
File: contracts/base/ERC721Enumerable.sol

120:  function _addTokenToOwnerEnumeration(address to, uint256 tokenId) private {

130:  function _addTokenToAllTokensEnumeration(uint256 tokenId) private {

143:  function _removeTokenFromOwnerEnumeration(address from, uint256 tokenId) private {

168:  function _removeTokenFromAllTokensEnumeration(uint256 tokenId) private {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

### Using `!= 0` on `uints` costs less gas than `> 0`.
This change saves 3 gas per instance/loop

_There are **7** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

153:  return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;

215:  if (srcRep != dstRep && amount > 0) {

218:  uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

225:  uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

243:  if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

541:  if (votes > 0) {

967:  if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### It costs more gas to initialize non-`constant`/non-`immutable` variables to zero than to let the default of zero be applied
Not overwriting the default for stack variables saves 8 gas. Storage and memory variables have larger savings

_There are **10** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

181:  uint32 lower = 0;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

281:  for (uint256 i = 0; i < proposal.targets.length; i++) {

319:  for (uint256 i = 0; i < proposal.targets.length; i++) {

346:  for (uint256 i = 0; i < proposal.targets.length; i++) {

371:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

292:  for (uint256 i = 0; i < proposal.targets.length; i++) {

330:  for (uint256 i = 0; i < proposal.targets.length; i++) {

357:  for (uint256 i = 0; i < proposal.targets.length; i++) {

382:  for (uint256 i = 0; i < proposal.targets.length; i++) {

948:  uint256 lower = 0;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Using `private` rather than `public` for constants, saves gas
If needed, the values can be read from the verified contract source code, or if there are multiple values there can be a single getter function that returns a tuple of the values of all currently-public constants. Saves 3406-3606 gas in deployment gas due to the compiler not having to create non-payable getter functions for deployment calldata, not having to store the bytes of the value outside of where it's used, and not adding another entry to the method ID table

_There are **31** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

41:   uint8 public constant decimals = 0;

59:   bytes32 public constant DOMAIN_TYPEHASH =

63:   bytes32 public constant DELEGATION_TYPEHASH =
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

67:   string public constant name = 'Nouns DAO';

70:   uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;

73:   uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;

76:   uint256 public constant MIN_VOTING_PERIOD = 5_760;

79:   uint256 public constant MAX_VOTING_PERIOD = 80_640;

82:   uint256 public constant MIN_VOTING_DELAY = 1;

85:   uint256 public constant MAX_VOTING_DELAY = 40_320;

88:   uint256 public constant MIN_QUORUM_VOTES_BPS = 200;

91:   uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;

94:   uint256 public constant proposalMaxOperations = 10;

97:   bytes32 public constant DOMAIN_TYPEHASH =

101:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

59:   string public constant name = 'Nouns DAO';

62:   uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;

65:   uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;

68:   uint256 public constant MIN_VOTING_PERIOD = 5_760;

71:   uint256 public constant MAX_VOTING_PERIOD = 80_640;

74:   uint256 public constant MIN_VOTING_DELAY = 1;

77:   uint256 public constant MAX_VOTING_DELAY = 40_320;

80:   uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200;

83:   uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000;

86:   uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000;

89:   uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;

92:   uint256 public constant proposalMaxOperations = 10;

95:   uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

98:   uint256 public constant REFUND_BASE_GAS = 36000;

101:  bytes32 public constant DOMAIN_TYPEHASH =

105:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Functions that are access-restricted from most users may be marked as `payable`
Marking a function as `payable` reduces gas cost since the compiler does not have to check whether a payment was provided or not. This change will save around 21 gas per function call.

_There are **21** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

363:  function veto(uint256 proposalId) external {

529:  function _setVotingDelay(uint256 newVotingDelay) external {

545:  function _setVotingPeriod(uint256 newVotingPeriod) external {

562:  function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {

580:  function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external {

597:  function _setPendingAdmin(address newPendingAdmin) external {

615:  function _acceptAdmin() external {

637:  function _setVetoer(address newVetoer) public {

649:  function _burnVetoPower() public {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

374:  function veto(uint256 proposalId) external {

621:  function _setVotingDelay(uint256 newVotingDelay) external {

637:  function _setVotingPeriod(uint256 newVotingPeriod) external {

654:  function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external {

673:  function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {

701:  function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {

726:  function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {

799:  function _setPendingAdmin(address newPendingAdmin) external {

817:  function _acceptAdmin() external {

839:  function _setVetoer(address newVetoer) public {

851:  function _burnVetoPower() public {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

78:   function _setImplementation(address implementation_) public {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### `++i`/`i++` should be `unchecked{++I}`/`unchecked{I++}` in `for`-loops
When an increment or any arithmetic operation is not possible to overflow it should be placed in `unchecked{}` block. \This is because of the default compiler overflow and underflow safety checks since Solidity version 0.8.0. \In for-loops it saves around 30-40 gas **per loop**

_There are **8** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

281:  for (uint256 i = 0; i < proposal.targets.length; i++) {

319:  for (uint256 i = 0; i < proposal.targets.length; i++) {

346:  for (uint256 i = 0; i < proposal.targets.length; i++) {

371:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

292:  for (uint256 i = 0; i < proposal.targets.length; i++) {

330:  for (uint256 i = 0; i < proposal.targets.length; i++) {

357:  for (uint256 i = 0; i < proposal.targets.length; i++) {

382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Usage of `uint`s/`int`s smaller than 32 bytes (256 bits) incurs overhead
'When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.' \ https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html \ Use a larger size then downcast where needed

_There are **49** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

41:   uint8 public constant decimals = 0;

48:   uint32 fromBlock;

53:   mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;

130:  uint8 v,

152:  uint32 nCheckpoints = numCheckpoints[account];

166:  uint32 nCheckpoints = numCheckpoints[account];

181:  uint32 lower = 0;

182:  uint32 upper = nCheckpoints - 1;

184:  uint32 center = upper - (upper - lower) / 2;

217:  uint32 srcRepNum = numCheckpoints[srcRep];

224:  uint32 dstRepNum = numCheckpoints[dstRep];

234:  uint32 nCheckpoints,

238:  uint32 blockNumber = safe32(
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

70:   event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);

111:  event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

114:  event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

117:  event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);

218:  uint8 support;

322:  uint8 support;

352:  uint16 minQuorumVotesBPS;

354:  uint16 maxQuorumVotesBPS;

357:  uint32 quorumCoefficient;

363:  uint32 fromBlock;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

101:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

450:  function castVote(uint256 proposalId, uint8 support) external {

462:  uint8 support,

474:  uint8 support,

475:  uint8 v,

499:  uint8 support
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

105:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

489:  function castVote(uint256 proposalId, uint8 support) external {

503:  function castRefundableVote(uint256 proposalId, uint8 support) external {

520:  uint8 support,

535:  uint8 support,

554:  uint8 support,

566:  uint8 support,

567:  uint8 v,

591:  uint8 support

673:  function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external {

687:  uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;

701:  function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external {

714:  uint16 oldMaxQuorumVotesBPS = params.maxQuorumVotesBPS;

726:  function _setQuorumCoefficient(uint32 newQuorumCoefficient) external {

730:  uint32 oldQuorumCoefficient = params.quorumCoefficient;

749:  uint16 newMinQuorumVotesBPS,

750:  uint16 newMaxQuorumVotesBPS,

751:  uint32 newQuorumCoefficient

923:  uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');

965:  uint32 blockNumber = safe32(block.number, 'block number exceeds 32 bits');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Don't compare boolean expressions to boolean literals
Use `if(x)`/`if(!x)` instead of `if(x == true)`/`if(x == false)`.

_There are **2** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

505:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

597:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol



### Array`.length` should not be looked up in every loop of a for-loop
Cache the array target length in a stack varibale (e.g. `uint256 len = proposal.targets.length;`)

_There are **8** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

281:  for (uint256 i = 0; i < proposal.targets.length; i++) {

319:  for (uint256 i = 0; i < proposal.targets.length; i++) {

346:  for (uint256 i = 0; i < proposal.targets.length; i++) {

371:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

292:  for (uint256 i = 0; i < proposal.targets.length; i++) {

330:  for (uint256 i = 0; i < proposal.targets.length; i++) {

357:  for (uint256 i = 0; i < proposal.targets.length; i++) {

382:  for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Splitting `require()` statements that use `&&` saves gas
Instead of using `&&` on single `require` check using two `require` checks can save gas

_There are **19** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

126:  require(
127:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:      'NounsDAO::initialize: invalid voting period'
129:  );

130:  require(
131:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:      'NounsDAO::initialize: invalid voting delay'
133:  );

134:  require(
135:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:      'NounsDAO::initialize: invalid proposal threshold'
137:  );

138:  require(
139:      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:      'NounsDAO::initialize: invalid proposal threshold'
141:  );

191:  require(
192:      targets.length == values.length &&
193:          targets.length == signatures.length &&
194:          targets.length == calldatas.length,
195:      'NounsDAO::propose: proposal function information arity mismatch'
196:  );

531:  require(
532:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:      'NounsDAO::_setVotingDelay: invalid voting delay'
534:  );

547:  require(
548:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:      'NounsDAO::_setVotingPeriod: invalid voting period'
550:  );

564:  require(
565:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:  );

582:  require(
583:      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:  );

617:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

137:  require(
138:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:      'NounsDAO::initialize: invalid voting period'
140:  );

141:  require(
142:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:      'NounsDAO::initialize: invalid voting delay'
144:  );

145:  require(
146:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:      'NounsDAO::initialize: invalid proposal threshold bps'
148:  );

201:  require(
202:      targets.length == values.length &&
203:          targets.length == signatures.length &&
204:          targets.length == calldatas.length,
205:      'NounsDAO::propose: proposal function information arity mismatch'
206:  );

623:  require(
624:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:      'NounsDAO::_setVotingDelay: invalid voting delay'
626:  );

639:  require(
640:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:      'NounsDAO::_setVotingPeriod: invalid voting period'
642:  );

656:  require(
657:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:  );

677:  require(
678:      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:  );

819:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Use `calldata` instead of `memory` for function parameters
If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory. Try to use calldata as a data location because it will avoid copies and also makes sure that the data cannot be modified.

_There are **23** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

253:  function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

258:  function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {

266:  string memory errorMessage

276:  string memory errorMessage
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

175:  address[] memory targets,

176:  uint256[] memory values,

177:  string[] memory signatures,

178:  bytes[] memory calldatas,

179:  string memory description

297:  string memory signature,

298:  bytes memory data,
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

185:  address[] memory targets,

186:  uint256[] memory values,

187:  string[] memory signatures,

188:  bytes[] memory calldatas,

189:  string memory description

308:  string memory signature,

309:  bytes memory data,

536:  string memory reason

906:  DynamicQuorumParams memory params

964:  function _writeQuorumParamsCheckpoint(DynamicQuorumParams memory params) internal {

1018: function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

94:   function delegateTo(address callee, bytes memory data) internal {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Replace `x <= y` with `x < y + 1`, and `x >= y` with `x > y - 1`
In the EVM, there is no opcode for `>=` or `<=`. When using greater than or equal, two operations are performed: `>` and `=`. Using strict comparison operators hence saves gas

_There are **41** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

142:  require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

172:  if (checkpoints[account][nCheckpoints - 1].fromBlock <= blockNumber) {

269:  require(c >= a, errorMessage);

278:  require(b <= a, errorMessage);
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

127:  votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,

131:  votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,

135:  proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,

139:  quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,

198:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

422:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

428:  } else if (block.number <= proposal.startBlock) {

430:  } else if (block.number <= proposal.endBlock) {

432:  } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < proposal.quorumVotes) {

438:  } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {

502:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

532:  newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,

548:  newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,

565:  newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&

566:  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,

583:  newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

138:  votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,

142:  votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,

146:  proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,

208:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

433:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

439:  } else if (block.number <= proposal.startBlock) {

441:  } else if (block.number <= proposal.endBlock) {

443:  } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {

449:  } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {

594:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

624:  newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,

640:  newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,

657:  newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&

658:  newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,

678:  newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&

679:  newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,

683:  newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,

706:  newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,

710:  params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,

935:  if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {

1019: require(n <= type(uint32).max, errorMessage);
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### `require()/revert()` strings longer than 32 bytes cost extra gas
Each extra memory word of bytes past the original 32 incurs an MSTORE which costs 3 gas

_There are **86** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

140:  require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

141:  require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

142:  require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:  require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/base/ERC721Enumerable.sol

62:   require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:   require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

122:  require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

124:  require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

125:  require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

126:  require(
127:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:      'NounsDAO::initialize: invalid voting period'
129:  );

130:  require(
131:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:      'NounsDAO::initialize: invalid voting delay'
133:  );

134:  require(
135:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:      'NounsDAO::initialize: invalid proposal threshold'
137:  );

138:  require(
139:      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:      'NounsDAO::initialize: invalid proposal threshold'
141:  );

187:  require(
188:      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189:      'NounsDAO::propose: proposer votes below proposal threshold'
190:  );

191:  require(
192:      targets.length == values.length &&
193:          targets.length == signatures.length &&
194:          targets.length == calldatas.length,
195:      'NounsDAO::propose: proposal function information arity mismatch'
196:  );

197:  require(targets.length != 0, 'NounsDAO::propose: must provide actions');

198:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

203:  require(
204:      proposersLatestProposalState != ProposalState.Active,
205:      'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
206:  );

207:  require(
208:      proposersLatestProposalState != ProposalState.Pending,
209:      'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
210:  );

275:  require(
276:      state(proposalId) == ProposalState.Succeeded,
277:      'NounsDAO::queue: proposal can only be queued if it is succeeded'
278:  );

301:  require(
302:      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
303:      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
304:  );

313:  require(
314:      state(proposalId) == ProposalState.Queued,
315:      'NounsDAO::execute: proposal can only be executed if it is queued'
316:  );

336:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

339:  require(
340:      msg.sender == proposal.proposer ||
341:          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
342:      'NounsDAO::cancel: proposer above threshold'
343:  );

364:  require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

366:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

422:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

485:  require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

501:  require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

502:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

505:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

530:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

531:  require(
532:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:      'NounsDAO::_setVotingDelay: invalid voting delay'
534:  );

546:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

547:  require(
548:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:      'NounsDAO::_setVotingPeriod: invalid voting period'
550:  );

563:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

564:  require(
565:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:  );

581:  require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

582:  require(
583:      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:  );

599:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

617:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

638:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

651:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

133:  require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

135:  require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

136:  require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

137:  require(
138:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:      'NounsDAO::initialize: invalid voting period'
140:  );

141:  require(
142:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:      'NounsDAO::initialize: invalid voting delay'
144:  );

145:  require(
146:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:      'NounsDAO::initialize: invalid proposal threshold bps'
148:  );

197:  require(
198:      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:      'NounsDAO::propose: proposer votes below proposal threshold'
200:  );

201:  require(
202:      targets.length == values.length &&
203:          targets.length == signatures.length &&
204:          targets.length == calldatas.length,
205:      'NounsDAO::propose: proposal function information arity mismatch'
206:  );

207:  require(targets.length != 0, 'NounsDAO::propose: must provide actions');

208:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213:  require(
214:      proposersLatestProposalState != ProposalState.Active,
215:      'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:  );

217:  require(
218:      proposersLatestProposalState != ProposalState.Pending,
219:      'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:  );

286:  require(
287:      state(proposalId) == ProposalState.Succeeded,
288:      'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:  );

312:  require(
313:      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314:      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:  );

324:  require(
325:      state(proposalId) == ProposalState.Queued,
326:      'NounsDAO::execute: proposal can only be executed if it is queued'
327:  );

347:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350:  require(
351:      msg.sender == proposal.proposer ||
352:          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353:      'NounsDAO::cancel: proposer above threshold'
354:  );

375:  require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

377:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:  require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:  require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

594:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

623:  require(
624:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:      'NounsDAO::_setVotingDelay: invalid voting delay'
626:  );

638:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

639:  require(
640:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:      'NounsDAO::_setVotingPeriod: invalid voting period'
642:  );

655:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

656:  require(
657:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:  );

674:  require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

677:  require(
678:      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:  );

682:  require(
683:      newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:      'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:  );

702:  require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705:  require(
706:      newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:      'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:  );

709:  require(
710:      params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:      'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:  );

727:  require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

79:   require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

80:   require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Use custom errors rather than `revert()`/`require()` strings to save gas
Custom errors are available from solidity version 0.8.4. Custom errors save ~50 gas each time they're hitby avoiding having to allocate and store the revert string. Not defining the strings also save deployment gas

_There are **90** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

140:  require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

141:  require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');

142:  require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');

164:  require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/base/ERC721Enumerable.sol

62:   require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');

77:   require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

122:  require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

123:  require(msg.sender == admin, 'NounsDAO::initialize: admin only');

124:  require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

125:  require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

126:  require(
127:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:      'NounsDAO::initialize: invalid voting period'
129:  );

130:  require(
131:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:      'NounsDAO::initialize: invalid voting delay'
133:  );

134:  require(
135:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:      'NounsDAO::initialize: invalid proposal threshold'
137:  );

138:  require(
139:      quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:      'NounsDAO::initialize: invalid proposal threshold'
141:  );

187:  require(
188:      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189:      'NounsDAO::propose: proposer votes below proposal threshold'
190:  );

191:  require(
192:      targets.length == values.length &&
193:          targets.length == signatures.length &&
194:          targets.length == calldatas.length,
195:      'NounsDAO::propose: proposal function information arity mismatch'
196:  );

197:  require(targets.length != 0, 'NounsDAO::propose: must provide actions');

198:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

203:  require(
204:      proposersLatestProposalState != ProposalState.Active,
205:      'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
206:  );

207:  require(
208:      proposersLatestProposalState != ProposalState.Pending,
209:      'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
210:  );

275:  require(
276:      state(proposalId) == ProposalState.Succeeded,
277:      'NounsDAO::queue: proposal can only be queued if it is succeeded'
278:  );

301:  require(
302:      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
303:      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
304:  );

313:  require(
314:      state(proposalId) == ProposalState.Queued,
315:      'NounsDAO::execute: proposal can only be executed if it is queued'
316:  );

336:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

339:  require(
340:      msg.sender == proposal.proposer ||
341:          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
342:      'NounsDAO::cancel: proposer above threshold'
343:  );

364:  require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

365:  require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

366:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

422:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

485:  require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

501:  require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

502:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

505:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

530:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

531:  require(
532:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:      'NounsDAO::_setVotingDelay: invalid voting delay'
534:  );

546:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

547:  require(
548:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:      'NounsDAO::_setVotingPeriod: invalid voting period'
550:  );

563:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

564:  require(
565:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:  );

581:  require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

582:  require(
583:      newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:      'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:  );

599:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

617:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

638:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

651:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

133:  require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');

134:  require(msg.sender == admin, 'NounsDAO::initialize: admin only');

135:  require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

136:  require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

137:  require(
138:      votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:      'NounsDAO::initialize: invalid voting period'
140:  );

141:  require(
142:      votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:      'NounsDAO::initialize: invalid voting delay'
144:  );

145:  require(
146:      proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:      'NounsDAO::initialize: invalid proposal threshold bps'
148:  );

197:  require(
198:      nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
199:      'NounsDAO::propose: proposer votes below proposal threshold'
200:  );

201:  require(
202:      targets.length == values.length &&
203:          targets.length == signatures.length &&
204:          targets.length == calldatas.length,
205:      'NounsDAO::propose: proposal function information arity mismatch'
206:  );

207:  require(targets.length != 0, 'NounsDAO::propose: must provide actions');

208:  require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

213:  require(
214:      proposersLatestProposalState != ProposalState.Active,
215:      'NounsDAO::propose: one live proposal per proposer, found an already active proposal'
216:  );

217:  require(
218:      proposersLatestProposalState != ProposalState.Pending,
219:      'NounsDAO::propose: one live proposal per proposer, found an already pending proposal'
220:  );

286:  require(
287:      state(proposalId) == ProposalState.Succeeded,
288:      'NounsDAO::queue: proposal can only be queued if it is succeeded'
289:  );

312:  require(
313:      !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
314:      'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
315:  );

324:  require(
325:      state(proposalId) == ProposalState.Queued,
326:      'NounsDAO::execute: proposal can only be executed if it is queued'
327:  );

347:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

350:  require(
351:      msg.sender == proposal.proposer ||
352:          nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
353:      'NounsDAO::cancel: proposer above threshold'
354:  );

375:  require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

376:  require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

377:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

433:  require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');

577:  require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

593:  require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');

594:  require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');

597:  require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

622:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

623:  require(
624:      newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:      'NounsDAO::_setVotingDelay: invalid voting delay'
626:  );

638:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

639:  require(
640:      newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:      'NounsDAO::_setVotingPeriod: invalid voting period'
642:  );

655:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

656:  require(
657:      newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:          newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:      'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:  );

674:  require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

677:  require(
678:      newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:          newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:      'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:  );

682:  require(
683:      newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:      'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:  );

702:  require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

705:  require(
706:      newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:      'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:  );

709:  require(
710:      params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:      'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:  );

727:  require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

79:   require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');

80:   require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Expressions for constant values such as a call to `keccak256()`, should use `immutable` rather than `constant`
It is expected that the value should be converted into a constant value at compile time. But actually the expression is re-calculated each time the constant is referenced.

_There are **2** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

101:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

105:  bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Use a more recent version of solidity
Use a solidity version of at least 0.8.2 to get simple compiler automatic inlining Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than revert()/require() strings Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

_There are **6** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

35:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/base/ERC721Enumerable.sol

28:   pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

33:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

61:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

53:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

36:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Cache in memory the multiple function variable that are declared in storage
If making multiple changes to a single variable of custom (struct) type, consider caching it in memory and after changes saving it to storage. That way multiple SSTORE and SLOAD opcodes (each costing 100+ gas) will be avoided and replaced with the chealp MSTORE and MLOAD (costing 3 gas).

_There are **12** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

227:  Proposal storage newProposal = _proposals[proposalCount];

290:  Proposal storage proposal = _proposals[proposalId];

328:  Proposal storage proposal = _proposals[proposalId];

349:  Proposal storage proposal = _proposals[proposalId];

379:  Proposal storage proposal = _proposals[proposalId];

413:  Proposal storage p = _proposals[proposalId];

434:  Proposal storage proposal = _proposals[proposalId];

463:  Proposal storage proposal = _proposals[proposalId];

595:  Proposal storage proposal = _proposals[proposalId];

596:  Receipt storage receipt = proposal.receipts[voter];

866:  function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {

878:  Proposal storage proposal = _proposals[proposalId];
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol