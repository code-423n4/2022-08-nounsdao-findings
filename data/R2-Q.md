1. **Ecrecover**

**Description**: 
In some cases ecrecover can return a random address instead of 0 for an invalid signature
So a malisious user will be able to add vote without tokens

**Code**:
https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L576

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/base/ERC721Checkpointable.sol#L139

**How to fix**:
Receive additional variable (e.g. address voter) and check that the ecrecover returned value matches with 'voter'



2. **Not pausable**

**Description**: 
There is no way to pause all interactions with your DAO contracts. So if you find a critical vulnerability or your private keys are stolen, you may need to stop all requests to the contract immediately



3. **Only vetoer can change vetoer**

**Description**: 
If as a result of an error you set an incorrect address for vetoer, you can't change it anymore. And then malicious user can provide sybil attack

**How to fix**:
Admin should be able to change vetoer




4. **Admin account controlled by one private key**

**Description**: 
If a private key will be compromised, you will lose contracts control. And then malicious user can steel all funds

**How to fix**:
Use multisignature access to admin
