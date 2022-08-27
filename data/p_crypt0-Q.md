# [Informational] Import statement inconsistencies.

Imports ought to be specific contracts to maintain consistency with NounsArt.sol, NounsAuctionHouse.sol, etc.


## NounsDAOLogicV2.sol
Add 
````
import {NounsDAOStorageV2} from './NounsDAOInterfaces.sol';
import {NounsDAOEventsV2} from './NounsDAOInterfaces.sol';
````

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L56

## NounsDAOProxy.sol
Add
````
import {NounsDAOSProxyStorageV2} from './NounsDAOInterfaces.sol';
import {NounsDAOEventsV2} from './NounsDAOInterfaces.sol';
````
