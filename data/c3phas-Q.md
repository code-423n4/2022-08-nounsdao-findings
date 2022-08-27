## QA

### Missing checks for address(0x0) when assigning values to address state variables
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L150

```solidity
150:        vetoer = vetoer_;
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L156
```solidity
156:        vetoer = vetoer_;
```
### constants should be defined rather than using magic numbers
There are several occurrences of literal values with unexplained meaning .Literal values in the codebase without an explained meaning make the code harder to read, understand and maintain, thus hindering the experience of developers, auditors and external contributors alike.

Developers should define a constant variable for every magic value used , giving it a clear and self-explanatory name. 

File: ERC721Checkpointable.sol [Line 254](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L254)
`2**32`
```solidity
        require(n < 2**32, errorMessage);
```

File: ERC721Checkpointable.sol [Line 259](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L259)
`2**96`
```solidity
        require(n < 2**96, errorMessage);
```

File: NounsDAOLogicV2.sol [Line 908](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L908)**
**10000**
```solidity
        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
```

File: NounsDAOLogicV2.sol [Line 909](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L909)
**1e6**
```solidity
        uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;
```

File: NounsDAOLogicV2.sol [Line 1007](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L1007)
**10000**
```solidity
        return (number * bps) / 10000;
```
File: NounsDAOLogicV1.sol [Line 673](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L673)
**10000**
```solidity
        return (number * bps) / 10000;
```

### Lock pragmas to specific compiler version
Contracts should be deployed with the same compiler version and flags that they have been tested the most with. Locking the pragma helps ensure that contracts do not accidentally get deployed using, for example, the latest compiler which may have higher risks of undiscovered bugs. Contracts may also be deployed by others and the pragma indicates the compiler version intended by the original authors.

File: ERC721Enumerable.sol [Line 28](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L28)
```solidity
pragma solidity ^0.8.0;
```

File: ERC721Checkpointable.sol [Line 35](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L35)
```solidity
pragma solidity ^0.8.6;
```

File: NounsDAOLogicV2.sol [Line 53](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L53)
```solidity
pragma solidity ^0.8.6;
```

File: NounsDAOLogicV1.sol [Line 61](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L61)
```solidity
pragma solidity ^0.8.6;
```
File: NounsDAOProxy.sol [Line 36](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L36)
```solidity
pragma solidity ^0.8.6;
```

### Inconsistency in using custom errors vs using require/revert strings
Throught the majority of the inscope contracts, revert/require statements have been used , however in some contracts we have both revert strings and also custom errors. 
Sample File 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L107

```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
107:     /// @dev Introduced these errors to reduce contract size, to avoid deployment failure
108:     error AdminOnly();
...
134:         require(msg.sender == admin, 'NounsDAO::initialize: admin only');
```
From just the above snippet, you can see the inconsistency , we've defined an error **AdminOnly** but still use revert strings. This pattern is all over the in scope contracts.
As evident from the comment on line 107, the custom errors do help reduce contract size and save on gas so should be used in the entire codebase as long as the compiler version is >= 0.8.4

## Use of functions that prevents overflows/underflows not necessary
The file [ERC721Checkpointable.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L253) defines functions such as [safe96](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L273)  which is meant to check and prevent any underflows when doing subtractions. This checks are not neccessay as solidity compiler version 0.8 comes with default checks for this behaviors .

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L273-L280
```solidity
    function sub96(
        uint96 a,
        uint96 b,
        string memory errorMessage
    ) internal pure returns (uint96) {
        require(b <= a, errorMessage);
        return a - b;
    }
```

Usage here: File: ERC721Checkpointable.sol [Line 219 ](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L219)
```solidity
                uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');
