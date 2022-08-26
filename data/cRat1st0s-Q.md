# 2022-08-nounsdao-code4rena QA Report

- [2022-08-nounsdao-code4rena QA Report](#2022-08-nounsdao-code4rena-qa-report)
  - [Files Description Table](#files-description-table)
  - [QA Report](#qa-report)
  - [Issues found](#issues-found)
    - [[N-01]: NounsDAOLogicV2 can **NOT** be compiled because of `block.basefee`](#n-01-nounsdaologicv2-can-not-be-compiled-because-of-blockbasefee)
      - [Impact](#impact)
      - [Code Affected](#code-affected)
      - [Mitigation](#mitigation)
    - [[N-02]: Typos](#n-02-typos)
      - [Impact](#impact-1)
      - [Code Affected and Mitigation](#code-affected-and-mitigation)
      - [Tools used](#tools-used)

##  Files Description Table

| File Name                                                    | SHA-1 Hash                               |
| ------------------------------------------------------------ | ---------------------------------------- |
| 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol    | e0e939a91c5d5c3148ae744741646e3f440d1e3d |
| 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol    | 96223a722bf49513779653adff38b72db55fce3f |
| 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol | 798ddc7b42dff8950e968af112bba2df70a9efe6 |
| 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol      | 8f1078c179bb62ed1da7eb176adea138571e9b6e |
| 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol     | 6cf98771a9206dda38a0900791b2d2e1f6556334 |
| 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol         | 0552a2f4170e2f3fad483ab0a90a0d9c50a92377 |

##  QA Report

## Issues found

### [N-01]: NounsDAOLogicV2 can **NOT** be compiled because of `block.basefee`

#### Impact
Member "basefee" not found or not visible after argument-dependent lookup in block.

#### Code Affected

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L980

```solidity
uint256 gasPrice = min(tx.gasprice, block.basefee + MAX_REFUND_PRIORITY_FEE);
```

#### Mitigation
Change pragma to `^0.8.7` that `block.basefee` was [introduced](https://github.com/ethereum/solidity/releases/tag/v0.8.7).

### [N-02]: Typos

#### Impact
None.

#### Code Affected and Mitigation

```diff
diff --git a/contracts/governance/NounsDAOLogicV1.sol b/contracts/governance/NounsDAOLogicV1.sol
index 5654382..4dcdd72 100644
--- a/contracts/governance/NounsDAOLogicV1.sol
+++ b/contracts/governance/NounsDAOLogicV1.sol
@@ -101,7 +101,7 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');
 
     /**
-     * @notice Used to initialize the contract during delegator contructor
+     * @notice Used to initialize the contract during delegator constructor
      * @param timelock_ The address of the NounsDAOExecutor
      * @param nouns_ The address of the NOUN tokens
      * @param vetoer_ The address allowed to unilaterally veto proposals
@@ -643,7 +643,7 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
     }
 
     /**
-     * @notice Burns veto priviledges
+     * @notice Burns veto privileges
      * @dev Vetoer function destroying veto power forever
      */
     function _burnVetoPower() public {
diff --git a/contracts/governance/NounsDAOLogicV2.sol b/contracts/governance/NounsDAOLogicV2.sol
index ccd7432..90251a2 100644
--- a/contracts/governance/NounsDAOLogicV2.sol
+++ b/contracts/governance/NounsDAOLogicV2.sol
@@ -112,7 +112,7 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
     error UnsafeUint16Cast();
 
     /**
-     * @notice Used to initialize the contract during delegator contructor
+     * @notice Used to initialize the contract during delegator constructor
      * @param timelock_ The address of the NounsDAOExecutor
      * @param nouns_ The address of the NOUN tokens
      * @param vetoer_ The address allowed to unilaterally veto proposals
@@ -845,7 +845,7 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
     }
 
     /**
-     * @notice Burns veto priviledges
+     * @notice Burns veto privileges
      * @dev Vetoer function destroying veto power forever
      */
     function _burnVetoPower() public {

```

#### Tools used
VS Code
