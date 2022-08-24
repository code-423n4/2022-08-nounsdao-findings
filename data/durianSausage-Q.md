## low risk foundings
### L01: UNUSED/EMPTY RECEIVE()/FALLBACK() FUNCTION
#### problem
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert
#### prof
governance/NounsDAOLogicV2.sol, 1030, b'    receive() external payable {}'


## none-critical issue foundings
### N01: EVENT IS MISSING INDEXED FIELDS
#### prof
governance/NounsDAOInterfaces.sol, 47, b'    event ProposalCreated(\n        uint256 id,\n        address proposer,\n        address[] targets,\n        uint256[] values,\n        string[] signatures,\n        bytes[] calldatas,\n        uint256 startBlock,\n        uint256 endBlock,\n        string description\n    );'
governance/NounsDAOInterfaces.sol, 62, b'    event ProposalCreatedWithRequirements(\n        uint256 id,\n        address proposer,\n        address[] targets,\n        uint256[] values,\n        string[] signatures,\n        bytes[] calldatas,\n        uint256 startBlock,\n        uint256 endBlock,\n        uint256 proposalThreshold,\n        uint256 quorumVotes,\n        string description\n    );'
governance/NounsDAOInterfaces.sol, 73, b'    event ProposalCanceled(uint256 id);'
governance/NounsDAOInterfaces.sol, 76, b'    event ProposalQueued(uint256 id, uint256 eta);'
governance/NounsDAOInterfaces.sol, 79, b'    event ProposalExecuted(uint256 id);'
governance/NounsDAOInterfaces.sol, 82, b'    event ProposalVetoed(uint256 id);'
governance/NounsDAOInterfaces.sol, 85, b'    event VotingDelaySet(uint256 oldVotingDelay, uint256 newVotingDelay);'
governance/NounsDAOInterfaces.sol, 88, b'    event VotingPeriodSet(uint256 oldVotingPeriod, uint256 newVotingPeriod);'
governance/NounsDAOInterfaces.sol, 91, b'    event NewImplementation(address oldImplementation, address newImplementation);'
governance/NounsDAOInterfaces.sol, 94, b'    event ProposalThresholdBPSSet(uint256 oldProposalThresholdBPS, uint256 newProposalThresholdBPS);'
governance/NounsDAOInterfaces.sol, 97, b'    event QuorumVotesBPSSet(uint256 oldQuorumVotesBPS, uint256 newQuorumVotesBPS);'
governance/NounsDAOInterfaces.sol, 100, b'    event NewPendingAdmin(address oldPendingAdmin, address newPendingAdmin);'
governance/NounsDAOInterfaces.sol, 103, b'    event NewAdmin(address oldAdmin, address newAdmin);'
governance/NounsDAOInterfaces.sol, 106, b'    event NewVetoer(address oldVetoer, address newVetoer);'
governance/NounsDAOInterfaces.sol, 111, b'    event MinQuorumVotesBPSSet(uint16 oldMinQuorumVotesBPS, uint16 newMinQuorumVotesBPS);'
governance/NounsDAOInterfaces.sol, 114, b'    event MaxQuorumVotesBPSSet(uint16 oldMaxQuorumVotesBPS, uint16 newMaxQuorumVotesBPS);'
governance/NounsDAOInterfaces.sol, 117, b'    event QuorumCoefficientSet(uint32 oldQuorumCoefficient, uint32 newQuorumCoefficient);'
governance/NounsDAOInterfaces.sol, 123, b'    event Withdraw(uint256 amount, bool sent);'



### N02: INCONSISTENT VERSION OF ENGLISH BEING USED
#### problem
Some functions use American English, whereas others use British English. A single project should use only one of the two
#### prof
base/ERC721Checkpointable.sol, 35, b'pragma solidity ^0.8.6;'
base/ERC721Enumerable.sol, 28, b'pragma solidity ^0.8.0;'
governance/NounsDAOInterfaces.sol, 33, b'pragma solidity ^0.8.6;'
governance/NounsDAOLogicV1.sol, 61, b'pragma solidity ^0.8.6;'
governance/NounsDAOLogicV2.sol, 53, b'pragma solidity ^0.8.6;'
governance/NounsDAOProxy.sol, 36, b'pragma solidity ^0.8.6;'

### N03: external function naming should not have "_"
#### prof
NounsDAOLogicV1.sol,_burnVetoPower,_setVetoer,_acceptAdmin,_setPendingAdmin