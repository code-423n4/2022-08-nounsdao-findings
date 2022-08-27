## Using `memory` instead of `storage` (80 ~ 100 gas per instance)
using `memory` mark for read or write data in blockchain can be save way a lot of gas instead of using `storage`. according to <a href="https://eips.ethereum.org/EIPS/eip-2929">EIP-2929</a> gas cost for `SLOAD`(read from storage) and `SSTORE`(write into storage) is 100 gas (2600 gas if never accessed before), it's very contrast with `MLOAD` (read from memory) and `MSTORE`(write into memory) which only cost 3 gas. 

SOLUTION:
1. First make new `memory` variable and pass the value from `storage`
2. use the data from new `memory` variable 
3. then pass it to the `storage` 

Example:
The existing code from Nouns DAO
```js
        Proposal storage newProposal = proposals[proposalCount]; //SLOAD 100 gas 
        // for every line below cost 100 gas for SSTORE
        newProposal.id = proposalCount; 
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply);
        newProposal.eta = 0; 
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.forVotes = 0; 
        newProposal.againstVotes = 0; 
        newProposal.abstainVotes = 0; 
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
        //Total 1400 gas + 100 gas = 1500 gas
```

The Cheaper Version 
```js
        Proposal memory newProposal ; //SSTORE 3 gas 
        //for every SSTORE below cost 3 gas 
        newProposal.id = proposalCount;
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.quorumVotes = bps2Uint(quorumVotesBPS, temp.totalSupply);
        newProposal.eta = 0; 
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.forVotes = 0; 
        newProposal.againstVotes = 0;
        newProposal.abstainVotes = 0; 
        newProposal.canceled = false;
        newProposal.executed = false;
        newProposal.vetoed = false;
        // subtotal 3*14 = 42 Gas 
        proposals[proposalCount] = newProposal; //SSTORE 100 Gas & MLOAD 3 gas
        // TOTAL = 3 + 42 + 103 = 148 gas
```

from example above we can decrease cost from 1500 gas to 148 gas by using `memory` instead of `storage` and there's even more in contracts in scope 

NOTE!!:
1.  In out of scope contract there's some instance too but i'm not gonna report it, maybe dev can considering to check again the contract.
2. In some instance which `external view` function will not affected by this gas optimizing but i will still report it with mark `EV`.

INSTANCE:

a. NounsDAOLogicV1.sol
1. NounsDAOLogicV1::propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L217-L237
2. NounsDAOLogicV1::queue()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L279-L290
3. NounsDAOLogicV1::execute()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L317-L327
4. NounsDAOLogicV1::cancel()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L338-L354
5. NounsDAOLogicV1::veto()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L368-L379
6. NounsDAOLogicV1::getActions() (`EV`) 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L402-403
7. NounsDAOLogicV1::state() 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L423-L443
8. NounsDAOLogicV1::castVoteInternal()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L503-L522

b. NounsDAOLogicV2.sol
1. NounsDAOLogicV2::propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L227-L247
2. NounsDAOLogicV2::queue()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L290-L301
3. NounsDAOLogicV2::execute()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L328-L338
4. NounsDAOLogicV2::cancel()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L349-L365
5. NounsDAOLogicV2::veto()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L349-L365
6. NounsDAOLogicV2::getActions() (`EV`)
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L413-L414
7. NounsDAOLogicV2::state()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L423-L442
8. NounsDAOLogicV2::castVoteInternal()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L503-L522

## Avoid using more than 32 bytes strings for `require` 

save strings for `require` can be costly if the string have more than 32 characters (1 char = 1 bytes). Since ethereum storage slot use 32 bytes, strings that use more than 32 characters will use more than 1 storage slot. it will be costly for deploying and operating the contract. 

SOLUTION:
i recommend 2 solutions for optimizing gas cost you can choose one:
1. Use `revert` and custom errors. 
example 
```js
    //defining 
    error OnlyAdmin();

    //useage
    function example() external {
        if (msg.sender != admin) revert OnlyAdmin();
        ... //implementation
    }
```
2. Simply limit the strings to 32 bytes string. 
example
```js
    //existing in NounsDAOLogicV1
    require(address(timelock) == address(0), 'NounsDAO::initialize: can only initialize once') //46 bytes

    //after optimizing 
    require(address(timelock) == address(0), 'NounsDAO::initialize: only once') //31 bytes
```

