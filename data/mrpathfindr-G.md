

G012 - Use Prefix Increment instead of Postfix Increment if possible
---

Instances Include:

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L292

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371


Mitigation:


Do this

```
for (uint256 i = 0; i < borrowersLength; ++i) {
```
Instead of this
```
for (uint256 i = 0; i < _borrowers.length; i++) {
```



G002 - Cache Array Length Outside of Loop
---


Instances Include:   https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L371


https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L281


https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV1.sol#L319



Do this
```
Uint proposalLength =  proposal.targets.length

        for (uint256 i = 0; i < proposalLength; i++) {
            timelock.executeTransaction(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                proposal.eta
            );
        }

```
Instead of this 
```
        for (uint256 i = 0; i < proposal.targets.length; i++) {
            timelock.executeTransaction(
                proposal.targets[i],
                proposal.values[i],
                proposal.signatures[i],
                proposal.calldatas[i],
                proposal.eta
            );
        }

```





G003 - Use != 0 instead of > 0 for Unsigned Integer Comparison
---




Instances Include: 

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L541


Mitigation 

Do this:


```
    if (votes  != 0 {
            _refundGas(startGas);
        }
```

Intead of this:

```     
 if (votes > 0) {
            _refundGas(startGas);
        }

```
