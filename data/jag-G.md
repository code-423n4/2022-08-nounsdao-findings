contracts/governance/NounsDAOProxy.sol	
1. Use local variable instead of storage
2. Delete receive() since it calls _fallback(). Let send, transfer functions default to fallback().
Since fallback is the only function to use _fallback(), delete  _fallback() and implement the logic directly in fallback().
This saves branching opcode. Every single gas saved in proxy is huge !
3. Use UUPS proxy instead of transparent proxy design. For every call to proxy, by default all functions will be routed to fallback and no function selector checks are required.

``` git diff
diff --git a/contracts/governance/NounsDAOProxy.sol b/contracts/governance/NounsDAOProxy.sol
index f42e3bf..f4e66a2 100644
--- a/contracts/governance/NounsDAOProxy.sol
+++ b/contracts/governance/NounsDAOProxy.sol
@@ -82,7 +82,8 @@ contract NounsDAOProxy is NounsDAOProxyStorage, NounsDAOEvents {
         address oldImplementation = implementation;
         implementation = implementation_;
 
-        emit NewImplementation(oldImplementation, implementation);
+        // Use local variable instead of storage
+        emit NewImplementation(oldImplementation, implementation_);
     }
 
     /**
@@ -100,13 +101,13 @@ contract NounsDAOProxy is NounsDAOProxyStorage, NounsDAOEvents {
         }
     }
 
+
     /**
-     * @dev Delegates execution to an implementation contract.
-     * It returns to the external caller whatever the implementation returns
-     * or forwards reverts.
+     * @dev Fallback function that delegates calls to the `implementation`. Will run if no other
+     * function in the contract matches the call data.
      */
-    function _fallback() internal {
-        // delegate all other functions to current implementation
+    fallback() external payable {
+                // delegate all other functions to current implementation
         (bool success, ) = implementation.delegatecall(msg.data);
 
         assembly {
@@ -123,19 +124,11 @@ contract NounsDAOProxy is NounsDAOProxyStorage, NounsDAOEvents {
         }
     }
 
-    /**
-     * @dev Fallback function that delegates calls to the `implementation`. Will run if no other
-     * function in the contract matches the call data.
-     */
-    fallback() external payable {
-        _fallback();
-    }
-
     /**
      * @dev Fallback function that delegates calls to `implementation`. Will run if call data
      * is empty.
      */
-    receive() external payable {
-        _fallback();
-    }
+    // receive() external payable {
+    //     _fallback();
+    // }
 }

```
contracts/governance/NounsDAOLogicV2.sol
1. Use local variable instead of storage variables.
2. Use unchecked wherever arithmetic operation does not under/over flow.
3. No need to initialize state variables to 0.
4. Use pre increment (++i)
5. Optimize for loop; unchecked pre increment, use local variable to compare condition termination, no need to initialize variable to 0.

