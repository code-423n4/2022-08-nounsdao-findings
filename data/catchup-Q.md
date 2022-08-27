# Low Risk Issues

## 1. ```state()``` function does not check the proposalId to be non-zero

The first created proposal takes index 1 in the _proposals mapping:
~~~
226:         proposalCount++;
227:         Proposal storage newProposal = _proposals[proposalCount];
228:         newProposal.id = proposalCount;
229:         newProposal.proposer = msg.sender;
~~~

Therefore, there is no proposal with proposalId = 0. ```state(uint256 proposalId)``` function checks proposalId to be smaller than or equal to proposalCount, but it does not check if proposalId is non-zero.
~~~
432:     function state(uint256 proposalId) public view returns (ProposalState) {
433:         require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
434:         Proposal storage proposal = _proposals[proposalId];
435:         if (proposal.vetoed) {
436:             return ProposalState.Vetoed;
~~~
Hence, when the ```state()``` function is called with argument 0, it would not revert.

The original GovernorBravoDelegate.sol has such a control:
~~~
    function state(uint proposalId) public view returns (ProposalState) {
        require(proposalCount >= proposalId && proposalId > initialProposalId, "GovernorBravo::state: invalid proposal id");
        Proposal storage proposal = proposals[proposalId];

~~~

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L432-L434
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L226-L228
https://github.com/compound-finance/compound-protocol/blob/b9b14038612d846b83f8a009a82c38974ff2dcfe/contracts/Governance/GovernorBravoDelegate.sol#L194-L195






## 2. Inconsistent limit checks when setting parameters

```minQuorumVotesBPS``` and ```maxQuorumVotesBPS``` can be updated independently through ```_setMinQuorumVotesBPS()``` and ```_setMaxQuorumVotesBPS()``` functions, or they can be updated through ```_setDynamicQuorumParams()``` all at once.
The individual functions accept the new value to be within limits and limits inclusive, whereas ```_setDynamicQuorumParams()``` function excludes the limit values.
~~~
677:         require(
678:             newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                 newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:             'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:         );
682:         require(
683:             newMinQuorumVotesBPS <= params.maxQuorumVotesBPS,
684:             'NounsDAO::_setMinQuorumVotesBPS: min quorum votes bps greater than max'
685:         );
686: 
687:         uint16 oldMinQuorumVotesBPS = params.minQuorumVotesBPS;
~~~
~~~
705:         require(
706:             newMaxQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS_UPPER_BOUND,
707:             'NounsDAO::_setMaxQuorumVotesBPS: invalid max quorum votes bps'
708:         );
709:         require(
710:             params.minQuorumVotesBPS <= newMaxQuorumVotesBPS,
711:             'NounsDAO::_setMaxQuorumVotesBPS: min quorum votes bps greater than max'
712:         );
713: 
~~~
~~~
756:         if (
757:             newMinQuorumVotesBPS < MIN_QUORUM_VOTES_BPS_LOWER_BOUND ||
758:             newMinQuorumVotesBPS > MIN_QUORUM_VOTES_BPS_UPPER_BOUND
759:         ) {
760:             revert InvalidMinQuorumVotesBPS();
761:         }
762:         if (newMaxQuorumVotesBPS > MAX_QUORUM_VOTES_BPS_UPPER_BOUND) {
763:             revert InvalidMaxQuorumVotesBPS();
764:         }
765:         if (newMinQuorumVotesBPS > newMaxQuorumVotesBPS) {
766:             revert MinQuorumBPSGreaterThanMaxQuorumBPS();
767:         }
768: 
~~~


### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L673-L720
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L748-L781




## 3. Missing non-zero address checks when setting important addresses

It is a good practice to include 0 address check when setting an important address. I suggest to include a non-zero address check when setting the vetoer in the initialize().

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L156




# Non-Critical Issues

## 1. Faulty comment

The comment within ```_burnVetoPower()``` is faulty.
~~~
851:     function _burnVetoPower() public {
852:         // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
853:         require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
854: 
855:         _setVetoer(address(0));
~~~

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L852





## 2. Missing indexed fields

Each event can have up to 3 indexed fields.
I believe it would be good to have the ```address proposer``` indexed in the below events:
~~~
36:     /// @notice An event emitted when a new proposal is created
37:     event ProposalCreated(
38:         uint256 id,
39:         address proposer,
40:         address[] targets,
41:         uint256[] values,
42:         string[] signatures,
43:         bytes[] calldatas,
44:         uint256 startBlock,
45:         uint256 endBlock,
46:         string description
47:     );
48: 
49:     /// @notice An event emitted when a new proposal is created, which includes additional information
50:     event ProposalCreatedWithRequirements(
51:         uint256 id,
52:         address proposer,
53:         address[] targets,
54:         uint256[] values,
55:         string[] signatures,
56:         bytes[] calldatas,
57:         uint256 startBlock,
58:         uint256 endBlock,
59:         uint256 proposalThreshold,
60:         uint256 quorumVotes,
61:         string description
62:     );
63: 
~~~

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L39
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L52




## 3. Why burn a privileged role forever

Burning the vetoer role forever through ```_burnVetoPower()``` function doesn't make sense, since this is irreversible.

### Lines of code
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851-L856

