## 1. `++i` costs less gas compared to `i++` or `i += 1`, same for `--i/i--`. Especially in for loops

`++i` costs less gas compared to `i++` or `i += 1` for unsigned integer, as pre-increment is cheaper (about 5 gas per iteration). This statement is true even with the optimizer enabled.

`i++` increments i and returns the initial value of `i`.
```
uint i = 1;  
i++; // == 1 but i == 2
```
But ++i returns the actual incremented value:
```
uint i = 1;  
++i; // == 2 and i == 2 too, so no need for a temporary variable  
```
In the first case, the compiler has to create a temporary variable (when used) for returning 1 instead of 2

I suggest using ++i instead of i++ to increment the value of an uint variable.

If done inside for loop, saves 6 gas per loop.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
216:        proposalCount++;
281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
226:        proposalCount++;
292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## 2. Use a more recent version of solidity

-  Use a solidity version of at least 0.8.2 to get compiler automatic inlining  
- Use a solidity version of at least 0.8.3 to get better struct packing and cheaper multiple storage reads  
- Use a solidity version of at least 0.8.4 to get custom errors, which are cheaper at deployment than `revert()/require()` strings  
- Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol
```
File: ERC721Enumerable.sol
28:	pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
35:	pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol
```
File: NounsDAOProxy.sol
36:	pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol
```
FIle: NounsDAOInterfaces.sol
33:	pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
61:	pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
53:	pragma solidity ^0.8.6;
```

## 3. No need to explicitly initialize variables with default values