``` git diff
diff --git a/contracts/governance/NounsDAOLogicV2.sol b/contracts/governance/NounsDAOLogicV2.sol
index ccd7432..03305fe 100644
--- a/contracts/governance/NounsDAOLogicV2.sol
+++ b/contracts/governance/NounsDAOLogicV2.sol
@@ -220,62 +220,68 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
             );
         }
 
-        temp.startBlock = block.number + votingDelay;
-        temp.endBlock = temp.startBlock + votingPeriod;
+        unchecked {
+            temp.startBlock = block.number + votingDelay;
+            temp.endBlock = temp.startBlock + votingPeriod;
+        }
 
-        proposalCount++;
+        //Use pre increment
+        ++proposalCount;
         Proposal storage newProposal = _proposals[proposalCount];
         newProposal.id = proposalCount;
         newProposal.proposer = msg.sender;
         newProposal.proposalThreshold = temp.proposalThreshold;
-        newProposal.eta = 0;
+        //newProposal.eta = 0;
         newProposal.targets = targets;
         newProposal.values = values;
         newProposal.signatures = signatures;
         newProposal.calldatas = calldatas;
         newProposal.startBlock = temp.startBlock;
         newProposal.endBlock = temp.endBlock;
-        newProposal.forVotes = 0;
-        newProposal.againstVotes = 0;
-        newProposal.abstainVotes = 0;
-        newProposal.canceled = false;
-        newProposal.executed = false;
-        newProposal.vetoed = false;
+        //default vaule is 0, no need to initialize
+        //newProposal.forVotes = 0;
+        //newProposal.againstVotes = 0;
+        //newProposal.abstainVotes = 0;
+        //newProposal.canceled = false;
+        //newProposal.executed = false;
+        //newProposal.vetoed = false;
         newProposal.totalSupply = temp.totalSupply;
         newProposal.creationBlock = block.number;
 
-        latestProposalIds[newProposal.proposer] = newProposal.id;
+        //Use local / memory variable proposalCount, temp.startBlock , temp.endBlock,
+        //temp.proposalThreshold instead of storage read.
+        latestProposalIds[newProposal.proposer] = proposalCount;
 
         /// @notice Maintains backwards compatibility with GovernorBravo events
         emit ProposalCreated(
-            newProposal.id,
+            proposalCount,
             msg.sender,
             targets,
             values,
             signatures,
             calldatas,
-            newProposal.startBlock,
-            newProposal.endBlock,
+            temp.startBlock,
+            temp.endBlock,
             description
         );
 
         /// @notice Updated event with `proposalThreshold` and `minQuorumVotes`
         /// @notice `minQuorumVotes` is always zero since V2 introduces dynamic quorum with checkpoints
         emit ProposalCreatedWithRequirements(
-            newProposal.id,
+            proposalCount,
             msg.sender,
             targets,
             values,
             signatures,
             calldatas,
-            newProposal.startBlock,
-            newProposal.endBlock,
-            newProposal.proposalThreshold,
+            temp.startBlock,
+            temp.endBlock,
+            temp.proposalThreshold,
             minQuorumVotes(),
             description
         );
 
-        return newProposal.id;
+        return proposalCount;
     }
 
     /**
@@ -354,7 +360,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         );
 
         proposal.canceled = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
@@ -362,6 +369,9 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
                 proposal.calldatas[i],
                 proposal.eta
             );
+            unchecked {
+                ++i;
+            }
         }
 
         emit ProposalCanceled(proposalId);
@@ -379,7 +389,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         Proposal storage proposal = _proposals[proposalId];
 
         proposal.vetoed = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
@@ -387,6 +398,9 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
                 proposal.calldatas[i],
                 proposal.eta
             );
+            unchecked {
+                ++i;
+            }
         }
 
         emit ProposalVetoed(proposalId);
```
contracts/governance/NounsDAOLogicV1.sol

1. Use local variable instead of storage variables.
2. Use unchecked wherever arithmetic operation does not under/over flow.
3. No need to initialize state variables to 0.
4. Use pre increment (++i)
5. Optimize for loop; unchecked pre increment, use local variable to compare condition termination, no need to initialize variable to 0.

