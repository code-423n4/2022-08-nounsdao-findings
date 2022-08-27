### [G01] Duplicate Require Statement
**Description:**
In NounsDAOLogicV2 the exact same require statement that is checked in burnVetoPower on line 853 is also checked in setVetoer on line 840. It is unnecessary to check that msg.sender == vetoer twice and can be removed from burnVetoPower. (Same for v1 just on line 651 instead)

**LOC:**
[NounsDAOLogicV2.sol#L853](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L853)
[NounsDAOLogicV1.sol#L651](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L651)

**Recommendation:**
Remove the duplicate require statement from burnVetoPower.


### [G02] For Loop Optimisations
**Description:**
When incrementing i in for loops there is no chance of overflow so unchecked can be used to save gas. You can also save a small amount of gas by replacing post increments with pre. It is also more expensive to loop over a state variable and you can save some gas by cacheing the value.

**LOC:**
[NounsDAOLogicV1.sol#L281-L289](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L281-L289)
[NounsDAOLogicV1.sol#L319-L327](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L319-L327)
[NounsDAOLogicV1.sol#L346-L354](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L346-L354)
[NounsDAOLogicV1.sol#L371-L379](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L371-L379)
[NounsDAOLogicV2.sol#L292-L300](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L292-L300)
[NounsDAOLogicV2.sol#L330-L338](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L330-L338)
[NounsDAOLogicV2.sol#L357-L365](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L357-L365)
[NounsDAOLogicV2.sol#L382-L390](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L382-L390)

**Recommendation:**
Change for loops from: 
```
for (uint256 i; i < variable.length; i++) {
} 
to 
uint256 len = variable.length;
(uint256 i; i < len;) {
	// for loop body
	unchecked { ++i; }
}
```


### [G03] Default Values for State Variables
**Description:**
When initialising state variables to their default value it is cheaper to just leave the value blank. 

**LOC:**
[ERC721Checkpointable.sol#L41](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L41)
[NounsDAOLogicV2.sol#L231](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L231)
[NounsDAOLogicV2.sol#L238-L243](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L238-L243)

**Recommendation:**
Remove = 0/false from the variable initialisations.


### [G04] Custom Errors
**Description:**
As your using a solidity version > 0.8.4 you can replace revert strings with cheaper custom errors. 

**LOC:**
There are 95 revert strings throughout the various contracts that can be replaced with custom errors.

**Recommendation:**
Replace revert strings with custom errors.


### [G05] Long Revert Strings
**Description:**
If you opt not to use custom errors keeping revert strings <= 32 bytes in length will save gas. 

**LOC:**
There are 87 revert strings > 32 bytes in length throughout the various contracts.

**Recommendation:**
Either replace with custom errors or shorten the revert strings to be < 32 bytes in length.