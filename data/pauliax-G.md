* Repeated casting to ```safe16(quorumVotesBPS)``` could be cached:
```solidity
  if (len == 0) {
      return
          DynamicQuorumParams({
              minQuorumVotesBPS: safe16(quorumVotesBPS),
              maxQuorumVotesBPS: safe16(quorumVotesBPS),
              quorumCoefficient: 0
          });
  }
  ...
  if (quorumParamsCheckpoints[0].fromBlock > blockNumber) {
      return
          DynamicQuorumParams({
              minQuorumVotesBPS: safe16(quorumVotesBPS),
              maxQuorumVotesBPS: safe16(quorumVotesBPS),
              quorumCoefficient: 0
          });
  }
```

* domainSeparator will likely not change unless a hard fork or something like that occurs, so consider caching this value to reduce gas costs:
```solidity
  bytes32 domainSeparator = keccak256(
      abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
  );
```
An elegant solution that you may consider applying is from Sushi Trident: https://github.com/sushiswap/trident/blob/concentrated/contracts/pool/concentrated/TridentNFT.sol#L47-L62

* Unchecked keyword can be used where overflow/underflow is not possible, e.g. here ```len - 1``` will never underflow:
```solidity
  if (len == 0) {
      return ...
  }
  if (quorumParamsCheckpoints[len - 1].fromBlock <= blockNumber) {
      return quorumParamsCheckpoints[len - 1].params;
  }
```