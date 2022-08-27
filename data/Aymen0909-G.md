# Gas Optimizations

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Multiple address mappings can be combined into a single mapping of an address to a struct     | 2      |
| 2      | Variables inside struct should be packed to save gas     | 2    |
| 3      | **array.length** should not be looked up in every loop in a for loop |  8 |
| 4      | Use **calldata** instead of **memory** for function parameters type  |  2 |
| 5      | Duplicated **require()** checks should be refactored to a modifier for saving deployment costs     |  17  |
| 6      | Use custom errors rather than **require()**/**revert()** strings to save deployments gas  |  89 |
| 7      | Splitting **require()** statements that uses **&&** saves gas  |  22 |
| 8      | **require()**/**revert()** strings longer than 32 bytes cost extra gas     |  89  |
| 9      | It costs more gas to initialize variables to zero than to let the default of zero be applied    |  11  |
| 10      | Use of **++i** cost less gas than **i++** in for loops    |  8  |
| 11      | **++i/i++** should be **unchecked{++i}/unchecked{i++}** when it is not possible for them to overflow, as in the case when used in for & while loops |  8  |
| 12      | Using **private** rather than **public** for constants, saves gas |  28 |
| 13      | Functions guaranteed to revert when called by normal users can be marked **payable** |  20 |


## Findings

### 1- Multiple address mappings can be combined into a single mapping of an address to a struct :

Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to not having to recalculate the key's keccak256 hash (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

There are 2 instances of this issue :

```
File: contracts/base/ERC721Checkpointable.sol

53      mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
56      mapping(address => uint32) public numCheckpoints;
```

These mappings could be refactored into the following struct and mapping for example :

```
struct UserCheckpoints {
        mapping(uint32 => Checkpoint) checkpoints;
        uint32 numCheckpoints;
    }
    
mapping(address => UserCheckpoints) public usersCheckpoints;
```

### 2- Variables inside struct should be packed to save gas :

As the solidity EVM works with 32 bytes, variables less than 32 bytes should be packed inside a struct so that they can be stored in the same slot, this saves gas when writing to storage

There are 2 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

157     struct ProposalTemp {
            uint256 totalSupply;
            uint256 proposalThreshold;
            uint256 latestProposalId;
            uint256 startBlock;
            uint256 endBlock;
        }

File: contracts/governance/NounsDAOLogicV2.sol

167     struct ProposalTemp {
            uint256 totalSupply;
            uint256 proposalThreshold;
            uint256 latestProposalId;
            uint256 startBlock;
            uint256 endBlock;
        }
```

Because none of the variable inside the ProposalTemp struct can overflow 2**64, it should be rearranged as follow :

```
struct ProposalTemp {
        uint64 totalSupply;
        uint64 proposalThreshold;
        uint64 latestProposalId;
        uint64 startBlock;
        uint64 endBlock;
    }
```

### 3- **array.length** should not be looked up in every loop in a for loop :

There are 8 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

281     for (uint256 i = 0; i < proposal.targets.length; i++)
319     for (uint256 i = 0; i < proposal.targets.length; i++)
346     for (uint256 i = 0; i < proposal.targets.length; i++)
371     for (uint256 i = 0; i < proposal.targets.length; i++)

File: contracts/governance/NounsDAOLogicV2.sol

292     for (uint256 i = 0; i < proposal.targets.length; i++)
330     for (uint256 i = 0; i < proposal.targets.length; i++)
357     for (uint256 i = 0; i < proposal.targets.length; i++)
382     for (uint256 i = 0; i < proposal.targets.length; i++)
```

### 4- Use **calldata** instead of **memory** for function parameters type :

If a reference type function parameter is read-only, it is cheaper in gas to use calldata instead of memory. Calldata is a non-modifiable, non-persistent area where function arguments are stored, and behaves mostly like memory.

There are 2 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

174     function propose(
            address[] memory targets,
            uint256[] memory values,
            string[] memory signatures,
            bytes[] memory calldatas,
            string memory description
        ) public returns (uint256)

File: contracts/governance/NounsDAOLogicV2.sol

184     function propose(
            address[] memory targets,
            uint256[] memory values,
            string[] memory signatures,
            bytes[] memory calldatas,
            string memory description
        ) public returns (uint256)
```

### 5- Duplicated require() checks should be refactored to a modifier for saving deployment costs :

require() checks repeated in multiple functions should be refactored into a modifier to save deployment gas.

There are 17 instances of this issue :

```
File: contracts/governance/NounsDAOLogicV1.sol

