# [L-01] `initialize` functions can be frontrun

The `initialize` function that initializes important contract state can be called by anyone.  
See:
- NounsDAOLogicV2.initialize
- NounsDAOLogicV1.initialize

An unauthorized party can initialize the contract before the legitimate deployer, hoping that the victim continues to use the same contract.  
In the best case for the victim, they notice it and have to redeploy their contract costing gas.

## Recommended Mitigation Steps

For initializing proxy contracts, deploy contracts using a factory contract that immediately calls `initialize` after deployment or make sure to call it immediately after deployment and verify the transaction succeeded.