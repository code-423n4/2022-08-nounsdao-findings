-> REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

USE CUSTOM ERRORS RATHER THAN REVERT()/REQUIRE() STRINGS TO SAVE GAS

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(address(timelock)%20%3D%3D%20address(0)%2C%20%27NounsDAO%3A%3Ainitialize%3A%20can%20only%20initialize%20once%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3Ainitialize%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(timelock_%20!%3D%20address(0)%2C%20%27NounsDAO%3A%3Ainitialize%3A%20invalid%20timelock%20address%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(nouns_%20!%3D%20address(0)%2C%20%27NounsDAO%3A%3Ainitialize%3A%20invalid%20nouns%20address%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=proposalThresholdBPS%2C%20temp.totalSupply)%3B-,require(,%27NounsDAO%3A%3Apropose%3A%20proposer%20votes%20below%20proposal%20threshold%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=below%20proposal%20threshold%27-,)%3B,)%3B,-require(targets.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(targets.length%20!%3D%200%2C%20%27NounsDAO%3A%3Apropose%3A%20must%20provide%20actions%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(targets.length%20%3C%3D%20proposalMaxOperations%2C%20%27NounsDAO%3A%3Apropose%3A%20too%20many%20actions%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=(temp.latestProposalId)%3B-,require(,)%3B,-require(
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=already%20active%20proposal%27-,)%3B,)%3B,-%7D
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=queue(uint256%20proposalId)%20external%20%7B-,require(,)%3B,-Proposal%20storage%20proposal%20%3D%20_proposals%5BproposalId%5D%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=)%20internal%20%7B-,require(,%27NounsDAO%3A%3AqueueOrRevertInternal%3A%20identical%20proposal%20action%20already%20queued%20at%20eta%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=execute(uint256%20proposalId)%20external%20%7B-,require(,NounsDAO%3A%3Aexecute%3A%20proposal%20can%20only%20be%20executed%20if%20it%20is%20queued%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(state(proposalId)%20!%3D%20ProposalState.Executed%2C%20%27NounsDAO%3A%3Acancel%3A%20cannot%20cancel%20executed%20proposal%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(state(proposalId)%20!%3D%20ProposalState.Executed%2C%20%27NounsDAO%3A%3Aveto%3A%20cannot%20veto%20executed%20proposal%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=require(proposalCount%20%3E%3D%20proposalId%2C%20%27NounsDAO%3A%3Astate%3A%20invalid%20proposal%20id%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(signatory%20!%3D%20address(0)%2C%20%27NounsDAO%3A%3AcastVoteBySig%3A%20invalid%20signature%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(state(proposalId)%20%3D%3D%20ProposalState.Active%2C%20%27NounsDAO%3A%3AcastVoteInternal%3A%20voting%20is%20closed%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(support%20%3C%3D%202%2C%20%27NounsDAO%3A%3AcastVoteInternal%3A%20invalid%20vote%20type%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(receipt.hasVoted%20%3D%3D%20false%2C%20%27NounsDAO%3A%3AcastVoteInternal%3A%20voter%20already%20voted%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setVotingDelay%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setVotingPeriod%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setProposalThresholdBPS%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setMinQuorumVotesBPS%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=quorum%20votes%20bps%27-,)%3B,%27NounsDAO%3A%3A_setMinQuorumVotesBPS%3A%20min%20quorum%20votes%20bps%20greater%20than%20max%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L687:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setMaxQuorumVotesBPS%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L687:~:text=max%20quorum%20votes%20bps%27-,)%3B,)%3B,-uint16%20oldMaxQuorumVotesBPS%20%3D%20params
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L687:~:text=require(msg.sender%20%3D%3D%20admin%2C%20%27NounsDAO%3A%3A_setQuorumCoefficient%3A%20admin%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L750:~:text=require(msg.sender%20%3D%3D%20vetoer%2C%20%27NounsDAO%3A%3A_burnVetoPower%3A%20vetoer%20only%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#:~:text=require(implementation_%20!%3D%20address(0)%2C%20%27NounsDAOProxy%3A%3A_setImplementation%3A%20invalid%20implementation%20address%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=require(signatory%20!%3D,signature%20expired%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=require(blockNumber%20%3C%20block.number%2C%20%27ERC721Checkpointable%3A%3AgetPriorVotes%3A%20not%20yet%20determined%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#:~:text=require(index%20%3C%20ERC721.balanceOf(owner)%2C%20%27ERC721Enumerable%3A%20owner%20index%20out%20of%20bounds%27)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#:~:text=require(index%20%3C%20ERC721Enumerable.totalSupply()%2C%20%27ERC721Enumerable%3A%20global%20index%20out%20of%20bounds%27)%3B


->SPLITTING REQUIRE() STATEMENTS THAT USE && SAVES GAS

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=nouns%20address%27)%3B-,require(,%27NounsDAO%3A%3Ainitialize%3A%20invalid%20voting%20period%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=invalid%20voting%20period%27-,)%3B,%27NounsDAO%3A%3Ainitialize%3A%20invalid%20voting%20delay%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=invalid%20voting%20delay%27-,)%3B,%27NounsDAO%3A%3Ainitialize%3A%20invalid%20proposal%20threshold%20bps%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=below%20proposal%20threshold%27-,)%3B,)%3B,-require(targets.
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=_setVotingDelay%3A%20admin%20only%27)%3B-,require(,)%3B,-uint256%20oldVotingDelay%20%3D%20votingDelay
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=_setVotingPeriod%3A%20admin%20only%27)%3B-,require(,)%3B,-uint256%20oldVotingPeriod%20%3D%20votingPeriod
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=_setProposalThresholdBPS%3A%20admin%20only%27)%3B-,require(,)%3B,-uint256%20oldProposalThresholdBPS%20%3D%20proposalThresholdBPS
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L568:~:text=.number)%3B-,require(,%27NounsDAO%3A%3A_setMinQuorumVotesBPS%3A%20invalid%20min%20quorum%20votes%20bps%27,-)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L750:~:text=require(msg.sender%20%3D%3D%20pendingAdmin%20%26%26%20msg.sender%20!%3D%20address(0)%2C%20%27NounsDAO%3A%3A_acceptAdmin%3A%20pending%20admin%20only%27)%3B

-> ++i costs less gas compared to i++ or i += 1 (Also --i costs less gas compared to i--- or i -= 1)

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=targets.length%3B-,i%2B%2B)%20%7B,-queueOrRevertInternal(
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=targets.length%3B-,i%2B%2B)%20%7B,-timelock.executeTransaction
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=vetoed%20%3D%20true%3B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20proposal.targets.length%3B%20i%2B%2B)%20%7B,-timelock.cancelTransaction(
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#:~:text=startBlock%20%2B%20votingPeriod%3B-,proposalCount%2B%2B%3B,-Proposal%20storage%20newProposal
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=(nonce%20%3D%3D-,nonces%5Bsignatory%5D%2B%2B,-%2C%20%27ERC721Checkpointable%3A%3AdelegateBySig


-> USAGE OF UINTS/INTS SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L520
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L535
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L554
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L566
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L567
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L591
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L687
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L749
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L750
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1024
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1027
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=uint256%20proposalId%2C-,uint8,-support%2C%20uint256
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20MinQuorumVotesBPSSet(-,uint16,-oldMinQuorumVotesBPS%2C%20uint16
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=uint16%20oldMinQuorumVotesBPS%2C-,uint16,-newMinQuorumVotesBPS)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=event%20MaxQuorumVotesBPSSet(-,uint16,-oldMaxQuorumVotesBPS%2C%20uint16
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#:~:text=uint16%20oldMaxQuorumVotesBPS%2C-,uint16,-newMaxQuorumVotesBPS)%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L218
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOInterfaces.sol#L322
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322:~:text=vote%20to%20succeed.-,uint16,-minQuorumVotesBPS%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322:~:text=vote%20to%20succeed.-,uint16,-maxQuorumVotesBPS%3B
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=governance%20platforms%20easier-,uint8,-public%20constant%20decimals
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#:~:text=uint256%20expiry%2C-,uint8%20v%2C,-bytes32%20r%2C


->IT COSTS MORE GAS TO INITIALIZE NON-CONSTANT/NON-IMMUTABLE VARIABLES TO ZERO THAN TO LET THE DEFAULT OF ZERO BE APPLIED

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=timelock.delay()%3B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20proposal.targets.length%3B%20i%2B%2B)%20%7B,-queueOrRevertInternal(
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=%3D%20true%3B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20proposal.targets.length%3B%20i%2B%2B)%20%7B,-timelock.executeTransaction
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=canceled%20%3D%20true%3B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20proposal.targets.length%3B%20i%2B%2B)%20%7B,-timelock.cancelTransaction(
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#:~:text=vetoed%20%3D%20true%3B-,for%20(uint256%20i%20%3D%200%3B%20i%20%3C%20proposal.targets.length%3B%20i%2B%2B)%20%7B,-timelock.cancelTransaction(

