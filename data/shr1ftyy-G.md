## NounsDAOLogicV2.sol - queue() - Array Length Caching
### git diff:
``` diff 
@@ -289,7 +289,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         );
         Proposal storage proposal = _proposals[proposalId];
         uint256 eta = block.timestamp + timelock.delay();
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 len = proposal.targets.length;
+        for (uint256 i = 0; i < len; i++) {
             queueOrRevertInternal(
                 proposal.targets[i],
                 proposal.values[i],
```

### Gas Usage
 |          | Before: | After: | Savings: |
 |---------|---------|--------|----------|
 | Avg.   | 140617  | 140507 | 110      |

## NounsDAOLogicV2.sol - execute() - Array Length Caching
### git diff:
``` diff 
@@ -327,7 +328,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         );
         Proposal storage proposal = _proposals[proposalId];
         proposal.executed = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 len = proposal.targets.length;
+        for (uint256 i = 0; i < len; i++) {
             timelock.executeTransaction(
                 proposal.targets[i],
                 proposal.values[i],
```


### Gas Usage
|         |  Before: | After: | Savings: |
|---------|---------|--------|----------|
| Avg.   |   117959 | 102664 | 98      |

## NounsDAOLogicV2.sol - cancel() - Array Length Caching
### git diff:
``` diff 
@@ -354,7 +356,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         );
 
         proposal.canceled = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 len = proposal.targets.length;
+        for (uint256 i = 0; i < len; i++) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
```


### Gas Usage
|         |  Before: | After: | Savings: |
|---------|---------|--------|----------|
| Avg.   |   102664 | 102765 | 101      |
| Min.   |   95154 |  95205| 101      |
| Max.   |   110174 | 110275 | 101      |

## NounsDAOLogicV2.sol - veto() - Array Length Caching
### git diff:
``` diff
@@ -379,7 +382,8 @@ contract NounsDAOLogicV2 is NounsDAOStorageV2, NounsDAOEventsV2 {
         Proposal storage proposal = _proposals[proposalId];
 
         proposal.vetoed = true;
-        for (uint256 i = 0; i < proposal.targets.length; i++) {
+        uint256 len = proposal.targets.length;
+        for (uint256 i = 0; i < len; i++) {
             timelock.cancelTransaction(
                 proposal.targets[i],
                 proposal.values[i],
```
### Gas Usage
|         |  Before: | After: | Savings: |
|---------|---------|--------|----------|
| Avg.   |   98538 | 98429 | 101      |
| Min.   |   73108 | 72999 | 109      |
| Max.   |   116668 | 116559 | 109      |



## Total Average Gas Saved: 410


----------------------------------------

|         |  Before: | After: | Savings: |
|---------|---------|--------|----------|
| Avg.   |   455467 | 98429 | 101      |
| Min.   |   453809 | 72999 | 109      |
| Max.   |   473709 | 116559 | 109      |
              