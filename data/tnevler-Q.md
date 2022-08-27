# Report 
## Non-Critical Issues ##

### [N-01]: NatSpec Is Missing ###

**Description:**
NatSpec if missing for the following function:

+ [NounsDAOLogicV2.queueOrRevertInternal](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L305)

+ [NounsDAOLogicV2._writeQuorumParamsCheckpoint](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L964)

+ [NounsDAOLogicV2._refundGas](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#974)

+ [NounsDAOLogicV2.min](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L988)

+ [NounsDAOLogicV2.bps2Uint](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1006)

+ [NounsDAOLogicV2.getChainIdInternal](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1010)

+ [NounsDAOLogicV2.safe32](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1018)

+ [NounsDAOLogicV2.safe16](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1023)

+ [NounsDAOLogicV1.queueOrRevertInternal](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L294)

+ [NounsDAOLogicV1.bps2Uint](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L672)

+ [NounsDAOLogicV1.getChainIdInternal](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L676)

**Recommendation:**
Implement NatSpec for all functions.


### [N-02]: Wrong order of functions ###
**Context:** 
[L1030](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030)

**Description:**
According [official solidity documentation](https://docs.soliditylang.org/en/v0.8.6/style-guide.html#order-of-functions) receive function must be placed after the constructor.

**Recommendation:**
Remove the receive function from [L1030](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030) and add it to [L167](https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L167).


### [N-03]: Comstant instead of unknown divisor ###
**Context:** 
[L1007](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1007)

**Description:**
You should use constant variables to make the code easier to understand and maintain.

**Recommendation:**
Define constant variables for the divisor.