123     require(msg.sender == admin, 'NounsDAO::initialize: admin only');
530     require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
546     require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
563     require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
581     require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
599     require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
617     require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
599     require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

File: contracts/governance/NounsDAOLogicV2.sol

134     require(msg.sender == admin, 'NounsDAO::initialize: admin only');
622     require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
638     require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
655     require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
674     require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
702     require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');
727     require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
801     require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');
819     require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
Those checks should be replaced by an **onlyOwner** modifier as follow :

```
modifier onlyAdmin(){
    if (msg.sender != admin) {
            revert AdminOnly();
        }
}
```

### 6- Use custom errors rather than **require()**/**revert()** strings to save deployments gas :

Custom errors from Solidity 0.8.4 are cheaper than revert strings (cheaper deployment cost and runtime cost when the revert condition is met) while providing the same amount of information.

There are 89 instances of this issue :

File: contracts/base/ERC721Checkpointable.sol => 4 instances  
File: contracts/base/ERC721Enumerable.sol => 2 instances  
File: contracts/governance/NounsDAOLogicV1.sol => 37 instances  
File: contracts/governance/NounsDAOLogicV2.sol => 44 instances  
File: contracts/governance/NounsDAOProxy.sol => 2 instances

### 7- Splitting **require()** statements that uses && saves gas :

There are 22 instances of this issue :

```
File: contracts/governance/NounsDAOLogicV1.sol

126     require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
130     require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
134     require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
138     require(
            quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::initialize: invalid proposal threshold'
        );
191     require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
191     require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
531     require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
547     require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
564     require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
582     require(
            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
        );
617     require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

File: contracts/governance/NounsDAOLogicV2.sol

137     require(
            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
            'NounsDAO::initialize: invalid voting period'
        );
141      require(
            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
            'NounsDAO::initialize: invalid voting delay'
        );
145     require(
            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::initialize: invalid proposal threshold bps'
        );
201     require(
            targets.length == values.length &&
                targets.length == signatures.length &&
                targets.length == calldatas.length,
            'NounsDAO::propose: proposal function information arity mismatch'
        );
623     require(
            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
            'NounsDAO::_setVotingDelay: invalid voting delay'
        );
639     require(
            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
            'NounsDAO::_setVotingPeriod: invalid voting period'
        );
656     require(
            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
        );
677     require(
            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
        );
705     require(
            newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
            'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
        );
709     require(
            params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
            'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
        );
819     require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```

### 8- **require()**/**revert()** strings longer than 32 bytes cost extra gas :

