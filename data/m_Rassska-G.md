# Table of contents

- **[[0x0] Disclaimer](#0x0)**
- **[[G-01] Try ++i instead of i++](G-01)**
- **[[G-02] Try `unchecked{++i}` instead of `i++` in loops](G-02)**
- **[[G-04] Consider marking onlyOwner functions as payable](G-04)**
- **[[G-05] Use binary shifting instead of `a / 2^x, x > 0`](G-05)**
- **[[G-06] Cache state variables, `MLOAD` << `SLOAD`](G-06)**
- **[[G-07] Add `require()` before some computations, if it makes sense](G-07)**
- **[[G-08] Unset the variables which are longer used](G-08)**
- **[[G-10] `Internal` functions can be inlined](G-10)**
- **[[G-11] Cache the array.length into a variable](G-11)**
- **[[G-15] Use `private/internal` for `constants/immutable/state` variables instead of `public`](G-15)**
- **[[G-16] Define modifier/function to cover the same require statements](G-16)**
- **[[G-17] Use const values instead of `type(uint256).max`](G-17)**
- **[[G-19] Use `uint` instead of `uint8`, `uint64`, if it's possible](G-19)**
- **[[G-20] Mark functions as `external` instead of `public`, if there are no internal calls](G-20)**
- **[[G-21] Use `calldataload` instead of `mload`](G-21)**


## Disclaimer<a name="0x0"></a>

- Please, consider everything described below as a general recommendation. These notes will represent potential possibilities to optimize gas consumption. It's okay, if something is not suitable in your case. Just let me know the reason in the comments section. Enjoy!

## **[G-01] Try ++i instead of i++**<a name="G-01"></a>

### ***Description:***

  - In case of `i++`, the compiler has to to create a temp variable to return `i` (if there's a need) and then `i` gets incremented.  
  - In case of `++i`, the compiler just simply returns already incremented value.

### ***All occurances:***

  - Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................
      
        // Lines: [281-281]
        for (uint256 i = 0; i < proposal.targets.length; i++) {}

    ```
  
  - **3 more occurances in the current file and 4 in `NounsDaoLogicV2.sol`**

## **[G-02] Try `unchecked{++i};` instead of `i++;`/`++i;` in loops**<a name="G-02"></a>

### ***Description:***

  - Since [Solidity 0.8.0](https://github.com/ethereum/solidity/releases/tag/v0.8.0), all arithmetic operations revert on over- and underflow by default Therefore, `unchecked` box can be used to prevent all unnecessary checks, if it's no a way to get a reversion.
  
  - There are ~1e80 atoms in the universe, so 2^256 is closed to that number, therefore it's no a way to be overflowed, because of the gas limit as well. 

### ***All occurances:***

- Contracts:

    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................
      
        // Lines: [281-281]
        for (uint256 i = 0; i < proposal.targets.length; i++) {}

    ```
    
  - **3 more occurances in the current file and 4 in `NounsDaoLogicV2.sol`**


## **[G-04] Consider marking onlyOwner functions as payable**<a name="G-04"></a>

### ***Description:***

- This one is a bit questionable, but you can try that out. So, the compiler adds some extra conditions in case of non-payable, but we know that `onlyOwner` modifier will be reverted, if the user invoke following methods.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................
      
        // Lines: [748-752]
          function _setDynamicQuorumParams(
              uint16 newMinQuorumVotesBPS,
              uint16 newMaxQuorumVotesBPS,
              uint32 newQuorumCoefficient
          ) public {}
    ```

  - **1 more occurances in the current file** 

## **[G-05] Use binary shifting instead of `a / 2^x, x > 0` or `a * 2^x, x > 0`**<a name="G-05"></a>

### ***Description:***

- It's also pretty impactful one, especially in loops.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/base/ERC721Checkpointable.sol 
      ...............................
      
        // Lines: [184-184]
          uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
    ```

  - **1 more occurance in the NounsDAOLogicV2.sol file** 


## **[G-06] Cache state variables, `MLOAD` << `SLOAD`**<a name="G-06"></a>

### ***Description:***

- `MLOAD` costs only 3 units of gas, `SLOAD`(warm access) is about 100 units. Therefore, cache, when it's possible.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................

        // Lines: [184-279]
          // Comment: allocate newProposal in memory as well in order to avoid warm access in providing newProposal.id, newProposal.startBlock, newProposal.endBlock in events and for return value as well. 

          emit ProposalCreated(
              newProposal.id,
              msg.sender,
              targets,
              values,
              signatures,
              calldatas,
              newProposal.startBlock,
              newProposal.endBlock,
              description
          );

          emit ProposalCreatedWithRequirements(
              newProposal.id,
              msg.sender,
              targets,
              values,
              signatures,
              calldatas,
              newProposal.startBlock,
              newProposal.endBlock,
              newProposal.proposalThreshold,
              minQuorumVotes(),
              description
          );

          return newProposal.id;

      // Lines: [292-300]
        // Comment: also, use memory in order to read.
          for (uint256 i = 0; i < proposal.targets.length; i++) {
            queueOrRevertInternal(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                eta
            );
          }

      // Lines: [330-338]
          for (uint256 i = 0; i < proposal.targets.length; i++) {
            timelock.executeTransaction(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                proposal.eta
            );
          }

      // Lines: [350-365]
          require(
            msg.sender == proposal.proposer ||
                nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
            'NounsDAO::cancel: proposer above threshold'
        );

        proposal.canceled = true;
        for (uint256 i = 0; i < proposal.targets.length; i++) {
            timelock.cancelTransaction(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                proposal.eta
            );
        }

      // Lines: [382-390]
        for (uint256 i = 0; i < proposal.targets.length; i++) {
            timelock.cancelTransaction(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                proposal.eta
            );
        }

      // Lines: [434-453]
        // Comment: there is no need to allocate storage var here, since the function logic performs only reading operations. 

        Proposal storage proposal = _proposals[proposalId];
        if (proposal.vetoed) {
            return ProposalState.Vetoed;
        } else if (proposal.canceled) {
            return ProposalState.Canceled;
        } else if (block.number <= proposal.startBlock) {
            return ProposalState.Pending;
        } else if (block.number <= proposal.endBlock) {
            return ProposalState.Active;
        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {
            return ProposalState.Defeated;
        } else if (proposal.eta == 0) {
            return ProposalState.Succeeded;
        } else if (proposal.executed) {
            return ProposalState.Executed;
        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
            return ProposalState.Expired;
        } else {
            return ProposalState.Queued;
        }

      // Lines: [462-482]
        // Comment: the same as above
        function proposals(uint256 proposalId) external view returns (ProposalCondensed memory) {
          Proposal storage proposal = _proposals[proposalId];
          return
              ProposalCondensed({
                  id: proposal.id,
                  proposer: proposal.proposer,
                  proposalThreshold: proposal.proposalThreshold,
                  quorumVotes: quorumVotes(proposal.id),
                  eta: proposal.eta,
                  startBlock: proposal.startBlock,
                  endBlock: proposal.endBlock,
                  forVotes: proposal.forVotes,
                  againstVotes: proposal.againstVotes,
                  abstainVotes: proposal.abstainVotes,
                  canceled: proposal.canceled,
                  vetoed: proposal.vetoed,
                  executed: proposal.executed,
                  totalSupply: proposal.totalSupply,
                  creationBlock: proposal.creationBlock
              });
          }

      // Lines: [593-612]
        // Comment: `receipt`, `proposal` could be cached

          require(state(proposalId) == ProposalState.Active, 'NounsDAO::castVoteInternal: voting is closed');
          require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type');
          Proposal storage proposal = _proposals[proposalId];
          Receipt storage receipt = proposal.receipts[voter];
          require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

          /// @notice: Unlike GovernerBravo, votes are considered from the block the proposal was created in order to normalize quorumVotes and proposalThreshold metrics
          uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));

          if (support == 0) {
              proposal.againstVotes = proposal.againstVotes + votes;
          } else if (support == 1) {
              proposal.forVotes = proposal.forVotes + votes;
          } else if (support == 2) {
              proposal.abstainVotes = proposal.abstainVotes + votes;
          }

          receipt.hasVoted = true;
          receipt.support = support;
          receipt.votes = votes;

      // Lines: [866-871]
        // Args via calldata instead of storage ref

        function proposalCreationBlock(Proposal storage proposal) internal view returns (uint256) {
          if (proposal.creationBlock == 0) {
              return proposal.startBlock - votingDelay;
          }
          return proposal.creationBlock;
        }

      // Lines: [877-889]
        function quorumVotes(uint256 proposalId) public view returns (uint256) {
          Proposal storage proposal = _proposals[proposalId];
          if (proposal.totalSupply == 0) {
              return proposal.quorumVotes;
          }

          return
              dynamicQuorumVotes(
                  proposal.againstVotes,
                  proposal.totalSupply,
                  getDynamicQuorumParamsAt(proposal.creationBlock)
              );
        }

        ```

## **[G-07] Add `require() before some computations, if it makes sense`**<a name="G-07"></a>


### ***Description:***

- Everyting above `require()` takes some gas for execution, therefore if the statement reverts gas will not be retrieved.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................
      
        // Lines: [197-208]
          // Comment: the following checks could be performed before interacting with `ProposalTemp`

          require(
              nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
              'NounsDAO::propose: proposer votes below proposal threshold'
          );
          require(
              targets.length == values.length &&
                  targets.length == signatures.length &&
                  targets.length == calldatas.length,
              'NounsDAO::propose: proposal function information arity mismatch'
          );
          require(targets.length != 0, 'NounsDAO::propose: must provide actions');
          require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');

    ```

## **[G-08] Unset the variables which are no longer used**<a name="G-08"></a>

### ***Description:***

- By resetting the state vars to their initial state, you'll be getting a gas refund.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................
      
        // Lines: [323-340]
          // Comment: after a proposal got executed, it is possible to delete the proposal if it's no longer used. 

          function execute(uint256 proposalId) external {
              require(
                  state(proposalId) == ProposalState.Queued,
                  'NounsDAO::execute: proposal can only be executed if it is queued'
              );
              Proposal storage proposal = _proposals[proposalId];
              proposal.executed = true;
              for (uint256 i = 0; i < proposal.targets.length; i++) {
                  timelock.executeTransaction(
                      proposal.targets[i],
                      proposal.values[i],
                      proposal.signatures[i],
                      proposal.calldatas[i],
                      proposal.eta
                  );
              }
              emit ProposalExecuted(proposalId);
          }
    ```

## **[G-10] `Internal` functions can be inlined**<a name="G-10"></a>

### ***Description:***

- It takes some extra `JUMP`s which costs around 40-50 gas uints. In loops it will save significant amount of gas.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................
      
        // Lines: [294-306]
          function queueOrRevertInternal(
              address target,
              uint256 value,
              string memory signature,
              bytes memory data,
              uint256 eta
          ) internal {
              require(
                  !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
                  'NounsDAO::queueOrRevertInternal: identical proposal action already queued at eta'
              );
              timelock.queueTransaction(target, value, signature, data, eta);
          }
    ```

## **[G-11] Cache the array.length into a variable**<a name="G-11"></a>

### ***Description:***

- Reduces the extra `DUP<X>` for every iteration and also removes warm access per each iteration.

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................
      
        // Lines: [292-292]
          // Please, check all loops, it's a significant amount of gas usage for each call as well.

            for (uint256 i = 0; i < proposal.targets.length; i++) {}
    ```


## **[G-15] Use `private/internal` for `constants/immutable/state` variables instead of `public`**<a name="G-15"></a>

### ***Description:***

- Optimization comes from not creating a getter function for each `public` instance.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol 
      ...............................
      
        // Lines: [59-105]
          string public constant name = 'Nouns DAO';

          /// @notice The minimum setable proposal threshold
          uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%

          /// @notice The maximum setable proposal threshold
          uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%

          /// @notice The minimum setable voting period
          uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours

          /// @notice The max setable voting period
          uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks

          /// @notice The min setable voting delay
          uint256 public constant MIN_VOTING_DELAY = 1;

          /// @notice The max setable voting delay
          uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week

          /// @notice The lower bound of minimum quorum votes basis points
          uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200; // 200 basis points or 2%

          /// @notice The upper bound of minimum quorum votes basis points
          uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000; // 2,000 basis points or 20%

          /// @notice The upper bound of maximum quorum votes basis points
          uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000; // 4,000 basis points or 60%

          /// @notice The maximum setable quorum votes basis points
          uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%

          /// @notice The maximum number of actions that can be included in a proposal
          uint256 public constant proposalMaxOperations = 10; // 10 actions

          /// @notice The maximum priority fee used to cap gas refunds in `castRefundableVote`
          uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;

          /// @notice The vote refund gas overhead, including 7K for ETH transfer and 29K for general transaction overhead
          uint256 public constant REFUND_BASE_GAS = 36000;

          /// @notice The EIP-712 typehash for the contract's domain
          bytes32 public constant DOMAIN_TYPEHASH =
              keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');

          /// @notice The EIP-712 typehash for the ballot struct used by the contract
          bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

    ```

## **[G-16] Define modifier/function to cover the same require statements**<a name="G-16"></a>

### ***Description:***
- Optimization relies on the deployemnt cost

### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................

        // Lines: [123-123]
          require(msg.sender == admin, 'NounsDAO::initialize: admin only');
      
        // Lines: [581-581]
          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

        // Lines: [563-563]
          require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');
        
        // Lines: [599-599]
          require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

    ```


## **[G-17] Use const values instead of `type(uint256).max`**<a name="G-17"></a>

### ***Description:***

- Not sure about readability, but it might be tangible in loops.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV2.sol
      ...............................
      
        // Lines: [1019-1019]
          require(n <= type(uint32).max, errorMessage);

        // Lines: [1024-1024]
            if (n > type(uint16).max) {}
    ```

## **[G-19] Use `uint` instead of `uint8`, `uint64`, if it's possible**<a name="G-19"></a>

### ***Description:***

- I deployed a contract with only a single error and function and compared between:
  - `error VaultAlreadyUnderAuction(bytes12 vaultId, address witch);`.
  - `error VaultAlreadyUnderAuction(bytes32 vaultId, address witch);`.
- The second one is saving about ~10k units of gas for deployment and 10k per each transaction.
- However, defaining further errors with !`bytes32` doesn't give a huge optimization.  
- The stack slots are 32bytes, just use 32bytes, if you are not trying to tight up the storage.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/base/ERC721Checkpointable.sol 
      ...............................
      
        // Lines: [152-152]
          uint32 nCheckpoints = numCheckpoints[account];

        // Lines: [166-166]
          uint32 nCheckpoints = numCheckpoints[account];

        // Lines: [181-182]
          uint32 lower = 0;
          uint32 upper = nCheckpoints - 1;

        // Lines: [184-184]
          uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow

        // Lines: [217-217]
          uint32 srcRepNum = numCheckpoints[srcRep];
          uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
          uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');

        // etc...


      ```
## **[G-20] Mark functions as `external` instead of `public`, if there are no internal calls**<a name="G-20"></a>

### ***Description:***

- Functions marked by `external` use call data to read arguments, where `public` will first allocate in local memory and then load them.
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................

        // Lines: [113-121]
          function initialize(
              address timelock_,
              address nouns_,
              address vetoer_,
              uint256 votingPeriod_,
              uint256 votingDelay_,
              uint256 proposalThresholdBPS_,
              uint256 quorumVotesBPS_
          ) public virtual {}

        //  etc.. 

      ```
## **[G-21] Use `calldataload` instead of `mload`**<a name="G-21"></a>

### ***Description:***

- After Berlin hard fork, to load a non-zero byte from calldata dropped from 64 units of gas to 16 units, therefore if you do not modify args, use a calldata instead of memory. Here you need to explicitly specify `calldataload`, or replace `memory` with `calldata`. If the args are pretty huge, allocating args in memory will cost a lot. 
  
### ***All occurances:***

- Contracts:
  
    ```Solidity
      file: contracts/governance/NounsDAOLogicV1.sol 
      ...............................
      
        // Lines: [174-180]

          function propose(
              address[] memory targets,
              uint256[] memory values,
              string[] memory signatures,
              bytes[] memory calldatas,
              string memory description
          ) public returns (uint256) {}

        // Lines: [294-300]
        function queueOrRevertInternal(
              address target,
              uint256 value,
              string memory signature,
              bytes memory data,
              uint256 eta
        ) internal {

        // Lines: [392-400]
          // Comments: you can also return what you need directly from calldata here.
            function getActions(uint256 proposalId)
              external
              view
              returns (
                  address[] memory targets,
                  uint256[] memory values,
                  string[] memory signatures,
                  bytes[] memory calldatas
            )


      ```
## Kudos for the quality of the code! It's pretty easy to explore
