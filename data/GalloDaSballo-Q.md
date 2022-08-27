# Executive Summary

Below are findings, sorted by severity, along with notes on how to remediate them.

## Legend:
- U -> Impact is unclear and potentially higher than Low Severity
- L -> Low Severity -> Could cause issues however impact is limited
- R -> Refactoring -> Suggested Code Change to improve readability and maintainability or to offer better User Experience
- NC -> Non-Critical / Informational -> No risk of loss, pertains to events or has no impact

## U - Change of Admin can happen without a delay

While the purpose of the Governor is to ensure governance votes on proposals, certain operations can be executed by the `admin` without delay nor voting, one of them is changing admin.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L799-L833

In spirit of the contract I recommend adding a delay, or forcing `admin` changes to be performed by the Timelock


## U - Lack of a Sweep function means tokens can be stuck in the Governor

A sweep, similar to the `withdraw` function, ideally transferring the tokens to the timelock, for a proposal to then release them, will allow recouping back tokens sent back by mistake.

A lack of sweep on the Governor means that tokens sent to it by mistake will not be retrievable, a catch-all sweep, that sends tokens to the Timelock will allow to use governance to claw them back.

## L - Unchecked Call in Withdraw

`payable.call` is a low-level call, it will not cause a revert on failure.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L789-L790

```solidity
        (bool sent, ) = msg.sender.call{ value: amount }('');

```

This is not particularly risky as it can always be called again until it works


## L - Lack of Zero check

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L839-L840

```solidity
    function _setVetoer(address newVetoer) public {
        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');
```

- See also:
[NounsDAOExecutor.constructor](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOExecutor.sol#L76-L77)


## L - DelegateTo will not fail if implementation is not a contract

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxyV2.sol#L98

Is a low level call, if `implementation` = random address it will still return true.

That's because the low level call `delegatecall` will not check for the contract existence

Adding a check for contract existence on the setter will avoid this low likelihood risk: https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxyV2.sol#L86



## L - Signature verification scheme is subject to signature malleability

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L482-L485

Due to the structure of ECDSA, each signature has a corresponding also valid signature, for that reason Open Zeppelin's implementation of `tryRecover` ensures the value of the signature is in the lower bits to avoid receiving a "reflected key"

You can see how OZ solved this problem here:
https://github.com/OpenZeppelin/openzeppelin-contracts/blob/93bc3b657b69e4886dcfef7a2502e165f2cd90d2/contracts/utils/cryptography/ECDSA.sol#L147

Because of the fact that you use `receipts` for voting, malleability won't be exploitable effectively, however the way you verify signatures is open to that attack


## R - DelegateTo is Redundant

[`delegateTo`](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxyV2.sol#L97-L98) is only used in constructor, would recommend reconciling code or using it in both the constructor and the fallback.


## R - Unusual naming convention

`_setVotingDelay` is prefixed by an `_` which is used to signify internal functions

This nomenclature is inconsistent, I'd recommend swapping it

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L621-L622

```solidity
    function _setVotingDelay(uint256 newVotingDelay) external {

```

Change to:

```solidity
    function setVotingDelay(uint256 newVotingDelay) external {

```


## R - Hardcoded Values - Recommend using Enums

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L510-L516

```solidity
if (support == 0) {
    proposal.againstVotes = proposal.againstVotes + votes;
} else if (support == 1) {
    proposal.forVotes = proposal.forVotes + votes;
} else if (support == 2) {
    proposal.abstainVotes = proposal.abstainVotes + votes;
}
```

## R - Inconsistent use of camelCase for CONSTANTS

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L92-L93

```solidity
    uint256 public constant proposalMaxOperations = 10; // 10 actions

```

Recommend using ALL_CAPS to maintain consistency

## R - Falsy statement

In `cancel` we can cancel if the proposer doesn't meet proposalThreshold

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L352

```solidity
nouns.getPriorVotes(proposal.proposer, block.number - 1) < proposal.proposalThreshold,
```

However that is by definition impossible due to the check

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L198-L199

```solidity
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,

```

I recommend deleting the check


## R - Bps is not checked to be actual BPS or any uint

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L1005-L1008

```solidity

    function bps2Uint(uint256 bps, uint256 number) internal pure returns (uint256) {
        return (number * bps) / 10000;
    }
```

Because the only parameter passed as bps is `maxQuorumVotesBPS`, the code is fine, I'd recommend adding a comment for clarity (also consider changing `uint256` to `uint16` which is in line with the size of `maxQuorumVotesBPS`)


## NC - Avoid Floating Pragma

Especially to avoid the herd of C4ooooors

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L53-L54

```solidity
pragma solidity ^0.8.6;

```

The caret will reduce risk of breaking changes however, due to plenty of bugs for versions between 0.8.1 to 0.8.14, it would be best to settle on a specific version and "pick your poison".

Considering that Hardhat is set to use 0.8.15, you may as well commit to using that one

## NC - Incorrect Comment

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L847-L856

```solidity
    /**
     * @notice Burns veto priviledges
     * @dev Vetoer function destroying veto power forever
     */
    function _burnVetoPower() public {
        // Check caller is pendingAdmin and pendingAdmin ≠ address(0)
        require(msg.sender == vetoer, 'NounsDAO::_burnVetoPower: vetoer only');

        _setVetoer(address(0));
    }
```

There is no `pendingAdmin` in this function

Also minor typo `priviledges` -> `privileges`