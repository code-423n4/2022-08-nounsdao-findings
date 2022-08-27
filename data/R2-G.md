1. function "propose"

**Code**:
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L174

**How to reduce gas usage**:
- public -> external
- receive array arguments as 'calldata', not 'memory'
- no need to explicitly set 0 to variables like eta, forVotes, againstVotes, abstainVotes, canceled, executed, vetoed. Their default value already 0


2. functions "cancel" and "veto"

**How to reduce gas usage**:
- no need to call timelock.cancelTransaction if transactions were not sended to timelock yet (you can check status)

