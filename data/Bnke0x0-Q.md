# [L-01] Unsafe calls to optional ERC20 functions (`decimals()`, `name()` and `symbol()`
 are optional parts of the ERC20 specification, so there are tokens that
 do not implement them. It’s not safe to cast arbitrary token addresses 
in order to call these functions. If `IERC20Metadata` is to be relied on, that should be the variable type of the token variable, rather than it being `address`, so the compiler can verify that types correctly match, rather than this being a runtime failure. See [this](https://github.com/code-423n4/2021-05-yield-findings/issues/32) prior instance of this issue which was marked as Low risk. Do this to resolve [the](https://github.com/boringcrypto/BoringSolidity/blob/c73ed73afa9273fbce93095ef177513191782254/contracts/libraries/BoringERC20.sol#L49-L55) issue.):



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 135-136):

    ` abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
        );`





# [L-02] Missing checks for `address(0x0)` when assigning values to `address` state variables:



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 150-154):

    `        vetoer = vetoer_;
        votingPeriod = votingPeriod_;
        votingDelay = votingDelay_;
        proposalThresholdBPS = proposalThresholdBPS_;
        quorumVotesBPS = quorumVotesBPS_;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 642):

    `vetoer = newVetoer;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol(line 156-159):

    `        vetoer = vetoer_;
        votingPeriod = votingPeriod_;
        votingDelay = votingDelay_;
        proposalThresholdBPS = proposalThresholdBPS_;` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 71):

    `admin = admin_;`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 201):

    `_delegates[delegator] = delegatee;`





# [L-03] `initialize` functions can be front-run (See [this](https://github.com/code-423n4/2021-10-badgerdao-findings/issues/40) finding from a prior badger-dao contest for details):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 113):

    `function initialize(`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 124):

    `function initialize(






# [L-04] Nonce used for multiple purposes (The nonce mapping used for `permit()` calls is the same as the one used for `delegateBySig()`.
 This should at the very least be documented so signers know that the 
order of operations between the two functions matters, and so that m`ulticall()`s can be organized appropriately):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 141):

    `require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');`





# [L-05] Unused `receive()` function will lock Ether in contract (If the intention is for the Ether to be used, the function should call another function, otherwise it should revert):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1030):

    `receive() external payable {}`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 138-140):

    ` receive() external payable {
        _fallback();
    }`








# [L-06] Discontinuity in Exercise Period (The position can be exercised if current block timestamp is less than the position’s expiration.

The position can be withdrawed if current block timestamp is greater than the position’s expiration

However, when current block timestamp is equal to the position’s expiration (`block.timestamp == positionExpirations`), the state is unknown (cannot be exercised or withdraw)):



1. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 142):

    `require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 438):

    `} else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol(line 449):

    `} else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {` 







##Non-Critical Issues







# [N-01] Adding a `return` statement when the function defines a named return variable, is redundant:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 522):

    `return votes;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 681):

    `return chainId;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol(line 267):

    `return newProposal.id;` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 278):

    `return newProposal.id;`       

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 614):

    `return votes;`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1027):

    `return chainId;`

7. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 287):

    `return chainId;`        










# [N-02] `require()`/`revert()` statements should have descriptive reason strings:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 98):

    `revert(add(returnData, 0x20), returndatasize())`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 118):

    `revert(free_mem_ptr, returndatasize())`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1019):

    `require(n <= type(uint32).max, errorMessage);` 

4. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 254):

    `require(n < 2**32, errorMessage);`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 259):

    `require(n < 2**96, errorMessage);`

6. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 269):

    `require(c >= a, errorMessage);`

7. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 278):

    `require(b <= a, errorMessage);`    










# [N-03] constants should be defined rather than using magic numbers:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 98):

    `revert(add(returnData, 0x20), returndatasize())`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 673):

    `return (number * bps) / 10000;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 908-909):

    `        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
        uint256 quorumAdjustmentBPS = (params.quorumCoefficient * againstVotesBPS) / 1e6;` 

4. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 254):

    `require(n < 2**32, errorMessage);`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 259):

    `require(n < 2**96, errorMessage);`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 851):

    `uint256 center = upper - (upper - lower) / 2;`

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1007):

    ` return (number * bps) / 10000;`

8. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 98):

    `revert(add(returnData, 0x20), returndatasize())`       

9. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 113):

    `let free_mem_ptr := mload(0x40)`

10. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol(line 184):

    `uint32 center = upper - (upper - lower) / 2; `





# [N-04] Use a more recent version of solidity (Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 61):

    `pragma solidity ^0.8.6;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 53):

    `pragma solidity ^0.8.6;`

3. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 35):

    `pragma solidity ^0.8.6;` 








