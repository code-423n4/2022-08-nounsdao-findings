## Impact
Don't check result of low-level call(). If for some reason call() fails it's fine to abort and revert.

## Proof of Concept
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L789

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L983

## Tools Used

Code check

## Recommended Mitigation Steps


For _withdraw() method add this after msg.sender.call: `require(sent, "Transfer failed.");` 
For _refundGas() method add this after msg.sender.call: `require(refundSent, "Transfer failed.");` 

