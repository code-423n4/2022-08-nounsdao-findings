 # Non-critical Issues

## Summary Of Findings:

  | Issue 
-- | --  
1 | Custom errors could be used with parameters for better user experience
2 | Checks for admin can be converted into a modifier
3 | Avoid hardcoding gas values

## Details on Findings:

### 1. <ins>Custom errors could be used with parameters for better user experience</ins>
Custom errors can take in parameters which can help the user identify the exact reason for the revert. The following custom errors can take in a parameter to make the protocol more user-friendly.

File: [NounsDAOLogicV2.sol](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol)
```solidity
    error InvalidMinQuorumVotesBPS();
    error InvalidMaxQuorumVotesBPS();
    error MinQuorumBPSGreaterThanMaxQuorumBPS();
    error UnsafeUint16Cast();
```

### 2. <ins>Checks for admin can be converted into a modifier</ins>
The contracts, `NounsDAOLogicV1` and `NounsDAOLogicV2` have several functions where there is a need to check if the `msg.sender` is `admin`. The method used for this is very inconsistent within these contracts. For increasing readability and even for saving gas, its recommended to create a modifier for this with a custom error(instead of a revert string).

Instances below:
 1. [NounsDAOLogicV1](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol)
```solidity
Line 123:      require(msg.sender == admin, 'NounsDAO::initialize: admin only');
Line 530:      require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
Line 546:      require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
Line 563:      require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
Line 581:      require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');
Line 599:      require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');     
```
 2. [NounsDAOLogicV2](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol)
```solidity
Line 134:      require(msg.sender == admin, 'NounsDAO::initialize: admin only');
Line 622:      require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
Line 638:      require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');
Line 655:      require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
Line 674:      require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');
Line 702:      require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');    
Line 727:      require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');
Line 753:      if (msg.sender != admin) {
                    revert AdminOnly();
                }
Line 784:      if (msg.sender != admin) {
                    revert AdminOnly();
                }
Line 801:      require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');     
```

An example mitigation would look like this:
```solidity
// declare a modifier with "require string"
    modifier onlyAdmin {
        require(msg.sender == admin, "only admin");
        _;
    }
// OR you can declare a modifier with a "custom error"
    error AdminOnly();  //declare a custom error
    modifier onlyAdmin {
        if(msg.sender != admin)
            revert AdminOnly();
        _;
    }
// use it in a function
    function _withdraw() external onlyAdmin {
        // statements
    }
```

### 3. <ins>Avoid hardcoding gas values</ins>
In `NounsDAOLogicV2` contract the `_refundGas` method uses calculations based on hardcoded gas values. This is not recommended. The gas cost of EVM instructions may change significantly during hard forks which will result in, the users who need to get a refund, gets less than what they are supposed to get. See [SWC-134](https://swcregistry.io/docs/SWC-134#hardcoded-gas-limitssol) for more information.

Concerned declarations are of [MAX_REFUND_PRIORITY_FEE and REFUND_BASE_GAS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L94-L98)
```solidity
    /// @notice The maximum priority fee used to cap gas refunds in `castRefundableVote`
    uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

    /// @notice The vote refund gas overhead, including 7K for ETH transfer and 29K for general transaction overhead
    uint256 public constant REFUND_BASE_GAS = 36000;
```
Mitigation would be to declare them as state variables and make them settable. 

 # Non-critical Issues

## Summary Of Findings:

  | Issue 
-- | --  
1 | Unused variables should be indicated as such
2 | Spelling mistakes in Natspec comments
3 | Non-library/interface files should use fixed compiler versions, not floating ones
4 | Use a newer version of Solidity

## Details on Findings:

### 1. <ins>Unused variables should be indicated as such</ins>

In `NounsDAOLogicV2`, the variable, [MAX_QUORUM_VOTES_BPS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L89) is declared but not used. Either it should be removed or it should be mentioned in the comment to avoid confusion.
```solidity
    /// @notice The maximum setable quorum votes basis points
    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%
```

### 2. <ins>Spelling mistakes in Natspec comments</ins>
The following spelling mistakes were noticed:
 
 1. [NounsDAOLogicV2](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol)
```solidity
Line 061:      setable 
Line 064:      setable 
Line 067:      setable 
Line 070:      setable 
Line 073:      setable 
Line 076:      setable 
Line 088:      setable 
Line 115:      contructor
Line 582:      caries
Line 848:      priviledges
``` 
 4. [NounsDAOLogicV1](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol)
```solidity
Line 069:      setable 
Line 072:      setable 
Line 075:      setable 
Line 078:      setable 
Line 081:      setable 
Line 084:      setable 
Line 087:      setable 
Line 090:      setable
Line 104:      contructor
Line 490:      caries
Line 646:      priviledges
``` 
 5. [NounsDAOProxy](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol)
```solidity
Line 072:      canceled
Line 197:      favor
Line 203:      canceled
Line 297:      favor
Line 303:      canceled
Line 383:      favor
Line 389:      canceled
```

### 3. <ins>Non-library/interface files should use fixed compiler versions, not floating ones</ins>

Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

All the files under scope uses floating pragma like the one below:
```solidity
pragma solidity ^0.8.6;
```

Mitigation would be to change it to this:
```solidity
pragma solidity 0.8.6;
```

### 4. <ins>Use a newer version of Solidity</ins>
The codebase uses Solidity version 0.8.6 which was released in June 2021. Though its not possible to keep up with the version changes of Solidity, its advisable to use a relatively newer version. The current Solidity version is 0.8.16 which was released in August 2022 (almost one year later than the current version used by the codebase).

By using an older version you might be missing the following features:

- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value
- Use a solidity version of at least 0.8.16 to have more efficient code for checked addition and subtraction.

See [this](https://github.com/ethereum/solidity/releases) for a more detailed list of features and bug fixes in each solidity version. 

