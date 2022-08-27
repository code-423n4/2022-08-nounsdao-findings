- In function `NounsDAOLogicV2.propose()` , no need to have assignments at LOC 231, 238, 239, 240, 241, 242, 243 because that values to be assigned are also storage's default values
- The `for` loops in `NounsDAOLogicV2.queue()`, `NounsDAOLogicV2.execute()`,  `NounsDAOLogicV2.cancel()`, `NounsDAOLogicV2.veto()` could be implemented like this
    ```
    uint256 length = proposal.targets.length;
    for(uint256 i; i < length; ){
        ...
    unchecked { ++i; }
    }
    ```
- https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L268 could use `unchecked` for this instruction since the next line we have check for arithmetic error. 
Similarly, https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/base/ERC721Checkpointable.sol#L279 could use `unchecked`
However, the function we could remove functions `add96` and `sub96` if the error messages are not necessary because with compiler above 0.8.0, the check for overflow/underflow is default