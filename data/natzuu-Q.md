##  PRAGMA VERSION

In the contracts, floating pragmas should not be used. Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.

### Proof of Concept

[https://swcregistry.io/docs/SWC-103](https://swcregistry.io/docs/SWC-103)

```markdown
ERC721Checkpointable.sol::35
ERC721Enumerable.sol::28
NounsDAOInterfaces.sol::33
NounsDAOLogicV1.sol::61
NounsDAOLogicV2.sol::53
NounsDAOProxy.sol::36
```