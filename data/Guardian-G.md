* No need to allocate space for temp storage of updated contract variables purely for event emission, instead just emit the event before the contract variable is set:

- NounsDAOLogicV1.sol::535
- NounsDAOLogicV1.sol::551
- NounsDAOLogicV1.sol::569
- NounsDAOLogicV1.sol::586
- NounsDAOLogicV1.sol::602
- NounsDAOLogicV1.sol::620
- NounsDAOLogicV1.sol::621
- NounsDAOLogicV2.sol::627
- NounsDAOLogicV2.sol::643
- NounsDAOLogicV2.sol::661
- NounsDAOLogicV2.sol::687
- NounsDAOLogicV2.sol::714
- NounsDAOLogicV2.sol::730
- NounsDAOLogicV2.sol::769
- NounsDAOLogicV2.sol::804
- NounsDAOLogicV2.sol::822
- NounsDAOLogicV2.sol::823

* NounsDAOLogicV1::480 can use block.chainid instead of getChainIdInternal to save ~40 gas – also makes the contract shorter for deployment.

* NounsDAOLogicV2.sol::572 can use block.chainid instead of getChainIdInternal to save ~40 gas – also makes the contract shorter for deployment.


* NounsDAOLogicV2::603,605,607 += is more efficient for these additions


* NounsDAOLogicV2.sol::966 pos can be a uint32 since an invariant is that `quorumParamsCheckpoints.length < block.number`.
* NounsDAOLogicV2.sol::924 len can be a uint32 since an invariant is that `quorumParamsCheckpoints.length < block.number`. Similarly lower and upper can be uint32 as well.

* Variables should not be initialized to defaults (uint256 default is 0):
  contracts/base/ERC721Checkpointable.sol::181 => uint32 lower = 0;
  contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::948 => uint256 lower = 0;

 * Length of array should be computed outside of for-loop:
  contracts/base/ERC721Enumerable.sol::70 => return _allTokens.length;
  contracts/base/ERC721Enumerable.sol::121 => uint256 length = ERC721.balanceOf(to);
  contracts/base/ERC721Enumerable.sol::122 => _ownedTokens[to][length] = tokenId;
  contracts/base/ERC721Enumerable.sol::123 => _ownedTokensIndex[tokenId] = length;
  contracts/base/ERC721Enumerable.sol::131 => _allTokensIndex[tokenId] = _allTokens.length;
  contracts/base/ERC721Enumerable.sol::172 => uint256 lastTokenIndex = _allTokens.length - 1;
  contracts/governance/NounsDAOLogicV1.sol::192 => targets.length == values.length &&
  contracts/governance/NounsDAOLogicV1.sol::193 => targets.length == signatures.length &&
  contracts/governance/NounsDAOLogicV1.sol::194 => targets.length == calldatas.length,
  contracts/governance/NounsDAOLogicV1.sol::197 => require(targets.length != 0, 'NounsDAO::propose: must provide actions');
  contracts/governance/NounsDAOLogicV1.sol::198 => require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
  contracts/governance/NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::202 => targets.length == values.length &&
  contracts/governance/NounsDAOLogicV2.sol::203 => targets.length == signatures.length &&
  contracts/governance/NounsDAOLogicV2.sol::204 => targets.length == calldatas.length,
  contracts/governance/NounsDAOLogicV2.sol::207 => require(targets.length != 0, 'NounsDAO::propose: must provide actions');
  contracts/governance/NounsDAOLogicV2.sol::208 => require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
  contracts/governance/NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
  contracts/governance/NounsDAOLogicV2.sol::924 => uint256 len = quorumParamsCheckpoints.length;
  contracts/governance/NounsDAOLogicV2.sol::966 => uint256 pos = quorumParamsCheckpoints.length;

* != is more efficient than > 0 for uint comparisons:
  contracts/base/ERC721Checkpointable.sol::153 => return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
  contracts/base/ERC721Checkpointable.sol::215 => if (srcRep != dstRep && amount > 0) {
  contracts/base/ERC721Checkpointable.sol::218 => uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
  contracts/base/ERC721Checkpointable.sol::225 => uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
  contracts/base/ERC721Checkpointable.sol::243 => if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
  contracts/governance/NounsDAOLogicV2.sol::495 => * Users with > 0 votes receive refunds. Refunds are partial when using a gas priority fee higher than the DAO's cap.
  contracts/governance/NounsDAOLogicV2.sol::509 => * Users with > 0 votes receive refunds. Refunds are partial when using a gas priority fee higher than the DAO's cap.
  contracts/governance/NounsDAOLogicV2.sol::541 => if (votes > 0) {
  contracts/governance/NounsDAOLogicV2.sol::967 => if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {

* Switching from division/multiplication to right-shift/left-shift can save gas:
  contracts/base/ERC721Checkpointable.sol::184 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
  contracts/governance/NounsDAOLogicV1.sol::88 => uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
  contracts/governance/NounsDAOLogicV1.sol::91 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
  contracts/governance/NounsDAOLogicV2.sol::80 => uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
  contracts/governance/NounsDAOLogicV2.sol::83 => uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
  contracts/governance/NounsDAOLogicV2.sol::86 => uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
  contracts/governance/NounsDAOLogicV2.sol::89 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
  contracts/governance/NounsDAOLogicV2.sol::951 => uint256 center = upper - (upper - lower) / 2;
