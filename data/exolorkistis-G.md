[1] SPLITTING ``REQUIRE()`` STATEMENTS THAT USE ``&&`` SAVES GAS
See [this issue](https://github.com/code-423n4/2022-01-xdefi-findings/issues/128) which describes the fact that there is a larger deployment gas cost, but with enough runtime calls, the change ends up being cheaper.
*There are 19 instances of this issue:*
```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #1

126:            require(
127:            votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
128:           'NounsDAO::initialize: invalid voting period'
129:            );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L129](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L126-L129)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #2

130:           require(
131:           votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
132:          'NounsDAO::initialize: invalid voting delay'
133:            );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130-L133](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L130-L133)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #3

134:          require(
135:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
136:           'NounsDAO::initialize: invalid proposal threshold'
137:        );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134-L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L134-L137)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #4

138:          require(
139:           quorumVotesBPS_ >= MIN_QUORUM_VOTES_BPS && quorumVotesBPS_ <= MAX_QUORUM_VOTES_BPS,
140:            'NounsDAO::initialize: invalid proposal threshold'
141:            );
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138-L141](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L138-L141)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #5

191:          require(
192:            targets.length == values.length &&
193:                targets.length == signatures.length &&
194:               targets.length == calldatas.length,
195:          'NounsDAO::propose: proposal function information arity mismatch'
196:          );
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191-L196](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L191-L196)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #6

531:          require(
532:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
533:            'NounsDAO::_setVotingDelay: invalid voting delay'
534:            );
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531-L534](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L531-L534)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #7

547:          require(
548:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
549:            'NounsDAO::_setVotingPeriod: invalid voting period'
550:        );
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547-L550](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L547-L550)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #8

564:          require(
565:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
566:                newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
567:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
568:        );
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564-L568](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L564-L568)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #9

582:          require(
583:            newQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS && newQuorumVotesBPS <= MAX_QUORUM_VOTES_BPS,
584:            'NounsDAO::_setProposalThreshold: invalid proposal threshold'
585:            );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582-L585](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L582-L585)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol  #10

617:          require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)


```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #11

137:         require(
138:           votingPeriod_ >= MIN_VOTING_PERIOD && votingPeriod_ <= MAX_VOTING_PERIOD,
139:           'NounsDAO::initialize: invalid voting period'
140:       );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137-L140](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L137-L140)
```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #12

141:        require(
142:            votingDelay_ >= MIN_VOTING_DELAY && votingDelay_ <= MAX_VOTING_DELAY,
143:           'NounsDAO::initialize: invalid voting delay'
144:       );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141-L144](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L141-L144)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #13

145:       require(
146:            proposalThresholdBPS_ >= MIN_PROPOSAL_THRESHOLD_BPS && proposalThresholdBPS_ <= MAX_PROPOSAL_THRESHOLD_BPS,
147:           'NounsDAO::initialize: invalid proposal threshold bps'
148:       );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145-L148](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L145-L148)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #14

201:      require(
202:            targets.length == values.length &&
203:                targets.length == signatures.length &&
204:                targets.length == calldatas.length,
205:            'NounsDAO::propose: proposal function information arity mismatch'
206:       );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201-L206](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L201-L206)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #15

623:     require(
624:            newVotingDelay >= MIN_VOTING_DELAY && newVotingDelay <= MAX_VOTING_DELAY,
625:            'NounsDAO::_setVotingDelay: invalid voting delay'
626:        );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623-L626](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L623-L626)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #16

639:     require(
640:            newVotingPeriod >= MIN_VOTING_PERIOD && newVotingPeriod <= MAX_VOTING_PERIOD,
641:            'NounsDAO::_setVotingPeriod: invalid voting period'
642:        );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639-L642](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L639-L642)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #17

656:    require(
657:            newProposalThresholdBPS >= MIN_PROPOSAL_THRESHOLD_BPS &&
658:               newProposalThresholdBPS <= MAX_PROPOSAL_THRESHOLD_BPS,
659:            'NounsDAO::_setProposalThreshold: invalid proposal threshold bps'
660:        );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656-L660](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L656-L660)


```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #18

677:    require(
678:            newMinQuorumVotesBPS >= MIN_QUORUM_VOTES_BPS_LOWER_BOUND &&
679:                newMinQuorumVotesBPS <= MIN_QUORUM_VOTES_BPS_UPPER_BOUND,
680:            'NounsDAO::_setMinQuorumVotesBPS: invalid min quorum votes bps'
681:        );

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677-L681](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L677-L681)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol  #19

819:    require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)

[2] ``<ARRAY>.LENGTH`` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A`` FOR``-LOOP

The overheads outlined below are *PER LOOP*, excluding the first loop

