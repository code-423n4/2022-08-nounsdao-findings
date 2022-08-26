## [NAZ-L1] Signature Malleability of EVM's `ecrecover()`
**Severity**: Low
**Context**: [`NounsDAOLogicV2.sol#L576`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L576), [`NounsDAOLogicV1.sol#L484`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L484), [`ERC721Checkpointable.sol#L139`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L139)

**Description**:
The function calls the Solidity `ecrecover()` function directly to verify the given signatures. However, the `ecrecover()` EVM opcode allows malleable (non-unique) signatures and thus is susceptible to replay attacks.

Although a replay attack seems not possible for `NounsDAOLogicV2 && V1` since the voter address is checked against `receipt.voted` to prevent re-voting. While for `ERC721Checkpointable` a nonce is increased each time, ensuring the signatures are not malleable is considered a best practice. However, this may become a vulnerability if used elsewhere.

**Recommendation**:
Use the ecrecover function from [OpenZeppelin's ECDSA library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/ECDSA.sol) for signature verification. (Ensure using a version `> 4.7.3` for there was a critical bug `>= 4.1.0 < 4.7.3`).


## [NAZ-L2] `DOMAIN_TYPEHASH` Can Change 
**Severity**: Low
**Context**: [`NounsDAOLogicV2.sol#L101`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L101), [`NounsDAOLogicV1.sol#L97`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L97), [`ERC721Checkpointable.sol#L59`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L59), 

**Description**:
The variable `DOMAIN_TYPEHASH` is assigned as a constant and will not change after being initialized. However, if a hard fork happens after the contract deployment, the domain would become invalid on one of the forked chains due to the `block.chainid` has changed. Also, you don't need an assmebly to retrieve `chainid`, you can get it from a built in variable `block.chainid`.

**Recommendation**:
Consider the solution from [Sushi Trident](https://github.com/sushiswap/trident/blob/concentrated/contracts/pool/concentrated/TridentNFT.sol#L47-L62).


## [NAZ-L3] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`NounsDAOLogicV2.sol#L621`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L621), [`NounsDAOLogicV2.sol#L637`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L637), [`NounsDAOLogicV2.sol#L654`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L654), [`NounsDAOLogicV2.sol#L673`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L673), [`NounsDAOLogicV2.sol#L701`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L701), [`NounsDAOLogicV2.sol#L726`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726), [`NounsDAOLogicV2.sol#L748`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L748), [`NounsDAOLogicV2.sol#L799`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799), [`NounsDAOLogicV2.sol#L839`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839), [`NounsDAOLogicV1.sol#L529`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L529), [`NounsDAOLogicV1.sol#L545`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L545), [`NounsDAOLogicV1.sol#L562`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L562), [`NounsDAOLogicV1.sol#L580`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L580), [`NounsDAOLogicV1.sol#L597`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L597), [`NounsDAOProxy.sol#L78`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L78)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L4] Missing Time locks
**Severity**: Low 
**Context**: [`NounsDAOLogicV2.sol#L621`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L621), [`NounsDAOLogicV2.sol#L637`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L637), [`NounsDAOLogicV2.sol#L654`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L654), [`NounsDAOLogicV2.sol#L673`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L673), [`NounsDAOLogicV2.sol#L701`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L701), [`NounsDAOLogicV1.sol#L545`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L545), [`NounsDAOLogicV1.sol#L562`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L562), [`NounsDAOLogicV1.sol#L580`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L580)

**Description**:
When critical parameters of systems need to be changed, it is required to broadcast the change via event emission and recommended to enforce the changes after a time-delay. This is to allow system users to be aware of such critical changes and give them an opportunity to exit or adjust their engagement with the system accordingly. None of the onlyOwner functions that change critical protocol addresses/parameters have a timelock for a time-delayed change to alert: (1) users and give them a chance to engage/exit protocol if they are not agreeable to the changes (2) team in case of compromised owner(s) and give them a chance to perform incident response.

**Recommendation**:
Users may be surprised when critical parameters are changed. Furthermore, it can erode users' trust since they can’t be sure the protocol rules won’t be changed later on. Compromised owner keys may be used to change protocol addresses/parameters to benefit attackers. Without a time-delay, authorised owners have no time for any planned incident response. (May be medium since owner can change critical prameters at anytime that can affect the users poorly).


## [NAZ-L5] Missing Zero-address Validation
**Severity**: Low
**Context**: [`NounsDAOLogicV2.sol#L127`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L127), [`NounsDAOLogicV2.sol#L799`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799), [`NounsDAOLogicV2.sol#L839`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839), [`NounsDAOLogicV1.sol#L597`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L597), [`NounsDAOLogicV1.sol#L637`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L637)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L6] `receive() && fallback()` Function Should Emit An Event
**Severity**: Low
**Context**: [`NounsDAOLogicV2.sol#L1030`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030), [`NounsDAOProxy.sol#L130`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L130), [`NounsDAOProxy.sol#L138`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L138)

**Description**:
Consider emitting an event inside this function with `msg.sender` and `msg.value` as the parameters. This would make it easier to track incoming ether transfers.

**Recommendation**:
Consider adding events to the `receive()` functions. 


## [NAZ-N1] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol#L167`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L167), [`NounsDAOLogicV1.sol#L157`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L157), [`ERC721Checkpointable.sol.sol#L41-L73`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L41-L73), [`ERC721Enumerable.sol.sol#L40-L49`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L40-L49)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Some constructs deviate from this recommended best-practice: Modifiers are in the middle of contracts. External/public functions are mixed with internal/private ones. Few events are declared in contracts while most others are in corresponding interfaces.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N2] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol#L95`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L95)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N3] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol#L1030`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N4] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`NounsDAOLogicV2.sol#L59`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L59), [`NounsDAOLogicV2.sol#L92`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L92), [`NounsDAOLogicV2.sol#L311`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L311), [`NounsDAOLogicV2.sol#L533`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L533), [`NounsDAOLogicV2.sol#L588`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L588), [`NounsDAOLogicV2.sol#L748`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L748), [`NounsDAOLogicV2.sol#L783`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783), [`NounsDAOLogicV2.sol#L799`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799), [`NounsDAOLogicV2.sol#L817`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L817), [`NounsDAOLogicV2.sol#L839`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L839), [`NounsDAOLogicV2.sol#L851`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851), [`NounsDAOLogicV2.sol#L866`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L866), [`NounsDAOLogicV2.sol#L988`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L988), [`NounsDAOLogicV2.sol#L1006`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1006), [`NounsDAOLogicV2.sol#L1010`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1010), [`NounsDAOLogicV2.sol#L1018`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1018), [`NounsDAOLogicV2.sol#L1023`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1023), [`NounsDAOLogicV1.sol#L67`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L67), [`NounsDAOLogicV1.sol#L94`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L94), [`NounsDAOLogicV1.sol#L294`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L294), [`NounsDAOLogicV1.sol#L496`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L496), [`NounsDAOLogicV1.sol#L529`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L529), [`NounsDAOLogicV1.sol#L545`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L545), [`NounsDAOLogicV1.sol#L562`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L562), [`NounsDAOLogicV1.sol#L580`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L580), [`NounsDAOLogicV1.sol#L597`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L597), [`NounsDAOLogicV1.sol#L615`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L615), [`NounsDAOLogicV1.sol#L637`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L637), [`NounsDAOLogicV1.sol#L649`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L649), [`NounsDAOLogicV1.sol#L672`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L672), [`NounsDAOLogicV1.sol#L676`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L676), [`NounsDAOProxy.sol#L78`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L78), [`NounsDAOProxy.sol#L94`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94), [`ERC721Checkpointable.sol#L253`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L253), [`ERC721Checkpointable.sol#L258`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L258), [`ERC721Checkpointable.sol#L263`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L263), [`ERC721Checkpointable.sol#L273`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L273), [`ERC721Checkpointable.sol#L282`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L282)

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby internal and private functions utilize the `mixedCase` format starting with an underscore. While public and external functions just utilize the `mixedCase` format. Also, variables are declared as `constant` utilize the `UPPER_CASE_WITH_UNDERSCORES` format.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the Solidity style guide.


## [NAZ-N5] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol), [`NounsDAOLogicV1.sol`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol), [`ERC721Checkpointable.sol`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N6] Comment Line Length
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L20), [`NounsDAOLogicV2.sol#L495`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L495), [`NounsDAOLogicV2.sol#L509`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L509), [`NounsDAOLogicV2.sol#L599`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L599), [`NounsDAOLogicV2.sol#L796`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L796), [`NounsDAOLogicV2.sol#L900`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L900), [`NounsDAOLogicV1.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L20), [`NounsDAOInterfaces.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L20), [`NounsDAOInterfaces.sol#L28`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L28), [`NounsDAOInterfaces.sol#L153`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L153), [`NounsDAOInterfaces.sol#L156`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L156), [`NounsDAOInterfaces.sol#L179`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L179), [`NounsDAOInterfaces.sol#L181`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L181), [`NounsDAOInterfaces.sol#L253`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L253), [`NounsDAOInterfaces.sol#L256`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L256), [`NounsDAOInterfaces.sol#L279`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L279), [`NounsDAOInterfaces.sol#L281`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L281), [`NounsDAOInterfaces.sol#L351`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L351), [`NounsDAOInterfaces.sol#L353`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L353), [`NounsDAOInterfaces.sol#L373`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L373), [`NounsDAOInterfaces.sol#L375`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L375), [`NounsDAOProxy.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L20), [`NounsDAOProxy.sol#L29`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L29), [`ERC721Checkpointable.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L20), [`ERC721Checkpointable.sol#L40`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L40), [`ERC721Checkpointable.sol#L198`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L198), [`ERC721Enumerable.sol#L20`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L20)

**Description**:
Max line length must be no more than 120 but many comments are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N7] Unindexed Event Parameters
**Severity** Informational
**Context**: [`NounsDAOInterfaces.sol#L37-L62`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L37-L62), [`NounsDAOInterfaces.sol#L76`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L76), [`NounsDAOInterfaces.sol#L85-106`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L85-L106), [`NounsDAOInterfaces.sol#L111-L117`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L111-L117), [`NounsDAOInterfaces.sol#L123`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L123)

