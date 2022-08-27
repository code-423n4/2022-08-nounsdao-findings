# Nouns DAO Contest QA Report

## Summary

The following QA issues were found during the code audit:

1. Unsafe ERC20 Operation(s) (4 instances)
2. Unspecific Compiler Version Pragma (18 instances)
3. Potential flaws in the binary search algorithm (1 instance)

Total 23 instances of 3 issues.

## 1. Unsafe ERC20 Operation(s) (4 instances) - Completed

ERC20 operations can be unsafe due to different implementations and vulnerabilities in the standard. It is therefore recommended to always either **use OpenZeppelin's SafeERC20 library** or at least to wrap each operation in a require statement.

```solidity
2022-08-nounsdao/contracts/NounsAuctionHouse.sol::233 => nouns.transferFrom(address(this), _auction.bidder, _auction.nounId);

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::249 => IERC20(weth).transfer(to, amount);

2022-08-nounsdao/contracts/test/WETH.sol::36 => payable(msg.sender).transfer(wad);

2022-08-nounsdao/test/foundry/DescriptorUpgradeViaProposal.t.sol::28 => nounsToken.transferFrom(minter, tokenHolder, 1);
```

## 2. Unspecific Compiler Version Pragma (18 instances) - Completed

**Avoid floating pragmas for non-library contracts.** While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations. A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain. It is recommended to pin to a concrete compiler version.

```solidity
2022-08-nounsdao/contracts/Inflator.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsArt.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsAuctionHouse.sol::25 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsDescriptor.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsDescriptorV2.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsSeeder.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/NounsToken.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/SVGRenderer.sol::18 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/base/ERC721.sol::33 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/base/ERC721Checkpointable.sol::35 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/base/ERC721Enumerable.sol::28 => pragma solidity ^0.8.0;

2022-08-nounsdao/contracts/external/opensea/IProxyRegistry.sol::3 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOExecutor.sol::31 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOInterfaces.sol::33 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV1.sol::61 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOLogicV2.sol::53 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOProxy.sol::36 => pragma solidity ^0.8.6;

2022-08-nounsdao/contracts/governance/NounsDAOProxyV2.sol::39 => pragma solidity ^0.8.6;
```

## 3. Potential flaws in the binary search algorithm (1 instance)

Comment on two things regarding the binary search algorithm:

1. The array `quorumParamsCheckpoints` must be sorted for binary search algorithm to work. The NatSpec says "The checkpoints array must not be empty, and the block number must be higher than or equal to the block of the first checkpoint", but it does not emphasis the fact that `quorumParamsCheckpoints` must be sorted.
2. Customized binary search algorithm is very likely to fail on some strange scenarios, thus it is recommend to use a library instead of writing custom code. OpenZeppelin has a binary search implementation at https://github.com/OpenZeppelin/openzeppelin-contracts/blob/v4.6.0/contracts/utils/Arrays.sol.

```solidity
File: 2022-08-nounsdao/governance/NounsDAOLogicV2.sol, line 922

function getDynamicQuorumParamsAt(uint256 blockNumber_) public view returns (DynamicQuorumParams memory) {
    uint32 blockNumber = safe32(blockNumber_, 'NounsDAO::getDynamicQuorumParamsAt: block number exceeds 32 bits');
    uint256 len = quorumParamsCheckpoints.length;

    if (len == 0) {
        return
            DynamicQuorumParams({
                minQuorumVotesBPS: safe16(quorumVotesBPS),
                maxQuorumVotesBPS: safe16(quorumVotesBPS),
                quorumCoefficient: 0
            });
    }

    if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {
        return quorumParamsCheckpoints[len - 1].params;
    }

    if (quorumParamsCheckpoints[0].fromBlock > blockNumber) {
        return
            DynamicQuorumParams({
                minQuorumVotesBPS: safe16(quorumVotesBPS),
                maxQuorumVotesBPS: safe16(quorumVotesBPS),
                quorumCoefficient: 0
            });
    }

    uint256 lower = 0;
    uint256 upper = len - 1;
    while (upper > lower) {
        uint256 center = upper - (upper - lower) / 2;
        DynamicQuorumParamsCheckpoint memory cp = quorumParamsCheckpoints[center];
        if (cp.fromBlock == blockNumber) {
            return cp.params;
        } else if (cp.fromBlock < blockNumber) {
            lower = center;
        } else {
            upper = center - 1;
        }
    }
    return quorumParamsCheckpoints[lower].params;
}
```
