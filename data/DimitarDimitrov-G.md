# NounsDAOLogicV2
There are 8 checks: `msg.sender == admin`. We can create modifier `isAdmin()`


    modifier isAdmin() {
      if(msg.sender == admin) {
       revert AdminOnly()
      }
      _;
    } 

Add this modifier to: 
initialize()
_setVotingDelay()
_setVotingPeriod()
_setProposalThresholdBPS()
_setMinQuorumVotesBPS()
_setMaxQuorumVotesBPS()
_setQuorumCoefficient()
_setPendingAdmin()

Replace if check in `_withdraw` with new isAdmin modifier.

---------

There are many places in the file where require() is used. For optimization use if with revert (custom errors)

---------

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L596

We can store this in memory.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L610-L613

And here add this line of code: `proposal.receipts[voter] = receipt`

-------
Same issue here: 
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L290

-------

Use calldata memory location at function params for strings.

# NounsDAOLogicV1

There are 8 checks: `msg.sender == admin`. We can create modifier `isAdmin()`


    modifier isAdmin() {
      if(msg.sender == admin) {
       revert AdminOnly()
      }
      _;
    } 

---------

There are many places in the file where require() is used. For optimization use if with revert (custom errors).

-------

Use calldata memory location at function params for strings.


# NounsDAOProxy & NounsDAOProxyV2

There are many places in the files where require() is used. For optimization use if with revert (custom errors).

# ERC721Checkpointable

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L197-L208

move `emit DelegateChanged(delegator, currentDelegate, delegatee);` to bottom of method.

