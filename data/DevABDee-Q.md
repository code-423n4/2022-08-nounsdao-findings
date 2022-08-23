# 1. Floating pragmas are considered a bad practice.

It is always recommended that `pragma` should be fixed (not floating) to the version that you are intending to deploy your contracts with. 
For more information Check [this](https://www.oreilly.com/library/view/mastering-blockchain-programming/9781839218262/d1250994-b952-4d5e-9cde-1b852c18b55f.xhtml) out

All the contracts have floating `pagma`, fix them to `0.8.6`:
```solidity
contracts/governance/NounsDAOLogicV1.sol:61: pragma solidity ^0.8.6;
contracts/governance/NounsDAOLogicV2.sol:53: pragma solidity ^0.8.6;
contracts/governance/NounsDAOInterfaces.sol:33: pragma solidity ^0.8.6;
contracts/governance/NounsDAOProxy.sol:36: pragma solidity ^0.8.6;
contracts/base/ERC721Checkpointable.sol:35: pragma solidity ^0.8.6;
contracts/base/ERC721Enumerable.sol:28: pragma solidity ^0.8.6;
```