-storage arrays incur a Gwarmaccess (100 gas)
-memory arrays use MLOAD (3 gas)
-calldata arrays use CALLDATALOAD (3 gas)
Caching the length changes each of these to a DUP<N> (3 gas), and gets rid of the extra DUP<N> needed to store the stack offset.

*There are 8 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #1

281: for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #2

319: for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #3

346:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #4

371:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #5

292:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #6

330:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #7

357:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #8

382:  for (uint256 i = 0; i < proposal.targets.length; i++) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)

[3] ``ABI.ENCODE()`` IS LESS EFFICIENT THAN ``ABI.ENCODEPACKED()``

*There are 8 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #1

302:  !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L302](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L302)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #2

480:   abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L480](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L480)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol #3

482:   bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L482](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L482)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #4

313:  !timelock.queuedTransactions(keccak256(abi.encode(target, value, signature, data, eta))),

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L313](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L313)


```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #5

572:  abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L572](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L572)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol #6

574:  bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L574](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L574)

```
File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

135:       abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name())), getChainId(), address(this))

137:       bytes32 structHash = keccak256(abi.encode(DELEGATION_TYPEHASH, delegatee, nonce, expiry));

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L135](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L135)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L137](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L137)

[4] USING  ``PRIVATE`` RATHER THAN ``PUBLIC`` FOR CONSTANTS, SAVES GAS

If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata, and not adding another entry to the method ID table

*There are 31 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

67: string public constant name = 'Nouns DAO';       #1

    
70:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1; // 1 basis point or 0.01%        #2

    
73:    uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000; // 1,000 basis points or 10%       #3

    
76:    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours                                       #4

    
79:     uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks                                    #5

    
82:    uint256 public constant MIN_VOTING_DELAY = 1;                                                                             #6

    
85:    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week                                        #7

    
88:     uint256 public constant MIN_QUORUM_VOTES_BPS = 200; // 200 basis points or 2%                      #8

    
91:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000; // 2,000 basis points or 20%              #9

    
94:    uint256 public constant proposalMaxOperations = 10; // 10 actions                                                  #10

    
97:    bytes32 public constant DOMAIN_TYPEHASH =keccak256('EIP712Domain(string name,uint256 chainId,address verifyingContract)');    #11                                                                         

   
101:    bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');      #12

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67-L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67-L101)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

59:    string public constant name = 'Nouns DAO';                #13

62:    uint256 public constant MIN_PROPOSAL_THRESHOLD_BPS = 1;               #14

65:   uint256 public constant MAX_PROPOSAL_THRESHOLD_BPS = 1_000;          #15

68:   uint256 public constant MIN_VOTING_PERIOD = 5_760;                               #16

71:    uint256 public constant MAX_VOTING_PERIOD = 80_640;                            #17

74:    uint256 public constant MIN_VOTING_DELAY = 1;                                       #18

77:    uint256 public constant MAX_VOTING_DELAY = 40_320;                            #19

80:    uint256 public constant MIN_QUORUM_VOTES_BPS_LOWER_BOUND = 200;            #20

83:    uint256 public constant MIN_QUORUM_VOTES_BPS_UPPER_BOUND = 2_000;           #21

86:    uint256 public constant MAX_QUORUM_VOTES_BPS_UPPER_BOUND = 6_000;            #22

89:    uint256 public constant MAX_QUORUM_VOTES_BPS = 2_000;                                       #23

92:    uint256 public constant proposalMaxOperations = 10;                                                  #24

95:     uint256 public constant MAX_REFUND_PRIORITY_FEE = 2 gwei;                                  #25

98:     uint256 public constant REFUND_BASE_GAS = 36000;                                                 #26

101:     bytes32 public constant DOMAIN_TYPEHASH =                                                          #27

105:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');           #28

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L59-L105](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L59-L105)

```
File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

41:    uint8 public constant decimals = 0;  #29

59:    bytes32 public constant DOMAIN_TYPEHASH =    #30

63:     bytes32 public constant DELEGATION_TYPEHASH =     #31

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L63](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L63)



[5] DUPLICATED ``REQUIRE()``/``REVERT()`` CHECKS SHOULD BE REFACTORED TO A MODIFIER OR FUNCTION

Saves deployment cost

*There are 6 instances of this issue:*

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

336:    require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L336](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L336)

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

365:    require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L365](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L365)

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

123:   require(msg.sender == admin, 'NounsDAO::initialize: admin only');
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L123](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L123)

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

134:   require(msg.sender == admin, 'NounsDAO::initialize: admin only');
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L134](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L134)

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

347:  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L347)

``` 
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

377:  require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L377)


[6] ++I/I++ SHOULD BE UNCHECKED{++I}/UNCHECKED{I++} WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN FOR- AND WHILE-LOOPS
 