``` git diff
diff --git a/contracts/governance/NounsDAOLogicV1.sol b/contracts/governance/NounsDAOLogicV1.sol
index 5654382..8f56b11 100644
--- a/contracts/governance/NounsDAOLogicV1.sol
+++ b/contracts/governance/NounsDAOLogicV1.sol
@@ -210,61 +210,64 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
             );
         }
 
-        temp.startBlock = block.number + votingDelay;
-        temp.endBlock = temp.startBlock + votingPeriod;
+        unchecked {
+            temp.startBlock = block.number + votingDelay;
+            temp.endBlock = temp.startBlock + votingPeriod;
+        }
 
-        proposalCount++;
+        ++proposalCount;
         Proposal storage newProposal = proposals[proposalCount];
 
         newProposal.id = proposalCount;
         newProposal.proposer = msg.sender;
         newProposal.proposalThreshold = temp.proposalThreshold;
         newProposal.quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply);
-        newProposal.eta = 0;
+        //newProposal.eta = 0;
         newProposal.targets = targets;
         newProposal.values = values;
         newProposal.signatures = signatures;
         newProposal.calldatas = calldatas;
         newProposal.startBlock = temp.startBlock;
         newProposal.endBlock = temp.endBlock;
-        newProposal.forVotes = 0;
-        newProposal.againstVotes = 0;
-        newProposal.abstainVotes = 0;
-        newProposal.canceled = false;
-        newProposal.executed = false;
-        newProposal.vetoed = false;
+        //default vaule is 0, no need to initialize
+        //newProposal.forVotes = 0;
+        //newProposal.againstVotes = 0;
+        //newProposal.abstainVotes = 0;
+        //newProposal.canceled = false;
+        //newProposal.executed = false;
+        //newProposal.vetoed = false;
 
-        latestProposalIds[newProposal.proposer] = newProposal.id;
+        latestProposalIds[newProposal.proposer] = proposalCount;
 
         /// @notice Maintains backwards compatibility with GovernorBravo events
         emit ProposalCreated(
-            newProposal.id,
+            proposalCount,
             msg.sender,
             targets,
             values,
             signatures,
             calldatas,
-            newProposal.startBlock,
-            newProposal.endBlock,
+            temp.startBlock,
+            temp.endBlock,
             description
         );
 
         /// @notice Updated event with `proposalThreshold` and `quorumVotes`
         emit ProposalCreatedWithRequirements(
-            newProposal.id,
+            proposalCount,
             msg.sender,
             targets,
             values,
             signatures,
             calldatas,
-            newProposal.startBlock,
-            newProposal.endBlock,
-            newProposal.proposalThreshold,
+            temp.startBlock,
+            temp.endBlock,
+            temp.proposalThreshold,
             newProposal.quorumVotes,
             description
         );
 
-        return newProposal.id;
+        return proposalCount;
     }
 
     /**
@@ -278,7 +281,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         );
         Proposal storage proposal = proposals[proposalId];
         uint256 eta = block.timestamp + timelock.delay();
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             queueOrRevertInternal(
                 proposal.targets[i],
                 proposal.values[i],
@@ -286,6 +290,9 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
                 proposal.calldatas[i],
                 eta
             );
+            unchecked {
+                ++i;
+            }
         }
         proposal.eta = eta;
         emit ProposalQueued(proposalId, eta);
@@ -316,7 +323,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         );
         Proposal storage proposal = proposals[proposalId];
         proposal.executed = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             timelock.executeTransaction(
                 proposal.targets[i],
                 proposal.values[i],
@@ -324,6 +332,9 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
                 proposal.calldatas[i],
                 proposal.eta
             );
+            unchecked {
+                ++i;
+            }
         }
         emit ProposalExecuted(proposalId);
     }
@@ -343,7 +354,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         );
 
         proposal.canceled = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
@@ -351,6 +363,9 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
                 proposal.calldatas[i],
                 proposal.eta
             );
+            unchecked {
+                ++i;
+            }
         }
 
         emit ProposalCanceled(proposalId);
@@ -368,7 +383,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         Proposal storage proposal = proposals[proposalId];
 
         proposal.vetoed = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 _len = proposal.targets.length;
+        for (uint256 i; i < _len;) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
@@ -376,6 +392,9 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
                 proposal.calldatas[i],
                 proposal.eta
             );
+            unchecked {
+                ++i;
+            }
         }
 
         emit ProposalVetoed(proposalId);
@@ -551,7 +570,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         uint256 oldVotingPeriod = votingPeriod;
         votingPeriod = newVotingPeriod;
 
-        emit VotingPeriodSet(oldVotingPeriod, votingPeriod);
+        // Use local variable instead of storage
+        emit VotingPeriodSet(oldVotingPeriod, newVotingPeriod);
     }
 
     /**
@@ -569,7 +589,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         uint256 oldProposalThresholdBPS = proposalThresholdBPS;
         proposalThresholdBPS = newProposalThresholdBPS;
 
-        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, proposalThresholdBPS);
+        // Use local variable instead of storage
+        emit ProposalThresholdBPSSet(oldProposalThresholdBPS, newProposalThresholdBPS);
     }
 
     /**
@@ -586,7 +607,8 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         uint256 oldQuorumVotesBPS = quorumVotesBPS;
         quorumVotesBPS = newQuorumVotesBPS;
 
-        emit QuorumVotesBPSSet(oldQuorumVotesBPS, quorumVotesBPS);
+        // Use local variable instead of storage
+        emit QuorumVotesBPSSet(oldQuorumVotesBPS, newQuorumVotesBPS);
     }
 
     /**
@@ -626,8 +648,9 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
         // Clear the pending value
         pendingAdmin = address(0);
 
-        emit NewAdmin(oldAdmin, admin);
-        emit NewPendingAdmin(oldPendingAdmin, pendingAdmin);
+        // Use local variable instead of storage
+        emit NewAdmin(oldAdmin, pendingAdmin);
+        emit NewPendingAdmin(oldPendingAdmin, address(0));
     }
 
     /**
@@ -637,7 +660,7 @@ contract NounsDAOLogicV1 is NounsDAOStorageV1, NounsDAOEvents {
     function _setVetoer(address newVetoer) public {
         require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
 
-        emit NewVetoer(vetoer, newVetoer);
+        emit NewVetoer(msg.sender, newVetoer);
 
         vetoer = newVetoer;
     }

```
contracts/base/ERC721Checkpointable.sol	