If a variable is not set/initialized, it is assumed to have the default value (`0` for uint, `false` for bool, `address(0)` for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

As an example: `for (uint256 i = 0; i < proposal.targets.length; i++) {` should be replaced with `for (uint256 i; i < proposal.targets.length; ++i) {`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
181:        uint32 lower = 0;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
948;        uint256 lower = 0;
```

## 4. Use Custom Errors instead of Revert Strings to save Gas


Custom errors from Solidity 0.8.4 are cheaper than revert string (cheaper deployment cost and runtime cost when the revert condition is met)

Source: https://blog.soliditylang.org/2021/04/21/custom-errors/:

> Starting from Solidity v0.8.4, there is a convenient and gas-efficient way to explain to users why an operation failed through the use of custom errors. Until now, you could already use strings to give more information about failures (e.g., revert("Insufficient funds.");), but they are rather expensive, especially when it comes to deploy cost, and it is difficult to use dynamic information in them.

Custom errors are defined using the error statement, which can be used inside and outside of contracts (including interfaces and libraries).

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol
```
File: ERC721Enumerable.sol
62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol
```
File: NounsDAOProxy.sol
79:			require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:			require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol 
122:		require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
123:		require(msg.sender == admin, 'NounsDAO::initialize: admin only');
124:		require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
125:		require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
126:		require(
127:			votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:			'NounsDAO::initialize: invalid voting period'
129:		);
130:		require(
131:			votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:			'NounsDAO::initialize: invalid voting delay'
133:		);
134:		require(
135:			proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:			'NounsDAO::initialize: invalid proposal threshold'
137:		);
138:		require(
139:			quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:			'NounsDAO::initialize: invalid proposal threshold'
141:		 );
...
187:		require(
188:			nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189:			'NounsDAO::propose: proposer votes below proposal threshold'
190:		);
191:		require(
192:			targets.length == values.length &&
193:				targets.length == signatures.length &&
194:				targets.length == calldatas.length,
195:			'NounsDAO::propose: proposal function information arity mismatch'
196:		);
197:		require(targets.length != 0, 'NounsDAO::propose: must provide actions');
198:		require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
...
Other lines and blocks with this issue in the contract: 203-209, 275-278, 301-304, 313-316, 336,
 339-343, 364-366, 422, 485, 501, 502, 505, 530-534, 546-550, 563-568, 581-585, 599, 617, 638, 651 
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
Lines and blocks with this issue in the contract: 133-148, 197-208, 213-220, 286-289, 312-315, 
324-327, 347, 350-354, 375-377, 433, 577, 593, 594, 597, 622-626, 638-642, 655-660, 674-685, 702-712, 727, 801, 819, 840, 853
```

## 5. Splitting `require()` statements that use `&&` saves gas


Instead of using the && operator in a single require statement to check multiple conditions, I suggest using multiple require statements with 1 condition per require statement

Link to the issue: https://github.com/code-423n4/2022-01-xdefi-findings/issues/128

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
126:		require(
127:			votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:			'NounsDAO::initialize: invalid voting period'
129:		);
130:		require(
131:			votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:			'NounsDAO::initialize: invalid voting delay'
133:		);
134:		require(
135:			proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:			'NounsDAO::initialize: invalid proposal threshold'
137:		);
138:		require(
139:			quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:			'NounsDAO::initialize: invalid proposal threshold'
141:		 );
...
191:		require(
192:			targets.length == values.length &&
193:				targets.length == signatures.length &&
194:				targets.length == calldatas.length,
195:			'NounsDAO::propose: proposal function information arity mismatch'
196:		);
...
531:		require(
532:			newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:			'NounsDAO::_setVotingDelay: invalid voting delay'
534:		);
...
547:		require(
548:			newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:			'NounsDAO::_setVotingPeriod: invalid voting period'
550:		);
...
564:		require(
565:			newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:				newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:			'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:		);
...
582:		require(
583:			newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:			'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:		);
...
617:		require(msg.sender  == pendingAdmin &&  msg.sender  !=  address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
137:		require(
138:			votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:			'NounsDAO::initialize: invalid voting period'
140:		);
141:		require(
142:			votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:			'NounsDAO::initialize: invalid voting delay'
144:		);
145:		require(
146:			proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:			'NounsDAO::initialize: invalid proposal threshold'
148:		);
...
201:		require(
202:			targets.length == values.length &&
203:				targets.length == signatures.length &&
204:				targets.length == calldatas.length,
205:			'NounsDAO::propose: proposal function information arity mismatch'
206:		);
...
623:		require(
624:			newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:			'NounsDAO::_setVotingDelay: invalid voting delay'
626:		);
...
639:		require(
640:			newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:			'NounsDAO::_setVotingPeriod: invalid voting period'
642:		);
...
656:		require(
657:			newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:				newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:			'NounsDAO::_setProposalThreshold: invalid proposal threshold'
660:		);
...
677:		require(
678:			newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679					newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:			'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:		);
```

## 6. Using `> 0` costs more gas than `!= 0` for Unsigned Integers

This change saves 6 gas per instance

Proof: While it may seem that `> 0` is cheaper than `!=`, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you’re in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

I suggest changing > 0 with != 0 here

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
153:        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;
215:        if (srcRep != dstRep && amount > 0) {
218:                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;
225:                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;
243:        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
541:        if (votes > 0) {
967:        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
```

## 7. Increments can be unchecked

In Solidity 0.8+, there’s a default overflow check on unsigned integers. It’s possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline. This saves 30-40 gas PER LOOP.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
281:		for (uint256 i = 0; i < proposal.targets.length; i++) {
282:			queueOrRevertInternal(
283:				proposal.targets[i],
284:				proposal.values[i],
285:				proposal.signatures[i],
286:				proposal.calldatas[i],
287:				eta
288:			);
289:		}
...
319:		for (uint256 i = 0; i < proposal.targets.length; i++) {
320:			timelock.executeTransaction(
321:				proposal.targets[i],
322:				proposal.values[i],
323:				proposal.signatures[i],
324:				proposal.calldatas[i],
325:				proposal.eta
326:			);
327:		}
...
346:		for (uint256 i = 0; i < proposal.targets.length; i++) {
347:			timelock.cancelTransaction(
348:				proposal.targets[i],
349:				proposal.values[i],
350:				proposal.signatures[i],
351:				proposal.calldatas[i],
352:				proposal.eta
353:			);
354:		}
...
371:		for (uint256 i = 0; i < proposal.targets.length; i++) {
372:			timelock.cancelTransaction(
373:				proposal.targets[i],
374:				proposal.values[i],
375:				proposal.signatures[i],
376:				proposal.calldatas[i],
377:				proposal.eta
378:			);
379:		}
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
292:		for (uint256 i = 0; i < proposal.targets.length; i++) {
293:			queueOrRevertInternal(
294:				proposal.targets[i],
295:				proposal.values[i],
296:				proposal.signatures[i],
297:				proposal.calldatas[i],
298:				eta
299:			);
300:		}
...
330:		for (uint256 i = 0; i < proposal.targets.length; i++) {
331:			timelock.executeTransaction(
332:				proposal.targets[i],
333:				proposal.values[i],
334:				proposal.signatures[i],
335:				proposal.calldatas[i],
336:				proposal.eta
337:			);
338:		}
...
357:		for (uint256 i = 0; i < proposal.targets.length; i++) {
358:			timelock.cancelTransaction(
359:				proposal.targets[i],
360:				proposal.values[i],
361:				proposal.signatures[i],
362:				proposal.calldatas[i],
363:				proposal.eta
364:			);
365:		}
...
382:		for (uint256 i = 0; i < proposal.targets.length; i++) {
383:			timelock.cancelTransaction(
384:				proposal.targets[i],
385:				proposal.values[i],
386:				proposal.signatures[i],
387:				proposal.calldatas[i],
388:				proposal.eta
389:			);
390:		}
```

## 8. `<ARRAY>.LENGTH` should not be looked up in every loop of a for-loop

The overheads outlined below are PER LOOP, excluding the first loop

- storage arrays incur a Gwarmaccess (100 gas)
- memory arrays use MLOAD (3 gas)
- calldata arrays use CALLDATALOAD (3 gas)

Caching the length changes each of these to a DUP\<N> (3 gas), and gets rid of the extra DUP\<N> needed to store the stack offset

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
281:		for (uint256 i = 0; i < proposal.targets.length; i++) {
319:		for (uint256 i = 0; i < proposal.targets.length; i++) {
346:		for (uint256 i = 0; i < proposal.targets.length; i++) {
371:		for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
292:		for (uint256 i = 0; i < proposal.targets.length; i++) {
330:		for (uint256 i = 0; i < proposal.targets.length; i++) {
357:		for (uint256 i = 0; i < proposal.targets.length; i++) {
382:		for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## 9. `require()/revert()` string longer than 32 bytes cost extra gas

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol
```
File: ERC721Enumerable.sol
62:        require(index < ERC721.balanceOf(owner), 'ERC721Enumerable: owner index out of bounds');
77:        require(index < ERC721Enumerable.totalSupply(), 'ERC721Enumerable: global index out of bounds');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
140:        require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');
141:        require(nonce == nonces[signatory]++, 'ERC721Checkpointable::delegateBySig: invalid nonce');
142:        require(block.timestamp <= expiry, 'ERC721Checkpointable::delegateBySig: signature expired');
164:        require(blockNumber < block.number, 'ERC721Checkpointable::getPriorVotes: not yet determined');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol
```
File: NounsDAOProxy.sol
79:			require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
80:			require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol 
122:		require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once');
123:		require(msg.sender == admin, 'NounsDAO::initialize: admin only');
124:		require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');
125:		require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');
126:		require(
127:			votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:			'NounsDAO::initialize: invalid voting period'
129:		);
130:		require(
131:			votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:			'NounsDAO::initialize: invalid voting delay'
133:		);
134:		require(
135:			proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:			'NounsDAO::initialize: invalid proposal threshold'
137:		);
138:		require(
139:			quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:			'NounsDAO::initialize: invalid proposal threshold'
141:		 );
...
187:		require(
188:			nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
189:			'NounsDAO::propose: proposer votes below proposal threshold'
190:		);
191:		require(
192:			targets.length == values.length &&
193:				targets.length == signatures.length &&
194:				targets.length == calldatas.length,
195:			'NounsDAO::propose: proposal function information arity mismatch'
196:		);
197:		require(targets.length != 0, 'NounsDAO::propose: must provide actions');
198:		require(targets.length <= proposalMaxOperations, 'NounsDAO::propose: too many actions');
...
Other lines and blocks with this issue in the contract: 203-209, 275-278, 301-304, 313-316, 336,
 339-343, 364-366, 422, 485, 501, 502, 505, 530-534, 546-550, 563-568, 581-585, 599, 617, 638, 651 
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
Lines and blocks with this issue in the contract: 133-148, 197-208, 213-220, 286-289, 312-315, 
324-327, 347, 350-354, 375-377, 433, 577, 593, 594, 597, 622-626, 638-642, 655-660, 674-685, 702-712, 727, 801, 819, 840, 853
```

## 10. Boolean comparisons

Comparing to a constant (true or false) is a bit more expensive than directly checking the returned boolean value. I suggest using `if(!directValue)` instead of `if(directValue == false)`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
505:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
597:        require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
```

## 11. Usage of uints/ints smaller than 32 bytes (256 bits) incurs overhead

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: Checkpointable.sol
48:        uint32 fromBlock;
49:        uint96 votes;
```
There are a lot of these throughout contracts in scope, all should be checked and if possible types declared to uint/int (256).


## 12. `abi.encode()` is less efficient than `abi.encodepacked()`

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
135:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))
137:        	bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
302:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
480:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
482:      	  	bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
313:            !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),
572:            abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
574:        	bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
```

## 13. Using bools for storage incurs overhead

// Booleans are more expensive than uint256 or any type that takes up a full
// word because each write operation emits an extra SLOAD to first read the
// slot's contents, replace the bits taken up by the boolean, and then write
// back. This is the compiler's defense against contract upgrades and 
// pointer aliasing, and it cannot be disabled.
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27

Use uint256(1) and uint256(2) for true/false

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol
```
File: NounsDAOInterfaces.sol
204:        bool canceled;
206:        bool vetoed;
208:        bool executed;
216:        bool hasVoted;
304:        bool canceled;
306:        bool vetoed;
308:        bool executed;
320:        bool hasVoted;
390:        bool canceled;
292:        bool vetoed;
294:        bool executed;
```

## 14. Not using the names return varialbes when a function returns, wastes deployment gas

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol
```
File: ERC721Enumerable.sol
55:        return interfaceId == type(IERC721Enumerable).interfaceId || super.supportsInterface(interfaceId);
63:        return _ownedTokens[owner][index];
70:        return _allTokens.length;
78:        return _allTokens[index];
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
80:        return safe96(balanceOf(delegator), 'ERC721Checkpointable::votesToDelegate: amount exceeds 96 bits');
90:         return current == address(0) ? delegator : current;
114:        return _delegate(msg.sender, delegatee);
143:        return _delegate(signatory, delegatee);
168:        return 0;
173:        return checkpoints[account][nCheckpoints - 1].votes;
178:        return 0;
194:        return checkpoints[account][lower].votes;
255:        return uint32(n);
260:        return uint96(n);
279:        return a - b;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol
```
File: NounsDAOProxy.sol
121:                return(free_mem_ptr, returndatasize())
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol
```
File: NounsDAOLogicV1.sol
403:        return (p.targets, p.values, p.signatures, p.calldatas);
413:        return proposals[proposalId].receipts[voter];
661:        return bps2Uint(proposalThresholdBPS, nouns.totalSupply());
669:        return bps2Uint(quorumVotesBPS, nouns.totalSupply());
673:        return (number * bps) / 10000;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
424:        return _proposals[proposalId].receipts[voter];
868:        return proposal.startBlock - votingDelay;
912:        return bps2Uint(quorumBPS, totalSupply);
936:        return quorumParamsCheckpoints[len - 1].params;
996:        return bps2Uint(getDynamicQuorumParamsAt(block.number).minQuorumVotesBPS, nouns.totalSupply());
1003:       return bps2Uint(getDynamicQuorumParamsAt(block.number).maxQuorumVotesBPS, nouns.totalSupply());
1007:       return (number * bps) / 10000;
1020:       return uint32(n);
1027:       return uint16(n);
```

## 15. Multiplication/division by two should use bit shifting

`\<x> * 2` is equivalent to `\<x> << 1` and `\<x> / 2` is the same as `\<x> >> 1`. The MUL and DIV opcodes cost 5 gas, whereas SHL and SHR only cost 3 gas.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol
```
File: ERC721Checkpointable.sol
184:            uint32 center = upper - (upper - lower) / 2; // ceil, avoiding overflow
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
```
File: NounsDAOLogicV2.sol
951:            uint256 center = upper - (upper - lower) / 2;
```