The ``unchecked`` keyword is new in solidity 0.8.0,so this only applies to that version or higher, which these instances are. This saves 30-40 gas *[PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)*

*There are 8 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

281:    for (uint256 i = 0; i < proposal.targets.length; i++) {

319:    for (uint256 i = 0; i < proposal.targets.length; i++) {

346:    for (uint256 i = 0; i < proposal.targets.length; i++) {

371:    for (uint256 i = 0; i < proposal.targets.length; i++) {

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L281)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L319)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L346)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L371)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

292:    for (uint256 i = 0; i < proposal.targets.length; i++) {

330:    for (uint256 i = 0; i < proposal.targets.length; i++) {

357:    for (uint256 i = 0; i < proposal.targets.length; i++) {

382:    for (uint256 i = 0; i < proposal.targets.length; i++) {

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L292)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L330)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L357)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L382)

[7]  ``PUBLIC`` FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED ``EXTERNAL`` INSTEAD

Contracts [are allowed](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) to override their parents’ functions and change the visibility from ``external`` to ``public`` and can save gas by doing so.

*There are 10 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

113:  function initialize(
114:       address timelock_,
115:      address nouns_,
116:      address vetoer_,
117:       uint256 votingPeriod_,
118:      uint256 votingDelay_,
119:     uint256 proposalThresholdBPS_,
120:     uint256 quorumVotesBPS_
121:      ) public virtual {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L113-L121](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L113-L121)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

174:  function propose(
175:       address[] memory targets,
176:        uint256[] memory values,
177:        string[] memory signatures,
178:      bytes[] memory calldatas,
179:       string memory description
180:    ) public returns (uint256) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L174-L180](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L174-L180)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol 

649:        function _burnVetoPower() public {
        
```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L649](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L649)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

124:  function initialize(
125:        address timelock_,
126:       address nouns_,
127:      address vetoer_,
128:      uint256 votingPeriod_,
129:     uint256 votingDelay_,
130:    uint256 proposalThresholdBPS_,
131:    DynamicQuorumParams calldata dynamicQuorumParams_
132:    ) public virtual {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L124-L132](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L124-L132)


```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

184:  function propose(
185:        address[] memory targets,
186:       uint256[] memory values,
187:      string[] memory signatures,
188:     bytes[] memory calldatas,
189:    string memory description
190:  ) public returns (uint256) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L184-L190](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L184-L190)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol 

851:        function _burnVetoPower() public {
        
```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851)

```

File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

163:      function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L163)

```
File : 2022-08-nounsdao/contracts/base/ERC721Enumerable.sol 

54:    function supportsInterface(bytes4 interfaceId) public view virtual override(IERC165, ERC721) returns (bool) {

61:      function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {

76:      function tokenByIndex(uint256 index) public view virtual override returns (uint256) {

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L54](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L54)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L61)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L76](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L76)



[8]  DON’T COMPARE BOOLEAN EXPRESSIONS TO BOOLEAN LITERALS

``if (<x> == true)`` => ``if(<x>)`` , ``if (<x> == false)`` => ``if (!<x>)``

*There are 2 instances of this issue:*

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol

505:   require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L505)


```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

597:   require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L597)


[9] USE ASSEMBLY TO CHECK FOR ADDRESS(0) to save gas.

*There are 11 instances of this issue:*

```

File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol

124:   require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

125:   require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

364:   require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

485:   require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

617:   require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124-L125](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L124-L125)


[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L364](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L364)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L485](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L485)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L617)


```

File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

135:   require(timelock_ != address(0), 'NounsDAO::initialize: invalid timelock address');

136:   require(nouns_ != address(0), 'NounsDAO::initialize: invalid nouns address');

375:   require(vetoer != address(0), 'NounsDAO::veto: veto power burned');

577:   require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');

819:   require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135-L136](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L135-L136)


[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L577](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L577)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819)

```
File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

140:    require(signatory != address(0), 'ERC721Checkpointable::delegateBySig: invalid signature');

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L140)



[10]  USAGE OF ``UINTS/INTS`` SMALLER THAN 32 BYTES (256 BITS) INCURS OVERHEAD

When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.

[https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html](https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html) Use a larger size then downcast where needed

*There are 50 instances of this issue*

```
File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

41:      uint8 public constant decimals = 0;

48:      uint32 fromBlock;

49:      uint96 votes;

130:     uint8 v,

152:     uint32 nCheckpoints = numCheckpoints[account];

181:      uint32 lower = 0;

182:       uint32 upper = nCheckpoints - 1;

184:      uint32 center = upper - (upper - lower) / 2;

205:       uint96 amount = votesToDelegate(delegator);

