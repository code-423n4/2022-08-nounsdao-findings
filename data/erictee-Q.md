### [L-01] Avoid floating pragmas for non-library contracts.


#### Impact
While floating pragmas make sense for libraries to allow them to be included with multiple different versions of applications, it may be a security risk for application implementations.

A known vulnerable compiler version may accidentally be selected or security tools might fall-back to an older compiler version ending up checking a different EVM compilation that is ultimately deployed on the blockchain.

It is recommended to pin to a concrete compiler version.

#### Findings:
```
contracts/base/ERC721Enumerable.sol:L28     pragma solidity ^0.8.0;

contracts/base/ERC721Checkpointable.sol:L35     pragma solidity ^0.8.6;

contracts/governance/NounsDAOProxy.sol:L36     pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV2.sol:L53     pragma solidity ^0.8.6;

contracts/governance/NounsDAOInterfaces.sol:L33     pragma solidity ^0.8.6;

contracts/governance/NounsDAOExecutor.sol:L31     pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV1.sol:L61     pragma solidity ^0.8.6;

```
