# 2022-08-NOUNSDAO
## Low Risk and Non-Critical Issues

### `public` functions not called by the contract should be declared `external` instead

_There are **13** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

163:  function getPriorVotes(address account, uint256 blockNumber) public view returns (uint96) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/base/ERC721Enumerable.sol

61:   function tokenOfOwnerByIndex(address owner, uint256 index) public view virtual override returns (uint256) {

76:   function tokenByIndex(uint256 index) public view virtual override returns (uint256) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

113:  function initialize(

174:  function propose(

649:  function _burnVetoPower() public {

660:  function proposalThreshold() public view returns (uint256) {

668:  function quorumVotes() public view returns (uint256) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

124:  function initialize(

184:  function propose(

851:  function _burnVetoPower() public {

862:  function proposalThreshold() public view returns (uint256) {

1002: function maxQuorumVotes() public view returns (uint256) {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Empty `receive()`/`fallback()` functions

_There are **3** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

1030: receive() external payable {}
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

130:  fallback() external payable {

138:  receive() external payable {
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Non-library/interface files should use fixed compiler versions, not floating ones

_There are **6** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

35:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/base/ERC721Enumerable.sol

28:   pragma solidity ^0.8.0;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Enumerable.sol

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

33:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

61:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

53:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

```solidity
File: contracts/governance/NounsDAOProxy.sol

36:   pragma solidity ^0.8.6;
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol

### Event is missing `indexed` fields
Each event that has 3+ fields should have exactly 3 of them indexed

_There are **3** instances of this issue:_

```solidity
File: contracts/base/ERC721Checkpointable.sol

event      DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance)
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol

```solidity
File: contracts/governance/NounsDAOInterfaces.sol

event      VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason)

event      RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent)
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOInterfaces.sol

### Redundant check - `msg.sender != address(0)`

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

819:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819

### Duplicate if-checks and require statements should be made into modifier

_There are **22** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV1.sol

123:  require(msg.sender == admin, 'NounsDAO::initialize: admin only');

365:  require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

530:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

546:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

563:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

581:  require(msg.sender == admin, 'NounsDAO::_setQuorumVotesBPS: admin only');

599:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

617:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

638:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

651:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

134:  require(msg.sender == admin, 'NounsDAO::initialize: admin only');

376:  require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');

622:  require(msg.sender == admin, 'NounsDAO::_setVotingDelay: admin only');

638:  require(msg.sender == admin, 'NounsDAO::_setVotingPeriod: admin only');

655:  require(msg.sender == admin, 'NounsDAO::_setProposalThresholdBPS: admin only');

674:  require(msg.sender == admin, 'NounsDAO::_setMinQuorumVotesBPS: admin only');

702:  require(msg.sender == admin, 'NounsDAO::_setMaxQuorumVotesBPS: admin only');

727:  require(msg.sender == admin, 'NounsDAO::_setQuorumCoefficient: admin only');

801:  require(msg.sender == admin, 'NounsDAO::_setPendingAdmin: admin only');

819:  require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');

840:  require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

853:  require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');
```
https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol

### Redundant safe16 and safe32, there are built-in overflow check

_There are **2** instances of this issue:_

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

1018: function safe32(uint256 n, string memory errorMessage) internal pure returns (uint32) {
1019:     require(n <= type(uint32).max, errorMessage);
1020:     return uint32(n);
1021: }

1023: function safe16(uint256 n) internal pure returns (uint16) {
1024:     if (n > type(uint16).max) {
1025:         revert UnsafeUint16Cast();
1026:     }
1027:     return uint16(n);
1028: }
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1018

### Use an enum instead of 0, 1 and 2 for `support`
Improve readability

```solidity
File: contracts/governance/NounsDAOLogicV2.sol

520:      uint8 support,

535:      uint8 support,

554:      uint8 support,

566:      uint8 support,

591:      uint8 support

602:  if (support == 0) {
603:      proposal.againstVotes = proposal.againstVotes + votes;
604:  } else if (support == 1) {
605:      proposal.forVotes = proposal.forVotes + votes;
606:  } else if (support == 2) {
607:      proposal.abstainVotes = proposal.abstainVotes + votes;
608:  }
```
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol
