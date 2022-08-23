## Floating Point Variable
All 6 files in scope contain `pragma solidity ^0.8.6`, which uses the `^` floating point variable. This exposes the possibility to be compiled at a higher version than intended, introducing undiscovered risks in compiler versions.
### Mitigation
Use `pragma solidity 0.8.6` instead to prevent potential issues.