Each extra memory word of bytes past the original 32 [incurs an MSTORE](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#consider-having-short-revert-strings) which costs **3 gas**.

There are 89 instances of this issue :

File: contracts/base/ERC721Checkpointable.sol => 4 instances  
File: contracts/base/ERC721Enumerable.sol => 2 instances  
File: contracts/governance/NounsDAOLogicV1.sol => 37 instances  
File: contracts/governance/NounsDAOLogicV2.sol => 44 instances  
File: contracts/governance/NounsDAOProxy.sol => 2 instances


### 9- It costs more gas to initialize variables to zero than to let the default of zero be applied :

If a variable is not set/initialized, it is assumed to have the default value (0 for uint or int, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

There are 11 instances of this issue:

```
File: contracts/base/ERC721Checkpointable.sol

41      uint8 public constant decimals = 0;
181     uint32 lower = 0;

File: contracts/governance/NounsDAOLogicV1.sol

281     for (uint256 i = 0; i < proposal.targets.length; i++)
319     for (uint256 i = 0; i < proposal.targets.length; i++)
346     for (uint256 i = 0; i < proposal.targets.length; i++)
371     for (uint256 i = 0; i < proposal.targets.length; i++)

File: contracts/governance/NounsDAOLogicV2.sol

292     for (uint256 i = 0; i < proposal.targets.length; i++)
330     for (uint256 i = 0; i < proposal.targets.length; i++)
357     for (uint256 i = 0; i < proposal.targets.length; i++)
382     for (uint256 i = 0; i < proposal.targets.length; i++)
948     uint256 lower = 0;
```

### 10- Use of ++i cost less gas than i++/i=i+1 in for loops :

There are 8 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

281     for (uint256 i = 0; i < proposal.targets.length; i++)
319     for (uint256 i = 0; i < proposal.targets.length; i++)
346     for (uint256 i = 0; i < proposal.targets.length; i++)
371     for (uint256 i = 0; i < proposal.targets.length; i++)

File: contracts/governance/NounsDAOLogicV2.sol

292     for (uint256 i = 0; i < proposal.targets.length; i++)
330     for (uint256 i = 0; i < proposal.targets.length; i++)
357     for (uint256 i = 0; i < proposal.targets.length; i++)
382     for (uint256 i = 0; i < proposal.targets.length; i++)
```

### 11- ++i/i++ should be unchecked{++i}/unchecked{i++} when it is not possible for them to overflow, as in the case when used in for & while loops :

There are 8 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

281     for (uint256 i = 0; i < proposal.targets.length; i++)
319     for (uint256 i = 0; i < proposal.targets.length; i++)
346     for (uint256 i = 0; i < proposal.targets.length; i++)
371     for (uint256 i = 0; i < proposal.targets.length; i++)

File: contracts/governance/NounsDAOLogicV2.sol

292     for (uint256 i = 0; i < proposal.targets.length; i++)
330     for (uint256 i = 0; i < proposal.targets.length; i++)
357     for (uint256 i = 0; i < proposal.targets.length; i++)
382     for (uint256 i = 0; i < proposal.targets.length; i++)
```


### 12- Using private rather than public for constants, saves gas :

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table.

There are 28 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

67      string public constant name = 'Nouns DAO';
70      uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;
73      uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;
76      uint256 public constant MIN_VOTING_PERIOD = 5_760;
79      uint256 public constant MAX_VOTING_PERIOD = 80_640;
82      uint256 public constant MIN_VOTING_DELAY = 1;
85      uint256 public constant MAX_VOTING_DELAY = 40_320;
88      uint256 public constant MIN_QUORUM_VOTES_BPS = 200;
91      uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;
94      uint256 public constant proposalMaxOperations = 10;
97      bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address  verifyingContract)');
101     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

File: contracts/governance/NounsDAOLogicV2.sol

59      string public constant name = 'Nouns DAO';
62      uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;
65      uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;
68      uint256 public constant MIN_VOTING_PERIOD = 5_760;
71      uint256 public constant MAX_VOTING_PERIOD = 80_640;
74      uint256 public constant MIN_VOTING_DELAY = 1;
77      uint256 public constant MAX_VOTING_DELAY = 40_320;
80      uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200;
83      uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000;
86      uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000;
89      uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;
92      uint256 public constant proposalMaxOperations = 10;
95      uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;
98      uint256 public constant REFUND_BASE_GAS = 36000;
101     bytes32 public constant DOMAIN_TYPEHASH =
        keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');
105     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

```

### 13- Functions guaranteed to revert when called by normal users can be marked **payable** :

If a function modifier such as **onlyAdmin** is used, the function will revert if a normal user tries to pay the function. Marking the function as payable will lower the gas cost for the owner because the compiler will not include checks for whether a payment was provided. The extra opcodes avoided are : 

CALLVALUE(gas=2), DUP1(gas=3), ISZERO(gas=3), PUSH2(gas=3), JUMPI(gas=10), PUSH1(gas=3), DUP1(gas=3), REVERT(gas=0), JUMPDEST(gas=1), POP(gas=2). 
Which costs an average of about 21 gas per call to the function, in addition to the extra deployment cost

There are 20 instances of this issue:

```
File: contracts/governance/NounsDAOLogicV1.sol

529     function _setVotingDelay(uint256 newVotingDelay) external
545     function _setVotingPeriod(uint256 newVotingPeriod) external
562     function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external
580     function _setQuorumVotesBPS(uint256 newQuorumVotesBPS) external
597     function _setPendingAdmin(address newPendingAdmin) external
615     function _acceptAdmin() external
637     function _setVetoer(address newVetoer) public
649     function _burnVetoPower() public

File: contracts/governance/NounsDAOLogicV2.sol

621     function _setVotingDelay(uint256 newVotingDelay) external
637     function _setVotingPeriod(uint256 newVotingPeriod) external
654     function _setProposalThresholdBPS(uint256 newProposalThresholdBPS) external
673     function _setMinQuorumVotesBPS(uint16 newMinQuorumVotesBPS) external
701     function _setMaxQuorumVotesBPS(uint16 newMaxQuorumVotesBPS) external 
726     function _setQuorumCoefficient(uint32 newQuorumCoefficient) external
748     function _setDynamicQuorumParams(
            uint16 newMinQuorumVotesBPS,
            uint16 newMaxQuorumVotesBPS,
            uint32 newQuorumCoefficient
        ) public
783     function _withdraw() external
799     function _setPendingAdmin(address newPendingAdmin) external
817     function _acceptAdmin() external
839     function _setVetoer(address newVetoer) public
851     function _burnVetoPower() public
```