# #
# 1 – Using Vulnerable Version of Openzeppelin

**Context:**
[package.json#L32-L33](https://github.com/code-423n4/2022-08-nounsdao/blob/main/package.json#L32-L33)

**Description:**
The package.json configuration file says that the project is using 4.1.0 of OpenZeppelin which has a vulnerability in initializers that call external contracts,
There is 1 instance of this issue

Package                                                                           
1-  @openzeppelin/contracts (npm)
      Affected versions:  >=3.2.0 <4.4.1 
      Patched versions:   4.4.1
2- @openzeppelin/contracts-upgradeable (npm)
     Affected versions:  >=3.2.0 <4.4.1 
     Patched versions:   4.4.1

**Recommendation:**
Use patched versions

**Proof Of Concept:**
https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-9c22-pwxw-p6hx

# #
# 2 – A protected initializer function that can be called at most once must be defined.

**Context:**
[NounsDAOLogicV2.sol#L124](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L124)

**Description:**
A protected initializer function that can be called at most once must be defined. Admin should be prevented from calling the restartize function, even if it is incorrect

**Recommendation:**
OpenZeppelin recommends that the initializer modifier be applied to constructors
https://forum.openzeppelin.com/t/uupsupgradeable-vulnerability-post-mortem/15680/5


# #
# 3- For modern and more readable code; Update import usages

**Context:**
All Contracts

**Description:**
Our Solidity code is also cleaner in another way that might not be noticeable: the struct Point. We were importing it previously with global import but not using it. The Point struct ```polluted the source code``` with an unnecessary object we were not using because we did not need it. This was breaking the rule of modularity and modular programming: ```only import what you need```Specific imports with curly braces allow us to apply this rule better.

**Recommendation:**
```import {contract1 , contract2} from "filename.sol";```

# #
# 4 – ```0``` address Check

**Context:**
[NounsDAOLogicV2.sol#L185](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L185)
[NounsDAOLogicV2.sol#L589](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L589)
[NounsDAOLogicV2.sol#L799](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L799)

**Description:**
Also check of the address to protect the code from 0x0 address  problem just in case.This is best practice

**Recommendation:**
like this ;
```require(_forwarder != address(0x0));```


# #
# 5 – Base Gas and Refund Priority Fee should be changeable archirecture

**Context:**
[NounsDAOLogicV2.sol#L95](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L95)
[NounsDAOLogicV2.sol#L98](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L98)

**Description:**
```Base Gas``` and ```Refund Priority Fee``` values have been added as constants and cannot be changed, but with the Ethereum 2.0 Merge transition, Gas fees may change  in the new structure, so these values should be set to ```admin``` in order not to be surprised considering this unpredictable transition.

A function to be changed in the  authority should be added

**Recommendation:**
```js
// we should use mixedCase When visibility is public

uint256 public AX_REFUND_PRIORITY_FEE = 2 gwei;
uint256 public REFUND_BASE_GAS = 36000;

function changeFee (uint256 _fee) external {
    require(msg.sender == admin, "Only admin can change");
    AX_REFUND_PRIORITY_FEE = _fee;
}

function changeGas (uint256 _gas) external {
    require(msg.sender == admin, "Only admin can change");
    REFUND_BASE_GAS = _gas;
}
```

# #
# 6 – Add to Blacklist function

**Description:**
Cryptocurrency mixing service, Tornado Cash, has been blacklisted in the OFAC.
A lot of blockchain companies, token projects, NFT Projects have ```blacklisted``` all Ethereum addresses owned by Tornado Cash listed in the US Treasury Department's sanction against the protocol.
(https://home.treasury.gov/policy-issues/financial-sanctions/recent-actions/20220808)
In addition, these platforms even ban accounts that have received ETH on their account with Tornadocash

Some of these Projects;
* USDC (https://www.circle.com/en/usdc)
* Flashbots (https://www.paradigm.xyz/portfolio/flashbots )
* Aave (https://aave.com/)
* Uniswap
* Balancer
* Infura
* Alchemy 
* Opense
* dYdX 

Details on the subject;
https://twitter.com/bantg/status/1556712790894706688?s=20&t=HUTDTeLikUr6Dv9JdMF7AA

https://cryptopotato.com/defi-protocol-aave-bans-justin-sun-after-he-randomly-received-0-1-eth-from-tornado-cash/

For this reason, every project in the Ethereum network must have a blacklist function, this is a good method to avoid legal problems in the future, apart from the current need.

NFT transactions from the project by an account funded by Tonadocash or banned by OFAC can lead to legal problems.Especially American Citizens may want to get addresses to the blacklist legally, this is not an obligation

```The ban on Tornado Cash makes little sense, because in the end, no one can prevent people from using other mixer smart contracts, or forking the existing ones. It neither hinders cybercrime, nor privacy.```

Here is the most beautiful and close to the project example; Manifold

Manifold NFT Upgradeable Contract
https://etherscan.io/address/0xe4e4003afe3765aca8149a82fc064c0b125b9e5a#code

```js
     modifier nonBlacklistRequired(address extension) {
         require(!_blacklistedExtensions.contains(extension), "Extension blacklisted");
         _;
     }
```
Recommended Mitigation Steps add to Blacklist function and modifier

# #
# 7 – Missing Event for Critical Parameters Change

**Context:**
[NounsDAOLogicV2.sol#L1030](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030)
[NounsDAOLogicV2.sol#L851](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L851)

**Description:**
Events help non-contract tools to track changes, and events prevent users from being surprised by changes

**Recommendation:**
Add Event-Emit


# #
# 8 – Add to indexed parameter for countable Events

**Context:**
[NounsDAOInterfaces.sol#L39](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L39)
[NounsDAOInterfaces.sol#L52](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L52)

**Description:**
Add to indexed parameter for countable Events

**Recommendation:**
Add Event-Emit


# #
# 9- Impossible to have ```proposalId``` value 0

Context:
[NounsDAOLogicV2.sol#L433](https://github.com/code-423n4/2022-08nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L433)

**Description:**
The proposal id is determined by the ```function propose``` function and its first value must be 1, there is a proposal id with number 0, but ```function state``` checks the proposal id 0 and queries as if it exists

**Proof of Concept:**
```js
 function state(uint256 proposalId) public view returns (ProposalState) {
        require(proposalCount >= proposalId, 'NounsDAO::state: invalid proposal id');
        Proposal storage proposal = _proposals[proposalId];
        if (proposal.vetoed) {
            return ProposalState.Vetoed;
        } else if (proposal.canceled) {
            return ProposalState.Canceled;
        } else if (block.number <= proposal.startBlock) {
            return ProposalState.Pending;
        } else if (block.number <= proposal.endBlock) {
            return ProposalState.Active;
        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {
            return ProposalState.Defeated;
        } else if (proposal.eta == 0) {
            return ProposalState.Succeeded;
        } else if (proposal.executed) {
            return ProposalState.Executed;
        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
            return ProposalState.Expired;
        } else {
            return ProposalState.Queued;
        }
    }
```
**Recommendation:**
```js
 function state(uint256 proposalId) public view returns (ProposalState) {
        require(proposalCount >= proposalId,  && proposalId != 0, 'NounsDAO::state: invalid proposal id');
        Proposal storage proposal = _proposals[proposalId];
        if (proposal.vetoed) {
            return ProposalState.Vetoed;
        } else if (proposal.canceled) {
            return ProposalState.Canceled;
        } else if (block.number <= proposal.startBlock) {
            return ProposalState.Pending;
        } else if (block.number <= proposal.endBlock) {
            return ProposalState.Active;
        } else if (proposal.forVotes <= proposal.againstVotes || proposal.forVotes < quorumVotes(proposal.id)) {
            return ProposalState.Defeated;
        } else if (proposal.eta == 0) {
            return ProposalState.Succeeded;
        } else if (proposal.executed) {
            return ProposalState.Executed;
        } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
            return ProposalState.Expired;
        } else {
            return ProposalState.Queued;
        }
    }
```