# ERC721Enumerable
## \[Non-critical\]Inherit from solidity version 0.8.6, but it is 0.8.0
ERC721Enumerable inherits from ERC721, this last is written considering solidity version 0.8.6, and the first one ir written using solidity version 0.8.0.
Recommendation: Chan
Change ERC721 solidity version to 0.8.6

# ERC721Checkpointable
## \[Non-critical\] decimals should be changed for DECIMALS
This to respect constant/immutable naming convention

## Naming convention: safe32, safe96, add96, sub96, getChainId
To respect private/internal function naming convention, change their name to:
* **safe32** --> **_safe32**
* **safe96** --> **_safe96**
* **add96** --> **_add96**
* **sub96** --> **_sub96**
* **getChainId** --> **_getChainId**

# NounsDAOProxy
## \[Non-Critical\] delegateTo does not respect naming convention
To respect private/internal function naming convention, change it name to:**_delegateTo**

# NounsDAOLogicV1
## \[Non-Critical\] name and proposalMaxOperations constants do not respect naming convention
Should change their names to respect constant/immutable variables naming convention:
* **name** to **NAME**
* **proposalMaxOperations** to **PROPOSAL_MAX_OPERATIONS**

## \[LOW\] initializer not disable
A good practise is to disable the initializers of a proxy implamentation during it deployment. This can be done by creating creating a storage boolean variable **initialized**, check this value is set to zero at **initialize** function (and modifie its value at the end), and set this storage variable to true in the constructor. Something like

```solidity
bool initialized;
constructor{
    initialized=true;
}
function initialize(/*params*/) public virtual{
    require(!initialized, "Contract already initialized");
    // code
    initialized=true;
}
```

## \[Non-Critical\] queueOrRevertInternal does not respect internal naming convention
Should be changed to **_queueOrRevert**


## \[Non-Critical\] castVoteInternal does not respect internal naming convention
Should be changed to **_castVote**

## \[Non-Critical\] _setVotingDelay, _setVotingPeriod,  _setProposalThresholdBPS,_setQuorumVotesBPS, _setPendingAdmin, _acceptAdmin, _setVetoer, _burnVetoPower do not respect public/extenal naming convention
Should change their names:
* _setVotingDelay --> setVotingDelay
* _setVotingPeriod --> setVotingPeriod
* _setProposalThresholdBPS --> setProposalThresholdBPS
* _setQuorumVotesBPS --> setQuorumVotesBPS
* _setPendingAdmin --> setPendingAdmin
* _acceptAdmin --> acceptAdmin
* _setVetoer --> setVetoer
* _burnVetoPower --> burnVetoPower

## \[Non-Critical\] bps2Uint does not respect internal naming convention
Should be changed to **_bps2Uint**

## \[Non-Critical\] getChainIdInternal do not respect internal naming convention
Should change it name to **_getChainId** to respect private/internal function naming convention.

# NounsDAOLogicV2
## \[Non-Critical\] name and proposalMaxOperations constants do not respect naming convention
Should change their names to respect constant/immutable variables naming convention:
* **name** to **NAME**
* **proposalMaxOperations** to **PROPOSAL_MAX_OPERATIONS**

## \[LOW\] initializer not disable
A good practise is to disable the initializers of a proxy implamentation during it deployment. This can be done by creating creating a storage boolean variable **initialized**, check this value is set to zero at **initialize** function (and modifie its value at the end), and set this storage variable to true in the constructor. Something like

```solidity
bool initialized;
constructor{
    initialized=true;
}
function initialize(/*params*/) public virtual{
    require(!initialized, "Contract already initialized");
    // code
    initialized=true;
}
```

## \[Non-Critical\] queueOrRevertInternal does not respect internal naming convention
Should be changed to **_queueOrRevert**

## \[Non-Critical\] function castRefundableVoteInternal does not respect internal naming convention
Should be changed to **_castRefundableVote**

## \[Non-Critical\] function castVoteInternal does not respect internal naming convention
Should be changed to **_castVote**


## \[Non-Critical\] _setProposalThresholdBPS does not respect public/external naming convention
Should be changed to **setProposalThresholdBPS**

## \[Non-Critical\] _setMinQuorumVotesBPS does not respect public/external naming convention
Should be changed to **setMinQuorumVotesBPS**

## \[Non-Critical\] _setMaxQuorumVotesBPS does not respect public/external naming convention
Should be changed to **setMaxQuorumVotesBPS**

## \[Non-Critical\] _setQuorumCoefficient does not respect public/external naming convention
Should be changed to **setQuorumCoefficient**


## \[Non-Critical\] _setDynamicQuorumParams does not respect public/external naming convention
Should be changed to **setDynamicQuorumParams**

## \[Non-Critical\] proposalCreationBlock does not respect internal naming convention
Should be changed to **_proposalCreationBlock**

## \[Non-Critical\] min does not respect internal naming convention
Should be changed to **_min**

## \[Non-Critical\] bps2Uint does not respect internal naming convention
Should be changed to **_bps2Uint**

## \[Non-Critical\] getChainIdInternal do not respect internal naming convention
Should change it name to **_getChainId** to respect private/internal function naming convention.

## \[Non-Critical\] safe32 does not respect internal naming convention
Should be changed to **_safe32**

## \[Non-Critical\] safe16 does not respect internal naming convention
Should be changed to **_safe16**.
