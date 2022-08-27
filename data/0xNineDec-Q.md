## L 1) Insufficient protection of sensitive data
The `hardhat.config.ts` uses sensitive information imported from an un-committed environment file. The usage of either `.env` imported variables or even plain pasted keys make it easier for an attacker to compromise the keys used for monitoring, deployment, testing and even if wallet private keys are used in such way funds can be compromised. The following data could be compromised if a leak happens or if the `.gitignore` file is mistakenly deleted according to the imports performed on `hardhat.config.ts` :

    INFURA_PROJECT_ID
    ETHERSCAN_API_KEY
    MNEMONIC
    WALLET_PUBLIC_KEY
    WALLET_PRIVATE_KEY


## L 2) Wrong parameters while emitting the Withdraw event

Currently the mentioned event within `NounsDAOLogicV2._withdraw()` emits the boolean `sent` parameter. 

    function _withdraw() external {
        if (msg.sender != admin) {
            revert AdminOnly();
        }

        uint256 amount = address(this).balance;
        (bool sent, ) = msg.sender.call{ value: amount }(''); 

        emit Withdraw(amount, sent);
    }

The `sent` parameter within the mentioned event is not checked after the low-level call, so there are scenarios where `Withdraw` can be `Withdraw(currentBalance, false)` which will be deceiving for filtering services and for event tracking. It is advised to check the truth of the sent parameter rather than emitting it on an event (and emit no events if the call fails, e.g. reverting before that line).


## QA 1) Inconsistent ways of execution reverting

There are several parts of the codebase where `require` statements with a revert string is used, and some other parts where `if statements` with custom errors are used. It is advised to unify the criteria regarding error handling. It is remarked that the boolean check needs to be inverted while going from one method to another (the require revert on false statements whereas true statements trigger custom errors inside if statements). Also, as a reminder, using custom errors from `0.8.x` provide a clear feedback to users while saving gas against error strings.

## QA 2) Some events lack from indexed parameters

It is advisable to have at least one indexed parameter per event in order to enable off-chain filtering for event tracking services. There are some events that have no indexed parameters:
    
_This issue was found 14 times:_

    event ProposalCreated
    event ProposalCreatedWithRequirements
    event ProposalCanceled
    event ProposalQueued
    event ProposalExecuted
    event ProposalVetoed
    event VotingDelaySet
    event VotingPeriodSet
    event NewImplementation
    event ProposalThresholdBPSSet
    event QuorumVotesBPSSet
    event NewPendingAdmin
    event NewAdmin
    event NewVetoer


## QA 3) Public functions that are not called by the contract should be external

If a function is not meant to be called inside a contract, it should be marked as external instead. According to the [Solidity Docs](https://docs.soliditylang.org/en/latest/contracts.html#function-overriding) an inherited virtual external function can be overridden and its behavior can be changed as public.

_This issue was found 8 times:_

    NounsDAOLogicV1.propose()
    NounsDAOLogicV1._burnVetoPower()
    NounsDAOLogicV1.proposalThreshold()
    NounsDAOLogicV1.quorumVotes()
    NounsDAOLogicV2.propose()
    NounsDAOLogicV2._burnVetoPower()
    NounsDAOLogicV2.proposalThreshold()
    NounsDAOLogicV2.maxQuorumVotes()

## QA 4) Avoid using in line assembly for available built-in methods

Using inline assembly for methods that are built-in within the current compiler versions avoid the need to use assembly which can make some compilers to interpret the code as complex. It is advised using inline assembly only when there is no other way to perform the intended behavior or when specific goals are meant to be achieved (such as optimizations).

    NounsDAOLogicV1.getChainIdInternal()
    NounsDAOLogicV2.getChainIdInternal()

Which can be modified by `uint256 chainId = block.chainid;`

## QA 5) Expressions that compute constant values via subsequent calls such as keccak256 could be declared as immutable

The main difference between both variable availability modifiers is that the `constant` calculates the hash each time it is referenced whereas `immutable` performs this calculation on deployment. More about this could be read [here](https://github.com/ethereum/solidity/issues/9232#issuecomment-646131646).

_This issue was found 4 times:_
    
    NounsDAOLogicV1.DOMAIN_TYPEHASH
    NounsDAOLogicV1.BALLOT_TYPEHASH
    NounsDAOLogicV2.DOMAIN_TYPEHASH
    NounsDAOLogicV2.BALLOT_TYPEHASH

## QA 6) Signatures recovered directly with `ecrecover()` can be malleable

It is advisable to consider using a third party library that avoids any signature malleability scenario that may occur while recovering signatures directly with `ecrecover()` (such as OpenZeppelin's `ECDSA`).

_This issue was found 3 times:_

    NounsDAOLogicV1.castVoteBySig()
    NounsDAOLogicV2.castVoteBySig()
    ERC721Checkpointable.delegateBySig()