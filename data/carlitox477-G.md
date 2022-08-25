# ERC721Checkpointable
## decimals should not be initialized
Its default value is zero, this is just a waste of gas

## add96, sub96 are unnecessary
Solidity 0.8.0 and higher version handle overflow and underflow. The only justification to use this functions if they are used inside an unchecked block (which is not the case). Both function can be removed to save gas.

## ERC721._name variable can change its visibility to internal to save gas in ERC721Checkpointable.delegateBySig()
This would avoid calling public name() function, and we could access to _name instead.

## _delegate: Can save gas by moving event emission to function end
Given there are chances that the function revert in functions votesToDelegate and _moveDelegates, DelegateChanged event emission can be move to end of the function to save gas in case one of this two functions fails.

## _moveDelegates: No need to use sub96 nor add96
Given that  uint96, srcRepOld and dstRepOld are uint96, there is no need to use add96 nor sub96. Overflow and underflow checks are included by default since solidity version 0.8.0

# NounsDaoLogicV1
## propose: proposalCount++ is not gas efficient
It is better to use ```++proposalCount```

## propose visibility should be external
Given it is not called by other functions, unless these is the case in an inherited contract, it visibility should be change from public to external. This will save gas in every call. ([POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices))

## queue: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## execute: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## cancel: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## veto: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## _burnVetoPower: wrong visibility implies more gas
Change it visibility to external

## proposalThreshold: wrong visibility implies more gas
It is not used by any other function in the contract. Change it visibility to external

## quorumVotes: wrong visibility implies more gas
It is not used by any other function in the contract. Change it visibility to external

# NounsDaoLogicV2
## quorumVotesBPS storage variable is never used
Seems to be deletable

## propose: proposalCount++ is not gas efficient
It is better to use ```++proposalCount```

## propose visibility should be external
Given it is not called by other functions, unless these is the case in an inherited contract, it visibility should be change from public to external. This will save gas in every call. ([POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices))

## propose: ProposalCreated can be omited
ProposalCreated is emited due to mantain backwards compatibility with GovernorBravo events, but isn't it better to update whatever app which use ProposalCreated to handle ProposalCreatedWithRequirements and save gas this way?

## queue: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## execute: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## cancel: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## veto: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

# NounsDaoLogicV2

## queue: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## execute: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## cancel: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```

## veto: for loop inefficient i++
Change ```i++``` for ```unchecked{++i}```


## getDynamicQuorumParamsAt: put quorumParamsCheckpoints in a storage variable pointer to save gas
Add line ```DynamicQuorumParams storage _quorumParamsCheckpoints = quorumParamsCheckpoints``` at function start, and then change ```quorumParamsCheckpoints``` ocurrencies in the function for this new variable.

## proposalThreshold can be set to external
It is not used by any other function, so it should be declared as external. This will save gas in every call. ([POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

## minQuorumVotes can be set to external
It is not used by any other function, so it should be declared as external. This will save gas in every call. ([POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)

## maxQuorumVotes can be set to external
It is not used by any other function, so it should be declared as external. This will save gas in every call. ([POC](https://ethereum.stackexchange.com/questions/19380/external-vs-public-best-practices)