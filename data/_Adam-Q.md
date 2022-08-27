### [L01] Unlocked Pragma
**Description:**
Non Library/Interface contracts should be deployed with a locked pragma version. This prevents the contract being deployed with a version that wasn't thoroughly tested against in development.

**LOC:**
[NounsDAOLogicV1.sol#L61](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L61)
[NounsDAOLogicV2.sol#L53](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L53)
[NounsDAOProxy.sol#L36](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOProxy.sol#L36)
[ERC721Checkpointable.sol#L35](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L35)
[ERC721Enumerable.sol#L28](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Enumerable.sol#L28)

**Recommendation:**
Lock the pragma the version that was used in testing.


### [N01] Incorrect Comment
**Description:**
A comment has been copy and pasted from a previous function and doesnt match the code it is referencing.

**LOC:**
[NounsDAOLogicV2.sol#L852](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L852)

**Recommendation:**
Delete/update to correct comment.


### [N02] Typos
**Description:**
There is a typo in the comment, 4,000 is written where 6,000 was intended.

**LOC:**
[NounsDAOLogicV2.sol#L86](https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L86) 

**Recommendation:**
Change to // 6,000 basis points or 60%