```

**other instances**
```solidity
File: 
253:     function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {

256:     function safe96(uint256 n, string memory errorMessage) internal pure returns (uint96) {

263:     function add96(
```

Unless this is being utilized for the sake of getting the friendly error messages then it should be removed.
## Typos

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L104
```solidity
File: NounsDAOLogicV1.sol
104:      * @notice Used to initialize the contract during delegator contructor //@audit: contructor

190:      * @notice Internal function that caries out voting logic //@audit: caries
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L115
```solidity
File: NounsDAOLogicV2.sol
115:      * @notice Used to initialize the contract during delegator contructor //@audit: contructor

582:      * @notice Internal function that caries out voting logic //@audit: caries
```

### Numeric values having to do with time should use time units for readability
There are units for seconds, minutes, hours, days, and weeks, and since they're defined, they should be used

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L68
```solidity
File: /contracts/governance/NounsDAOLogicV2.sol
68:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

71:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

77:     uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L76
```solidity
File: /contracts/governance/NounsDAOLogicV1.sol
76:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

79:    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

85:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
```

### Use uppercase for constants
Constants should be named with all capital letters with underscores separating words. The practice has been efffected on most constants apart from the following.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L67
```solidity
94:     uint256 public constant proposalMaxOperations = 10; // 10 actions
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L59
```solidity
92:     uint256 public constant proposalMaxOperations = 10; // 10 actions
```

### Natspec is incomplete
File: NounsDAOLogicV2.sol [Line 873-877](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L873-L877)
Missing @param proposalId
```solidity
    /**
     * @notice Quorum votes required for a specific proposal to succeed
     * Differs from `GovernerBravo` which uses fixed amount
     */
    function quorumVotes(uint256 proposalId) public view returns (uint256) {
```

File: NounsDAOLogicV2.sol [Line 836-839](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L836-L839)
Missing @param newVetoer
```solidity
     * @notice Changes vetoer address
     * @dev Vetoer function for updating vetoer address
     */
    function _setVetoer(address newVetoer) public {
```

File: NounsDAOLogicV1.sol [Line 468-478](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L468-L478)
Missing @param proposalId,support,v,r,s
```solidity
    /**
     * @notice Cast a vote for a proposal by signature
     * @dev External function that accepts EIP-712 signatures for voting on proposals.
     */
    function castVoteBySig(
        uint256 proposalId,
        uint8 support,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external {
```

### public functions not called by the contract should be declared external instead
Contracts are allowed to override their parents' functions and change the visibility from external to public.
File: ERC721Checkpointable.sol [Line 126-144](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L126-L144)
```solidity
    function delegateBySig(
        address delegatee,
        uint256 nonce,
        uint256 expiry,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) public {
```

File: ERC721Checkpointable.sol [Line 163-195](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L163-L195)
```solidity
    function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {
```

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L184-L279
```solidity
File: NounsDAOLogicV2.sol
184:     function propose(
185:        address[] memory targets,
186:        uint256[] memory values,
187:        string[] memory signatures,
188:        bytes[] memory calldatas,
189:        string memory description
190:    ) public returns (uint256) {

851:     function _burnVetoPower() public {

862:     function proposalThreshold() public view returns (uint256) { 

1002:    function maxQuorumVotes() public view returns (uint256) {

```

File: NounsDAOLogicV1.sol [Line 649-654](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L649-L654)
```solidity
    function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');


        _setVetoer(address(0));
    }
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L61
```solidity
File: /contracts/base/ERC721Enumerable.sol
61:     function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {

76:     function tokenByIndex(uint256 index) public view virtual override returns (uint256) {
```

### Use of ecrecover is susceptible to signature malleability and replay attacks
The function `castVoteBySig` calls the solidity `ecrecover` function directly. However the `ecrecover` EVM opcode allows for malleable signatures(because of non unique s and v values) which could lead to  replay attacks. In this instances there is no **nonce** being used to track the signatures .

In some cases ecrecover can return a random address instead of 0 for an invalid signature. The  code  below only checks for address 0
Signature are malleable, meaning you might be able to create a second also valid signature for the same data. 

File: NounsDAOLogicV1.sol [Line 472-487](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L472-L487)
```solidity
    function castVoteBySig(
        uint256 proposalId,
        uint8 support,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external {
        bytes32 domainSeparator = keccak256(
            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
        );
        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
        address signatory = ecrecover(digest, v, r, s);
        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
        emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
    }
```

File: NounsDAOLogicV2.sol [Line 564-579](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L564-L579)
```solidity
    function castVoteBySig(
        uint256 proposalId,
        uint8 support,
        uint8 v,
        bytes32 r,
        bytes32 s
    ) external {
        bytes32 domainSeparator = keccak256(
            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
        );
        bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
        bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
        address signatory = ecrecover(digest, v, r, s);
        require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
        emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
    }
```

Consider adding nonces and also consider using [OpenZeppelin’s ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/cryptography/ECDSA.sol)  (which prevents malleability) instead of the built-in function:

See an already existing implementation of nonce in the following code

see [Line 139](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L126-L144) for an already well implemented check

```solidity
File: /contracts/base/ERC721Checkpointable.sol
139:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
```