**Description**:
Parameters of certain events are expected to be indexed so that they’re included in the block’s bloom filter for faster access. Failure to do so might confuse off-chain tooling looking for such indexed events.

**Recommendation**:
Consider adding the indexed keyword to event parameters that should include it.


## [NAZ-N8] Spelling Errors
**Severity**: Informational
**Context**: [`NounsDAOLogicV2.sol#L61 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L61), [`NounsDAOLogicV2.sol#L64 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L64), [`NounsDAOLogicV2.sol#L67 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L67), [`NounsDAOLogicV2.sol#L73 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L73), [`NounsDAOLogicV2.sol#L76 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L76), [`NounsDAOLogicV2.sol#L88 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L88), [`NounsDAOLogicV2.sol#L115 (contructor => constructor)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L115), [`NounsDAOLogicV2.sol#L848 (priviledges => privileges)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L848), [`NounsDAOLogicV1.sol#L69 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L69), [`NounsDAOLogicV1.sol#L72 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L72), [`NounsDAOLogicV1.sol#L75 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L75), [`NounsDAOLogicV1.sol#L78 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L78), [`NounsDAOLogicV1.sol#L81 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L81), [`NounsDAOLogicV1.sol#L84 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L84), [`NounsDAOLogicV1.sol#L87 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L87), [`NounsDAOLogicV1.sol#L90 (setable => settable)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L90), [`NounsDAOLogicV1.sol#L104 (contructor => constructor)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L104), [`NounsDAOLogicV1.sol#L646 (priviledges => privileges)`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L646)

**Description**:
Spelling errors in comments can cause confusion to both users and developers.

**Recommendation**:
Consider checking all misspellings to ensure they are corrected.

## [NAZ-N9] Multiple Solidity Pragma
**Severity**: Informational
**Context**: [`ERC721Enumerable.sol`](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol)

**Description**:
It is better to use one Solidity compiler version across all contracts instead of different versions with different bugs and security checks. 

**Recommendation**: 
Consider ensuring all pragma versions are the same one.

## [NAZ-N10] Floating Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts)

**Description**:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

**Recommendation**: 
Consider locking the pragma version.


## [NAZ-N11] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.