# QA Report

## Summary

|               | Issue         | Instances     |
| :-------------: |:-------------|:-------------:|
| 1      | Missing checks for **address(0)** when assigning values to address state variables |  5 |
| 2      | Non-library/interface files should use fixed compiler versions, not floating ones     |  5  |
| 3      | Duplicated **require()** checks should be refactored to a modifier  |  17 |
| 4      | Wrong check in **_acceptAdmin()** function |  2 |
| 5      | 2**n should be re-written as **type(uint(n)).max**  |  2 |
| 6      | Related data should be grouped in a struct |  2 |


## Findings

### 1- Missing checks for address(0) when assigning values to address state variables :

Setters should check the input address and revert if it's the zero address

#### Impact - Low Risk

#### Proof of Concept
Instances include:

File: contracts/governance/NounsDAOProxy.sol

[admin = admin_;](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L71)

File: contracts/governance/NounsDAOLogicV1.sol

[function _setPendingAdmin(address newPendingAdmin) external](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L597)

[function _setVetoer(address newVetoer) public](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L637)

File: contracts/governance/NounsDAOLogicV2.sol

[function _setPendingAdmin(address newPendingAdmin) external](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799)

[function _setVetoer(address newVetoer) public](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839)

#### Mitigation
Add non-zero address checks to the functions aforementioned.


### 2- Non-library/interface files should use fixed compiler versions, not floating ones :

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

check this source : https://swcregistry.io/docs/SWC-103

#### Impact - Low Risk
 
#### Proof of Concept
All contracts use a floating solidity version 

```
File: contracts/governance/NounsDAOProxy.sol

pragma solidity ^0.8.6;

File: contracts/governance/NounsDAOLogicV1.sol

pragma solidity ^0.8.6;

File: contracts/governance/NounsDAOLogicV2.sol

pragma solidity ^0.8.6;

File: contracts/base/ERC721Checkpointable.sol

pragma solidity ^0.8.6;

File: contracts/base/ERC721Enumerable.sol

pragma solidity ^0.8.0;
```

#### Mitigation
Lock the pragma version to the same version as used in the other contracts and also consider known bugs (https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

Pragma statements can be allowed to float when a contract is intended for consumption by other developers, as in the case with contracts in a library or EthPM package. Otherwise, the developer would need to manually update the pragma in order to compile it locally.

### 3- Duplicated require() checks should be refactored to a modifier :

#### Impact - NON CRITICAL

#### Proof of Concept

There are 17 instances of this issue:

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

#### Mitigation
Those checks should be replaced by an **onlyOwner** modifier as follow :

```
modifier onlyAdmin(){
    if (msg.sender != admin) {
            revert AdminOnly();
        }
}
```

### 4- Unnecessary check in _acceptAdmin() function :

When calling the  _acceptAdmin() function their is a check for  msg.sender != address(0) which doesn't make sense as msg.sender can't be equal to the ZERO address, as it's either a contract or EOA address .


#### Impact - NON CRITICAL

#### Proof of Concept

There are 2 instances of this issue:

File: contracts/governance/NounsDAOLogicV1.sol

[require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)

File: contracts/governance/NounsDAOLogicV2.sol

[require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)


#### Mitigation

Remove the msg.sender != address(0) check from the instances mentioned before.

### 5- 2**n should be re-written as type(uint(n)).max :

#### Impact - NON CRITICAL

#### Proof of Concept

There are 2 instances of this issue:

File: contracts/base/ERC721Checkpointable.sol

[require(n < 2**32, errorMessage);](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L254)

[require(n < 2**96, errorMessage);](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L259)

#### Mitigation

Use type(uint(n)).max to replace 2**n in the instances mentioned before.

### 6- Related data should be grouped in a struct :

When there are mappings that use the same key value, having separate fields is error prone, for instance in case of deletion or with future new fields.

#### Impact - NON CRITICAL

#### Proof of Concept

Instances include:

```
File: contracts/base/ERC721Checkpointable.sol

53      mapping(address => mapping(uint32 => Checkpoint)) public checkpoints;
56      mapping(address => uint32) public numCheckpoints;
```

#### Mitigation

Group the related data in a struct and use one mapping:

```
struct UserCheckpoints {
        mapping(uint32 => Checkpoint) checkpoints;
        uint32 numCheckpoints;
    }
```

And it would be used as a state variable :

```
mapping(address => UserCheckpoints) public usersCheckpoints;
```