1. Use unchecked wherever arithmetic operation does not under/over flow.
2. No need to initialize variable to 0.
``` git diff
diff --git a/contracts/base/ERC721Checkpointable.sol b/contracts/base/ERC721Checkpointable.sol
index 2c60d61..9441ce6 100644
--- a/contracts/base/ERC721Checkpointable.sol
+++ b/contracts/base/ERC721Checkpointable.sol
@@ -178,8 +178,11 @@ abstract contract ERC721Checkpointable is ERC721Enumerable {
             return 0;
         }
 
-        uint32 lower = 0;
-        uint32 upper = nCheckpoints - 1;
+        uint32 lower;
+        uint32 upper;
+        unchecked {
+            upper = nCheckpoints - 1;
+        }
         while (upper > lower) {
             uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
             Checkpoint memory cp = checkpoints[account][center];
@@ -188,7 +191,9 @@ abstract contract ERC721Checkpointable is ERC721Enumerable {
             } else if (cp.fromBlock < blockNumber) {
                 lower = center;
             } else {
-                upper = center - 1;
+                unchecked {
+                    upper = center - 1;
+                }
             }
         }
         return checkpoints[account][lower].votes;
```

contracts/base/ERC721Enumerable.sol
1. Use unchecked wherever arithmetic operation does not under/over flow.
``` git diff
diff --git a/contracts/base/ERC721Enumerable.sol b/contracts/base/ERC721Enumerable.sol
index 20de175..56fe903 100644
--- a/contracts/base/ERC721Enumerable.sol
+++ b/contracts/base/ERC721Enumerable.sol
@@ -144,9 +144,13 @@ abstract contract ERC721Enumerable is ERC721, IERC721Enumerable {
         // To prevent a gap in from's tokens array, we store the last token in the index of the token to delete, and
         // then delete the last slot (swap and pop).
 
-        uint256 lastTokenIndex = ERC721.balanceOf(from) - 1;
+        uint256 lastTokenIndex;
         uint256 tokenIndex = _ownedTokensIndex[tokenId];
 
+        unchecked {
+            lastTokenIndex = ERC721.balanceOf(from) - 1;
+        }
+
         // When the token to delete is the last token, the swap operation is unnecessary
         if (tokenIndex != lastTokenIndex) {
             uint256 lastTokenId = _ownedTokens[from][lastTokenIndex];
@@ -169,9 +173,13 @@ abstract contract ERC721Enumerable is ERC721, IERC721Enumerable {
         // To prevent a gap in the tokens array, we store the last token in the index of the token to delete, and
         // then delete the last slot (swap and pop).
 
-        uint256 lastTokenIndex = _allTokens.length - 1;
+        uint256 lastTokenIndex;
         uint256 tokenIndex = _allTokensIndex[tokenId];
 
+        unchecked {
+            lastTokenIndex = _allTokens.length - 1;
+        }
+
         // When the token to delete is the last token, the swap operation is unnecessary. However, since this occurs so
         // rarely (when the last minted token is burnt) that we still do the swap here to avoid the gas cost of adding
         // an 'if' statement (like in _removeTokenFromOwnerEnumeration)
```