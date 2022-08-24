- **File:** NounsDAOLogicV2.sol  
    **Severity:** QA, non-critical
    **Description:**
    In [initialize](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L124)
    function, new parameter was added: `DynamicQuorumParams calldata dynamicQuorumParams_`.
    `DynamicQuorumParams` is a new structure with fields: `minQuorumVotesBPS`, `maxQuorumVotesBPS`, and `quorumCoefficient`.
    Values of these fields are not checked when `initialize` is called, even though new relevant constants were added
    (`MIN_QUORUM_VOTES_BPS_LOWER_BOUND`, `MIN_QUORUM_VOTES_BPS_UPPER_BOUND`, `MAX_QUORUM_VOTES_BPS_UPPER_BOUND`).
    Moreover, `quorumCoefficient` doesn't have relevant constants to control the range of its correct values.

    It's recommended to check the fields of `dynamicQuorumParams_` parameter in `initialize` function, as well as add
    constraints to `quorumCoefficient` to avoid initialization of the contract with incorrect parameters.

    It's also recommended to add necessary checks to functions [_setQuorumCoefficient](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L726)
    and [_setDynamicQuorumParams](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L748)
    to avoid setting an incorrect value of `quorumCoefficient`.

- **File:** NounsDAOLogicV2.sol  
    **Severity:** QA, non-critical
    **Description:**
    Function [_setMinQuorumVotesBPS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L673)
    allows to set a new minimal quorum votes basis points, which is passed via parameter
    `newMinQuorumVotesBPS`. This parameter is checked against `maxQuorumVotesBPS` of the dynamic quorum parameters at
    the current block. In another function that allows to set a new minimal quorum votes basis points, [_setDynamicQuorumParams](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L748),
    there's no such check, which allows to set an incorrect value for `minQuorumVotesBPS` in dynamic quorum parameters.

    It's recommended to add the missing check to `_setDynamicQuorumParams` function to ensure that a newly set value is
    correct.

- **File:** NounsDAOLogicV2.sol  
    **Severity:** QA, non-critical
    **Description:**
    [MAX_QUORUM_VOTES_BPS](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L89)
    constant was not removed during the upgrade to V2. It can introduce confusion to
    the code since the actual maximal quorum votes basis points were set to 6000 in V2 in another constant ([MAX_QUORUM_VOTES_BPS_UPPER_BOUND](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L86)).

    It's recommended to remove the constant to avoid possible confusion.

- **File:** NounsDAOLogicV2.sol  
    **Severity:** QA, non-critical
    **Description:**
    New fields were added to `Proposal` structure: [totalSupply and creationBlock](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L244-L245). However, they were not added to
    the relevant events: [ProposalCreated](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L250-L260)
    and [ProposalCreatedWithRequirements](https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L264-L276).
    Events serve the purpose of indexing on-chain data to make changes in data easily discoverable. Adding the new
    fields to the events will improve monitoring and indexing of new proposals off-chain. Since, when querying
    events/logs from an Ethereum node, block number is always provided, it's recommended to add only `totalSupply` to
    the relevant events.