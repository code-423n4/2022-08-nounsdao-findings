## Anyone can create proposal without holding any NFT if they borrow NFT then create proposal then return it

https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L197-L200

```
        require(
            nouns.getPriorVotes(msg.sender, block.number - 1) > temp.proposalThreshold,
            'NounsDAO::propose: proposer votes below proposal threshold'
        );
```

1. Borrow Noun DAO NFT to have enough for passing proposalThreshold
2. Wait for 1 block
3. Create proposal, proposalThreshold will be passed since we are holding enough Noun DAO NFT
4. Return Noun DAO NFT


## Validate targets[i] != address(0) on proposal creation
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol#L184-L190

```
    function propose(
        address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    ) public returns (uint256) {
```

Each member of targets shouldn't be address(0) but it haven't been validated yet