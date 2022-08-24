# gas optimization

### Don't Initialize Variables with Default Value

#### examples:
```
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::181 => uint32 lower = 0;
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::948 => uint256 lower = 0;
```

### Prefix Increment instead of Postfix Increment

#### examples:
```
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::292 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::330 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::357 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::382 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::948 => uint256 lower = 0;
```


### Cache Array Length Outside of Loop


#### examples:
```
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::70 => return _allTokens.length;
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::121 => uint256 length = ERC721.balanceOf(to);
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::122 => _ownedTokens[to][length] = tokenId;
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::123 => _ownedTokensIndex[tokenId] = length;
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::131 => _allTokensIndex[tokenId] = _allTokens.length;
2022-08-nounsdao\contracts\base\ERC721Enumerable.sol::172 => uint256 lastTokenIndex = _allTokens.length - 1;
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::192 => targets.length == values.length &&
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::193 => targets.length == signatures.length &&
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::194 => targets.length == calldatas.length,
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::197 => require(targets.length != 0, 'NounsDAO::propose: must provide actions');
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::198 => require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::281 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::319 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::346 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::371 => for (uint256 i = 0; i < proposal.targets.length; i++) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::202 => targets.length == values.length &&
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::203 => targets.length == signatures.length &&
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::204 => targets.length == calldatas.length,
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::207 => require(targets.length != 0, 'NounsDAO::propose: must provide actions');
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

#### examples:
```
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::153 => return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::215 => if (srcRep != dstRep && amount > 0) {
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::218 => uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::225 => uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::243 => if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::495 => * Users with > 0 votes receive refunds. Refunds are partial when using a gas priority fee higher than the DAO's cap.
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::509 => * Users with > 0 votes receive refunds. Refunds are partial when using a gas priority fee higher than the DAO's cap.
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::541 => if (votes > 0) {
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::967 => if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
2022-08-nounsdao\contracts\governance\NounsDAOProxy.sol::34 => // The fallback() and receive() functions of Proxy.sol have been used to allow Solidity > 0.6.0 compatibility
```

### Use immutable for OpenZeppelin AccessControl's Roles Declarations

#### examples:
```
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::60 => keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::64 => keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::134 => bytes32 domainSeparator = keccak256(
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::135 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::137 => bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::138 => bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::98 => keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::101 => bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::302 => !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::479 => bytes32 domainSeparator = keccak256(
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::480 => abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::482 => bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

### Use Shift Right/Left instead of Division/Multiplication if possible

#### examples:
```
2022-08-nounsdao\contracts\base\ERC721Checkpointable.sol::184 => uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::88 => uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV1.sol::91 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::80 => uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::83 => uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::86 => uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::89 => uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
2022-08-nounsdao\contracts\governance\NounsDAOLogicV2.sol::951 => uint256 center = upper - (upper - lower) / 2;
```

