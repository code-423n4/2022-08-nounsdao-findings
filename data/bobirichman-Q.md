# QA REPORT

## [LOW] Not verified input
At the following functions you should verify the parameters that are being assigned to a state variable.

### Proof of concept:
- [NounsArt.sol#L59](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L59)
- [NounsArt.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L68)

## [LOW] Approve 0 first
At some tokens you can approve an amount (at USDT for instance) only after approving to 0. Consider using increase/decrease approve notation instead.

Example: [NounsToken.sol#L139](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsToken.sol#L139)

## [LOW] Missing nonReentrancy modifier
The following functions allows attackers to try reentrancy since they are calling to external contracts / transferring eth. Consider adding a nonReentrancy modifier.

### Proof of concept:
- [NounsDAOLogicV1.sol#L180](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L180)
- [NounsArt.t.sol#L351](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L351)

## [LOW] Improving precision by using multiplication instead division
When conditioning over a mathematical in-equation of the type ```x / y < z``` you can improve precision by changing to ```x < z * y```.

Example: [NounsAuctionHouse.sol#L109](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L109)

## [LOW] Missing pause functionality


### Proof of concept:
- [DescriptorUpgradeViaProposal.t.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgradeViaProposal.t.sol)
- [WETH.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/WETH.sol)

## [LOW] Payable functions that should not be payable
The following functions are payable but doesn't record the sender transaction. Consider making them not payable instead.

### Proof of concept:
- [WETH.sol#L24](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/WETH.sol#L24)
- [MaliciousBidder.sol#L8](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/MaliciousBidder.sol#L8)

## [LOW] Consider adding two steps verification process
Protocol ownership transfer should be dealt with great care. Adding two steps verification is necessary for that matter.

Example: [NounsDescriptorV2.t.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol)

## [LOW] Consider adding an MIT licence
Adding MIT licence is common among open source projects, consider adding it to every solidity file.

### Proof of concept:
- [Constants.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/helpers/Constants.sol)
- [NounsAuctionHouseProxy.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/proxies/NounsAuctionHouseProxy.sol)

## [NON CRITICAL] Named returns
Consider using one of the two return methods. Using both makes the code less readable.

### Proof of concept:
- [NounsDAOLogicV2.sol#L412](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L412)
- [SVGRenderer.sol#L68](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L68)

## [NON CRITICAL] Missing function spec comments


### Proof of concept:
- [NounsArt.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsArt.sol#L142)
- [NounsArt.t.sol#L228](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsArt.t.sol#L228)

## [NON CRITICAL] Floating pragma
Floating pragma is a bad practice, since it does not guaranty the same version at future deployments.

### Proof of concept:
- [DescriptorUpgradeViaProposal.t.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgradeViaProposal.t.sol)
- [NounsDAOExecutor.sol](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol)

## [NON CRITICAL] Unused function parameters should have name removed
If for any reason the following unused parameters are necessary then remove their naming (since only the type matters for function signature)

### Proof of concept:
- [NounsDAOExecutorHarness.sol#L24](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/test/NounsDAOExecutorHarness.sol#L24)
- [NounsAuctionHouseProxy.sol#L27](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/proxies/NounsAuctionHouseProxy.sol#L27)

## [NON CRITICAL] NonReentrancy should be the first modifier in order


Example: [NounsAuctionHouse.sol#L96](https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L96)

## [NON CRITICAL] Missing event emit
In functions that update/assigns state variables it is a good practice to emit event.

### Proof of concept:
- [NounsDescriptorV2.t.sol#L20](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/NounsDescriptorV2.t.sol#L20)
- [DescriptorUpgradeViaProposal.t.sol#L16](https://github.com/code-423n4/2022-08-nounsdao/tree/main/test/foundry/DescriptorUpgradeViaProposal.t.sol#L16)