# Low Risk

## [L-01] Floating pragma

It's a good practice to avoid the use of floating pragma. Code must be compiled with the same version it as been tested the most. It also avoids the use of any nightly builds which can have unexpected and unknown behaviors

6 instances:
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L53
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L61
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOInterfaces.sol#L33
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOProxy.sol#L36
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L35
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Enumerable.sol#L28

Consider replacing `^0.8.1xx` by `0.8.xx`

Low risk because the tremendous majority of the time there is any risk.

## [L-02] Missing check for address(0) when assigning value to address state variable.
Zero address checking is the best practice to prevent burning fee, unexpected exceptions or the redeployment of the contract. 

1 instances:

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOProxy.sol#L42-L46

Consider adding a modifier at the start of those function to check it.

*This is low risk because any risk can only result of an owner's error in calling an external function. This can easily be prevented.*


# Non Critical

## [N-01 ] Typo
4 instances
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol#L104
	- `delegator contructor -> delegate constructor`
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L115
	- `delegator contructor -> delegate constructor`
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L582
	- `caries -> carries`
- https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L848
	- `priviledges -> privileges`
