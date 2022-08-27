## no check on eao or if call is success because  it can fail selient
```
  (bool refundSent, ) = msg.sender.call{ value: refundAmount }('');
 also :
       (bool sent, ) = msg.sender.call{ value: amount }('');
```
make it 
```
require (refundSent);
```
governance/NounsDAOLogicV2.sol:789:   
governance/NounsDAOLogicV2.sol:983:   
## require statement cant be zero because msg.sender cant be  address zero 
```
      // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == pendingAdmin && msg.sender != address(0), 'NounsDAO::_acceptAdmin: pending admin only');
```
governance/NounsDAOLogicV2.sol:819: 
## there is few cenerlation issues 
because with conditions this can be higher and lower and higher it gets the harder it is for an account to get int causing a centerlazation issue and the lower the easy it is for accounts to spam proposals

```

        newProposal.proposalThreshold = temp.proposalThreshold;
```
for all the varibles including admin which can be changed can be compormised by actors who are compormised 
## block.timestmap can overflow and cause dos for a short period of time
```
governance/NounsDAOLogicV2.sol:291:        uint256 eta = block.timestamp + timelock.delay();

```
##  there is no init (0) proposal that is has to greater then just best practice its in compound contract
 there is no init (0) proposal that is has to greater then just best practice
```
        Proposal storage proposal = proposals[proposalId];
```
## if targets is alot of addresss the function will dos and you can queues
```
        governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
## there is no check for this comment above and a compormised vetoer can cause harm to a protocol like frontrunning approvals for the vetoer  own benefit

```
governance/NounsDAOLogicV2.sol:851:    function _burnVetoPower() public {

   function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
```

##  it can be overflow on very exterme cases 
it can be a overflow because its uint96 not using safemath which is possible for overflow and cause a revert with the contract under alot of stress


```
        if (support == 0) {
            proposal.againstVotes = proposal.againstVotes + votes;
        } else if (support == 1) {
```
even in v2 after a long buying spree and lets say there are alot of nfts which makes for alot of votes so it will overflow
the reason its low is because its very unrealistic for that many nfts  minted/bought.
##########