NOTE: theres several instance that using other function to use `require` but it's still defining string that have  more than 32 bytes  so its can be considered to. (example using `safe32()`)

INSTANCE:
a. NounsDAOLogicV1.sol
1. NounsDAOLogicV1::initialize()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L122-L141
2. NounsDAOLogicV1::propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L187-L211
3. NounsDAOLogicV1::queue()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L275-L278
4. NounsDAOLogicV1::queueOrRevertInternal()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L301-L304
5. NounsDAOLogicV1::execute()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L313-L316
6. NounsDAOLogicV1::cancel()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L336-L343
7. NounsDAOLogicV1::veto()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L364-L366
8. NounsDAOLogicV1::castVoteBySig()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L485
9. NounsDAOLogicV1::castVoteInternal()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L501-L505
10. NounsDAOLogicV1::_setVotingDelay()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L530-L534
11. NounsDAOLogicV1::_setVotingPeriod()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L546-L550
12. NounsDAOLogicV1::_setProposalThresholdBPS()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L563-L568
13. NounsDAOLogicV1::_setQuorumVotesBPS()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L581-L585
14. NounsDAOLogicV1::_setPendingAdmin()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L599
15. NounsDAOLogicV1::_acceptAdmin()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L617
16. NounsDAOLogicV1::_setVetoer()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L638
17. NounsDAOLogicV1::_burnVetoPower()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L651

b. NounsDAOLogicV2.sol
1. NounsDAOLogicV2::initialize()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L133-L148
2. NounsDAOLogicV2::propose()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L197-L221
3. NounsDAOLogicV2::queue()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L286-L289
4. NounsDAOLogicV2::queueOrRevertInternal()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L312-L315
5. NounsDAOLogicV2::execute()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L324-L327
6. NounsDAOLogicV2::cancel()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347-L354
7. NounsDAOLogicV2::veto()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L375-L377
8. NounsDAOLogicV2::castVoteBySig()
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L577
9. NounsDAOLogicV2::castVoteInternal() Line 593 - 597
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L593-L597
10. NounsDAOLogicV2::_setVotingDelay() Line 622 - 626
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L622-L626
11. NounsDAOLogicV2::_setVotingPeriod() Line 638 - 642
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L638-L642
12. NounsDAOLogicV2::_setProposalThresholdBPS() Line 655 - 660
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L655-L660
13. NounsDAOLogicV2::_setMinQuorumVotesBPS() Line 674 - 685
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L674-L685
14. NounsDAOLogicV2::_setMaxQuorumVotesBPS() Line 702 - 712
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L702-L712
15. NounsDAOLogicV2::_setQuorumCoefficient() Line 727
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L727
16. NounsDAOLogicV2::_setPendingAdmin() Line 801
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L801
17. NounsDAOLogicV2::_acceptAdmin() Line 819
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L819
18. NounsDAOLogicV2::_setVetoer() Line 840
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L840
19. NounsDAOLogicV2::_burnVetoPower() Line 853
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L853
20. NounsDAOLogicV2::getDynamicQuorumParamsAt() (NOTE: via `safe32()`) Line 923
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L923
21. NounsDAOLogicV2::_writeQuorumParamsCheckpoint() (NOTE: via `safe32()`) Line 965
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L965

c. NounsDAOProxy.sol
1. NounsDAOProxy::_setImplementation() Line 79 - 80
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L79-L80

d. ERC721Checkpointable.sol
1. ERC721Checkpointable::delegateBySig() Line 140-142
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L140-L142
2. ERC721Checkpointable::getPriorVotes() Line 164
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L164
3. ERC721Checkpointable::_writeCheckpoint() (NOTE: via `safe32()`) Line 238-241
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L238-L241
4. ERC721Checkpointable::votesToDelegate() (NOTE: via `safe96()`) Line 80
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L80
5. ERC721Checkpointable::_moveDelegates() (NOTE: via `sub96()` and `add96()`) 
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L219
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L226

