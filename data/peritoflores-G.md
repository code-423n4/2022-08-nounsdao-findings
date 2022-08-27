# Gas-Report for 2022-08-nounsdao



####  Reading msg.sender is  much much cheaper than reading storage variable

Reading `msg.sender` cost 2 gas while reading a storage cost 2100 the first time and 100 the next.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L247


    latestProposalIds[newProposal.proposer] = newProposal.id; @audit gas replace newProposal.proposer for msg.sender

  *Gas saved: 98 per proposal call*



### Unnecessary check

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L606

You don't need to    check `support ==2` .   Above you made the following check

    require(support <= 2, 'NounsDAO::castVoteInternal: invalid vote type'); 

Add a comment instead



###  Use ! instead of  == false



https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L597
