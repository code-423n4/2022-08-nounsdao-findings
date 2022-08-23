**NounsDAOLogicV2**
- L133/135/136/139/143/147/199/205/207/208/215/219/288/314/326/347/353/377/577/593/594/597/622/625/641/655/659/674/680/684/702/707/711/727/801/819/923
- When a message has a size greater than 32 bytes, it generates a higher expense if it has 32 characters or less.

- L133/135/136/139/143/147/199/205/207/208/215/219/288/314/326/347/353/377/577/593/594/597/625/641/655/659/674/680/684/702/707/711/727/801/819/923
- The require and the modifier can generate much less gas cost if you use if and custom errors and also private view functions.

- L226/292/330/357/382/935/936/949/967/968 - Instead of "variable + 1" or "variable++", much more gas is saved by doing: ++variable.

- L231/238/239/240/241/242/243/292/330/357/382/948 - It is not necessary to create a variable and set it to its default value, this generates an extra gas expense without providing greater understanding.

- L541/967 - Less gas cost is generated, if instead of validating "variable > 0", "variable != 0" is validated, this also does not generate a reduction in its understanding.

- L202/203/204/207/208/292/330/357/382 - When the length of an array is used multiple times, for example: in for loops, it is less expensive if a variable is created in memory of the function.

- L597 - The validation receipt.hasVoted == false can generate less gas cost if !receipt.hasVoted is done.

- L823/826 - If you have the same value in memory, with msg.sender and in storage, it is preferable to use the one in memory since it generates less gas cost.

- L909/910/911/912 - When a variable is used only once, depending on how necessary it is to simplify its understanding, its variable in memory could be ignored.

- L627/628/630/643/644/646/661/662/664/687/688/692/714/715/719/730/731/735/804/807/810/822/823/826/831 /832 - It is possible to save the creation of a variable in memory if when we set a variable and we want to send an event showing the new and previous value before setting it without creating a variable.
Ejemplo: 
	require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');
        require(newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,'NounsDAO::_setVotingDelay: invalid voting delay');

        emit VotingDelaySet(votingDelay, newVotingDelay);

        votingDelay = newVotingDelay;


**NounsDAOLogicV1**
- L122/124/125/128/132/136/140/189/195/197/198/205/209/277/303/315/336/342/366/485/501/502/505/530/533/549/563/567/581/584/617 - When a message has a size greater than 32 bytes, it generates a higher expense if it has 32 characters or less.

- L216/281/319/346/371 - Instead of "variable + 1" or "variable++", much more gas is saved by doing: ++variable.

- L223/230/231/232/233/234/235/281/319/346/371 - It is not necessary to create a variable and set it to its default value, this generates an extra gas expense without providing greater understanding.

- L281/319/346/371 - When the length of an array is used multiple times, for example: in for loops, it is less expensive if a variable is created in the function's memory.

- L505 - The validation receipt.hasVoted == false can generate less gas cost if !receipt.hasVoted is done.

- L535/536/538/551/552/554/569/570/572/586/587/589/602/605/608/620/621/624/629/630 - You can save the creation of a variable in memory if when we set a variable and we want to send an event showing the new and previous value before setting it without creating a variable.
Ejemplo: 
	require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

	emit NewImplementation(implementation, implementation_); 
        implementation = implementation_;


**NounsDAOProxy**
- L82/83/85 - It is possible to save the creation of a variable in memory if when we set a variable and we want to send an event showing the new and previous value before setting it without creating a variable.
Ejemplo: 
	require(msg.sender == admin, 'NounsDAOProxy::_setImplementation: admin only');
        require(implementation_ != address(0), 'NounsDAOProxy::_setImplementation: invalid implementation address');

	emit NewImplementation(implementation, implementation_); 
        implementation = implementation_;



**ERC721Enumerable**
- L62/77 - When a message has a size greater than 32 bytes, it generates a higher expense if it has 32 characters or less.


**ERC721Checkpointable**
- L41/181 - It is not necessary to create a variable and set it to its default value, this generates an extra gas expense without providing greater understanding.

- L140/141/142/164 - When a message has a size greater than 32 bytes, it generates a higher expense if it has 32 characters or less.

- L141/153/172/173/182/191/243/244/247 - Instead of "variable + 1" or "variable - 1" or "variable++", much more gas is saved by doing: ++variable.

- L153/215/218/225/243 - Less gas cost is generated, if instead of validating "variable > 0", "variable != 0" is validated, this also does not generate a reduction in its understanding.