## Using cheaper loop for gas saving 
Loop is a gas costly operation, it can be very expensive in term of gas useage to use Loop in a contract. but loop still can be a option to operate something with only one transaction, with some optimization.
for optimizing loop we need to do this step below:
1. Avoid passing a default value to `i`, integer have default value 0.
example :
```js
    // expensive version
    uint i = 0;

    // cheaper version
    uint i;
```
2. Avoid reading attribute from other object to compare `i`, for most case it's a length (example: array.length). Instead pass it to new variable first and use that variable to compare `i`;
example:
```js 
    // expesinve version
    for (uint i = 0, i < array.length, i++) {};

    //cheaper version 
    uint length = array.length;
    for (uint i, i < length , i++) {};
```

3. Using `++i` instead of `i++` and consider using `unchecked` for it (don't worry `i` will never overflowing since it's start by 0 to some value and for most case gas will run out first instead of `i` being more than 32 bytes uint)
Example :
```js 
    //expensive version  
    for(uint i = 0, i < array.length, i++) {};

    //cheaper version 
    uint length = array.length;
    for(uint i, i < length) {
        ...//implementation of loop
        unchecked{ //at the end of loop
            ++i;
        }
    }
```

NOTE: for informational only some instance may be a 'external view' function which not effected with this kind of gas optimization. for `external view` function i will still report it with `EV` mark.

INSTANCE:
a. NounsDAOLogicV1.sol
1. NounsDAOLogicV1::queue() Line 281
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281
2. NounsDAOLogicV1::execute() Line 391
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319
3. NounsDAOLogicV1::cancel() Line 346
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L346
4. NounsDAOLogicV1::veto() Line 371
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371

b. NounsDAOLogicV2.sol
1. NounsDAOLogicV2::queue() Line 292
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292
2. NounsDAOLogicV2::execute() Line 330
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L330
3. NounsDAOLogicV2::cancel() Line 357
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L357
4. NounsDAOLogicV2::veto() Line Line 382
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L382

## Avoid passing a default value to new variable
Like in the loop section, avoiding default value passing can be optimize. I will make this optimization only difference instance than the loop.

INSTANCE:
1. NounsDAOLogicV1::porpose()
2. NounsDAOLogicV2::porpose()
```js
Proposal storage newProposal = _proposals[proposalCount];
        newProposal.id = proposalCount;
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.eta = 0; //default
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.forVotes = 0;//default
        newProposal.againstVotes = 0;//default
        newProposal.abstainVotes = 0;//default
        newProposal.canceled = false;//default
        newProposal.executed = false;//default
        newProposal.vetoed = false;//default
        newProposal.totalSupply = temp.totalSupply;
        newProposal.creationBlock = block.number;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L227-L245
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L217-L235

SOLUTION: 
Leave it empty like this 
```js 
    //expensive 
    uint a = 0
    bool b = false

    //cheaper
    uint a
    bool b
```
in above case it can be deleted from the line instead, Like this: 
```js
        newProposal.id = proposalCount;
        newProposal.proposer = msg.sender;
        newProposal.proposalThreshold = temp.proposalThreshold;
        newProposal.targets = targets;
        newProposal.values = values;
        newProposal.signatures = signatures;
        newProposal.calldatas = calldatas;
        newProposal.startBlock = temp.startBlock;
        newProposal.endBlock = temp.endBlock;
        newProposal.totalSupply = temp.totalSupply;
        newProposal.creationBlock = block.number;
```

## A function that only called inside the `constructor` or `initialize` can be deleted and move the logic inside the `contructor` or `initialize` 

defining new function is cost gas. if the function is `internal` but only called once in `constructor` or `initialize`, we can get rid the function and move the function logic inside `contructor` or `initialize`.

INSTANCE:
NounsDAOProxy::delegateTo()
Only called here: (`constructor`)
```js
    constructor(
        address timelock_,
        address nouns_,
        address vetoer_,
        address admin_,
        address implementation_,
        uint256 votingPeriod_,
        uint256 votingDelay_,
        uint256 proposalThresholdBPS_,
        uint256 quorumVotesBPS_
    ) {
        // Admin set to msg.sender for initialization
        admin = msg.sender;


        delegateTo( //@audit only called here!!
            implementation_,
            abi.encodeWithSignature(
                'initialize(address,address,address,uint256,uint256,uint256,uint256)',
                timelock_,
                nouns_,
                vetoer_,
                votingPeriod_,
                votingDelay_,
                proposalThresholdBPS_,
                quorumVotesBPS_
            )
        );


        _setImplementation(implementation_);


        admin = admin_;
    }
```
https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOProxy.sol#L55-L67