# [N-05] Use a more recent version of solidity (Use a solidity version of at least 0.8.12 to get `string.concat()` to be used instead of `abi.encodePacked(<str>,<str>)`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 37-62):

    `    event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );

    /// @notice An event emitted when a new proposal is created, which includes additional information
    event ProposalCreatedWithRequirements(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        uint256 proposalThreshold,
        uint256 quorumVotes,
        string description
    );`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 70):

    `event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 76):

    `event ProposalQueued(uint256 id, uint256 eta);` 

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 85-106):

    `    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);

    /// @notice An event emitted when the voting period is set
    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);

    /// @notice Emitted when implementation is changed
    event NewImplementation(address oldImplementation, address newImplementation);

    /// @notice Emitted when proposal threshold basis points is set
    event ProposalThresholdBPSSet(uint256 oldProposalThresholdBPS, uint256 newProposalThresholdBPS);

    /// @notice Emitted when quorum votes basis points is set
    event QuorumVotesBPSSet(uint256 oldQuorumVotesBPS, uint256 newQuorumVotesBPS);

    /// @notice Emitted when pendingAdmin is changed
    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);

    /// @notice Emitted when pendingAdmin is accepted, which means admin is updated
    event NewAdmin(address oldAdmin, address newAdmin);

    /// @notice Emitted when vetoer is changed
    event NewVetoer(address oldVetoer, address newVetoer)`       

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 111-123):

    `    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);

    /// @notice Emitted when maxQuorumVotesBPS is set
    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);

    /// @notice Emitted when quorumCoefficient is set
    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);

    /// @notice Emitted when a voter cast a vote requesting a gas refund.
    event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);

    /// @notice Emitted when admin withdraws the DAO's balance.
    event Withdraw(uint256 amount, bool sent);`

6. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 73):

    `event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);`

   


# [N-06] Unused file:



1. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 1):

   `// SPDX-License-Identifier: MIT`





# [N-07] Use of sensitive/non-inclusive terms:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 204-208):

   `        bool canceled;
       /// @notice Flag marking whether the proposal has been vetoed
       bool vetoed;
       /// @notice Flag marking whether the proposal has been executed
       bool executed;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 216):

   `bool hasVoted;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 304-308):

   `        bool canceled;
       /// @notice Flag marking whether the proposal has been vetoed
       bool vetoed;
       /// @notice Flag marking whether the proposal has been executed
       bool executed;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 320):

   `bool hasVoted;`

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 390-394):

   `        bool canceled;
       /// @notice Flag marking whether the proposal has been vetoed
       bool vetoed;
       /// @notice Flag marking whether the proposal has been executed
       bool executed;`






# [N-08] `public` functions not called by the contract should be declared `external` instead (Contracts are allowed to override their parents’ functions and change the visibility from `public` to `external`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 649):

   `function _burnVetoPower() public {`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 660):

   `function proposalThreshold() public view returns (uint256) {`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 668):

   ` function quorumVotes() public view returns (uint256) {`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 851):

   `function _burnVetoPower() public {`

5. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 862):

   `function proposalThreshold() public view returns (uint256) {`

6. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 995):

   ` function minQuorumVotes() public view returns (uint256) {` 

7. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1002):

   `function maxQuorumVotes() public view returns (uint256) {` 







# [N-09] Numeric values having to do with time should use time units for readability (There are [units](https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units) for seconds, minutes, hours, days, and weeks):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 76-85):

   ` uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 1;

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 68-77):

   ` uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

    /// @notice The max setable voting period
    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

    /// @notice The min setable voting delay
    uint256 public constant MIN_VOTING_DELAY = 1;

    /// @notice The max setable voting delay
    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week`








# [N-10] Constant redefined elsewhere (Consider defining in only one contract so that values cannot become out of sync when only one location is updated):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 97-101):

   `   bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 101-105):

   `    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the ballot struct used by the contract
    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');`

3. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 59-64):

   `    bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

    /// @notice The EIP-712 typehash for the delegation struct used by the contract
    bytes32 public constant DELEGATION_TYPEHASH =
        keccak256('Delegation(address delegatee,uint256 nonce,uint256 expiry)');`











# [N-11] Interfaces should be moved to separate files (Most of the other interfaces in this project are in their own file in
 the interfaces directory. The interfaces below do not follow this 
pattern):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 402):

   `interface INounsDAOExecutor {`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 436):

   `interface NounsTokenLike {`


 





# [N-12] Non-library/interface files should use fixed compiler versions, not floating ones:



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 61):

   `pragma solidity ^0.8.6;`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 53):

   `pragma solidity ^0.8.6;`

3. File: 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol (line 33):

   `pragma solidity ^0.8.6;`

4. File: 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol (line 36):

   `pragma solidity ^0.8.6;`       

5. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 35):

   `pragma solidity ^0.8.6;`

6. File: 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol (line 28):

   `pragma solidity ^0.8.0;`                   




# [N-13] Non-assembly method available (`assembly{ id := chainid() }` => `uint256 id = block.chainid`, `assembly { size := extcodesize() }` => `uint256 size = address().code.length`):



1. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol (line 679):

    `chainId := chainid()`

2. File: 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol (line 1013):

    `chainId := chainid()`

3. File: 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol (line 285):

    `chainId := chainid()` 



