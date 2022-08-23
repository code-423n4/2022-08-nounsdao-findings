
## MISSING INITIALIZER MODIFIER ON `initialize()` function

OpenZeppelin recommends that the `initializer`modifier be applied to `initialize()` in order to avoid potential griefs, social engineering, or exploits. Ensure that the modifier is applied to the implementation contract.

2 instances:
-NounsDAOLogicV1.sol lines 113
-NounsDAOLogicV2.sol lines 124

## 2**<N> - 1 SHOULD BE RE-WRITTEN AS TYPE(UINT<N>).MAX

Instead of `2**32` write `type(uint32).max`
Instead of `2**96` write `type(uint96).max`

There are 2  instance of this issue:
-ERC721Checkpointable.sol lines 254, 259.

## EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT

There are 6 instances of this issue:
-ERC721Checkpointable.sol lines 59, 63.
-NounsDAOLogicV1.sol lines 97, 101, 
-NounsDAOLogicV2.sol lines 102, 105

# TYPOS
Two Instaces:
-NounsDAOLogicV1.sol line 104 ‘contructor’, 
-NounsDAOLogicV2.sol line 115 ‘contructor’,

# RECEIVE() FUNCTION WILL LOCK ETHER IN CONTRACT
If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

There is 1 instances of this issue:
-NounsDAOLogicV2.sol line 1030
