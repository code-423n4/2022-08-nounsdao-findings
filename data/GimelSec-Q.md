# Summary

We list 3 low-critical findings and 1 non-critical findings:
* (Low) quorumCoefficient has no cap
* (Low) `_withdraw` doesn't check `sent` and is reentrant
* (Low) floating pragma
* (Non) Using ecrecover is against best practice

# (Low) quorumCoefficient has no cap

## Impact

There is no cap for `quorumCoefficient`.

## Proof of Concept

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L726

## Recommended Mitigation Steps

Add limit in `_setQuorumCoefficient`

# (Low) `_withdraw` doesn't check `sent` and is reentrant

## Impact

`_withdraw` doesn't check `sent`. Caller will not receive ETH from the protocol if `sent` is false, and some functions after calling `_withdraw` may fail, or leading to cost caller's ETH rather than ETH from the protocol.

## Proof of Concept

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L783

## Recommended Mitigation Steps

Check `sent` should be true.

# (Low) floating pragma

## Impact

Floating pragma may cause unexpected compilation time behaviour and introduce unintended bugs.

## Proof of Concept

```
contracts/governance/NounsDAOProxy.sol
36:pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV2.sol
53:pragma solidity ^0.8.6;

contracts/governance/NounsDAOLogicV1.sol
61:pragma solidity ^0.8.6;

contracts/base/ERC721Enumerable.sol
28:pragma solidity ^0.8.0;

contracts/base/ERC721Checkpointable.sol
35:pragma solidity ^0.8.6;
```

## Recommended Mitigation Steps

Don't use `^`, lock pragma to ensure compiler version. e.g. `pragma solidity 0.8.0;`

# (Non) Using ecrecover is against best practice

## Impact

Using ecrecover is against best practice. Preferably use ECDSA.recover instead. EIP-2 still allows signature malleability for ecrecover(). Remove this possibility and make the signature unique. However it should be impossible to be a threat by now.

## Proof of Concept

```
contracts/governance/NounsDAOLogicV2.sol
576:        address signatory = ecrecover(digest, v, r, s);

contracts/governance/NounsDAOLogicV1.sol
484:        address signatory = ecrecover(digest, v, r, s);

contracts/base/ERC721Checkpointable.sol
139:        address signatory = ecrecover(digest, v, r, s);
```

## Recommended Mitigation Steps

Take these implementation into consideration

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/4a9cc8b4918ef3736229a5cc5a310bdc17bf759f/contracts/utils/cryptography/draft-EIP712.sol

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/draft-ERC20Permit.sol
