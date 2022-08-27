# Summary
## Low
[L01] A floating pragma is set.
[L02] A different pragma is set.
[L03] Non reentrancy guard

## Non Critical
[NC01] Use of block timestamp

# Low
## [L01] A floating pragma is set.
### Description
The current pragma Solidity directive is "^0.8.6". It is recommended to specify a fixed compiler version to ensure that the bytecode produced 
does not vary between builds. 
This is especially important if you rely on bytecode-level verification of the code.

### Mitigation
Lock the pragma version

### Lines in the code
[NounsDAOLogicV1.sol#L61](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L61)
[NounsDAOLogicV2.sol#L53](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L53)
[NounsDAOInterfaces.sol#L33](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOInterfaces.sol#L33)
[NounsDAOProxy.sol#L36](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L36)
[ERC721Checkpointable.sol#L35](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L35)
[ERC721Enumerable.sol#L28](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L28)

## [L02] A different pragma is set.
### Description
The current pragma Solidity directive is "^0.8.6" but there is other contract ERC721Enumerable that is using a different pragma version "^0.8.0".
It's cleaner to use the same versions.

### Lines in the code
[ERC721Enumerable.sol#L28](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L28)

## [L03] Non reentrancy guard
### Description
A reentrancy in this case depend on the code of "implementation" in _fallback (NounsDAOProxy.sol) function but it's important to have a reentrancy guard 
in external functions when have an external call so fallback() and receive() should be protected.

### Recommendation
It is recommended to follow the good security practices and apply necessary reentrancy prevention by utilizing the nonReentrant modifier 
from [Openzeppelin Library](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/security/ReentrancyGuard.sol) to block possible re-entrancy.

### Lines in the code
[NounsDAOProxy.sol#L130-L132](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L130-L132)
[NounsDAOProxy.sol#L138-L140](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L138-L140)
[NounsDAOProxy.sol#L108](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L108)

# Non Critical
## [NC01] Use of block timestamp
### Description
Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), 
locking funds for periods of time, and various state-changing conditional statements that are time-dependent. 
Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

### Recommendation
Block timestamps should not be used for entropy or generating random numbers—i.e., they should not be the deciding factor 
(either directly or through some derivation) for winning a game or changing an important state.

Time-sensitive logic is sometimes required; e.g., for unlocking contracts (time-locking), completing an ICO after a few weeks, 
or enforcing expiry dates. It is sometimes recommended to use block.number and an average block time to estimate times; 
with a 10 second block time, 1 week equates to approximately, 60480 blocks. Thus, specifying a block number at which to change 
a contract state can be more secure, as miners are unable to easily manipulate the block number.

### Lines in the code
[NounsDAOLogicV1.sol#L280](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L280)
[NounsDAOLogicV1.sol#L438](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L438)
[NounsDAOLogicV2.sol#L291](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L291)
[NounsDAOLogicV2.sol#L449](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L449)
[ERC721Checkpointable.sol#L142](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L142)