213:       uint96 amount

217:       uint32 srcRepNum = numCheckpoints[srcRep];

218:       uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

219:       uint96 srcRepNew = sub96(srcRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount underflows');

224:       uint32 dstRepNum = numCheckpoints[dstRep];

225:        uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

226:        uint96 dstRepNew = add96(dstRepOld, amount, 'ERC721Checkpointable::_moveDelegates: amount overflows'); 

234:        uint32 nCheckpoints,

235:          uint96 oldVotes,

236:          uint96 newVotes

238:          uint32 blockNumber = safe32(

264:           uint96 a,

265:           uint96 b,

268:           uint96 c = a + b;

274:           uint96 a,

275:           uint96 b,

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L48-L49](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L48-L49)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L130](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L130)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L152](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L152)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181-L182](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L181-L182)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L205](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L205)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L213](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L213)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L217-L219](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L217-L219)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L224-L226](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L224-L226)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L234-L236](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L234-L236)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L238](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L238)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L264-L265](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L264-L265)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L268](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L268)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L274-L275](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L274-L275)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol

101:      bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

450:        function castVote(uint256 proposalId, uint8 support) external {

462:     uint8 support,

474:      uint8 support

475:       uint8 v

499:        uint8 support

508:         uint96 votes = nouns.getPriorVotes(voter, proposal.startBlock - votingDelay);

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L101](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L101)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L450](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L450)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L462](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L462)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L474-L475](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L474-L475)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L499](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L499)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L508](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L508)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

105:     bytes32 public constant BALLOT_TYPEHASH = keccak256('Ballot(uint256 proposalId,uint8 support)');

503:     function castRefundableVote(uint256 proposalId, uint8 support) external {

520:       uint8 support,

535:      uint8 support

539:      uint96 votes = castVoteInternal(msg.sender, proposalId, support);

566:        uint8 support

567:        uint8 v,

591:        uint8 support

592:        internal returns (uint96)

600:       uint96 votes = nouns.getPriorVotes(voter, proposalCreationBlock(proposal));

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L105](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L105)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L503](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L503)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L520](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L520)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L535](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L535)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L539](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L539)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L566-L567](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L566-L567)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L591-L592](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L591-L592)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L600](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L600)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol

218:     uint8 support;

220:    uint96 votes;

322:     uint8 support;

324:    uint96 votes;

352:    uint16 minQuorumVotesBPS;

354:     uint16 maxQuorumVotesBPS;

357:      uint32 quorumCoefficient;

363:     uint32 fromBlock;

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L218](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L218)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L220](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L220)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L322)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L324](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L324)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L352](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L352)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L354](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L354)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L357](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L357)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L363](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L363)


[11]  USING ``STORAGE`` INSTEAD OF ``MEMORY`` FOR STRUCTS/ARRAYS SAVES GAS

When fetching data from a storage location, assigning the data to a ``memory`` variable causes all fields of the struct/array to be read from storage, which incurs a Gcoldsload (2100 gas) for each field of the struct/array. If the fields are read from the new memory variable, they incur an additional ``MLOAD`` rather than a cheap stack read. Instead of declearing the variable with the ``memory`` keyword, declaring the variable with the ``storage`` keyword and caching any fields that need to be re-read in stack variables, will be much cheaper, only incuring the Gcoldsload for the fields actually read. The only time it makes sense to read the whole struct/array into a ``memory`` variable, is if the full struct/array is being returned by the function, is being passed to a function that requires ``memory``, or if the array/struct is being read from another ``memory`` array/struct

*There is 4 instance of this issue:*

```
File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

185:        Checkpoint memory cp = checkpoints[account][center];

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L185](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L185)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol

181:        ProposalTemp memory temp;

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L181](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L181)

```
File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

191:        ProposalTemp memory temp;

952:        DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L191](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L191)

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L952](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L952)


[12]   DIVISION BY TWO SHOULD USE BIT SHIFTING

``<x> / 2`` is the same as ``<x> >>1``. The ``DIV`` opcode costs 5 gas, whereas ``SHR`` only costs 3 gas

*There is 2 instance of this issue:*

```

File : 2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol

184:     uint32 center = upper - (upper - lower) / 2;

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L184)

```

File : 2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol

951:     uint256 center = upper - (upper - lower) / 2;

```
[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L951](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L951)



[13]   INTERNAL FUNCTIONS ONLY CALLED ONCE CAN BE INLINED TO SAVE GAS

Not inlining costs 20 to 40 gas because of two extra ``JUMP`` instructions and additional stack operations needed for function calls.

*There is 1 instance of this issue:*

```

File : 2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol

94:      function delegateTo(address callee, bytes memory data) internal {

```

[https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94)