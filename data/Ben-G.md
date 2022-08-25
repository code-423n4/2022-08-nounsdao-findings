# Use calldata instead of memory for read only arguments in external functions.
Will save at least 60 gas for every element in each memory array.
```
NounsDAOLogicV2.sol
184:  function propose(
        address[] memory targets,
        uint256[] memory values,
        string[] memory signatures,
        bytes[] memory calldatas,
        string memory description
    )
```
# Use unchecked { ++i; } instead of i++ in for loops for gas savings
Saves roughly 30-40 gas per loop iteration
```
NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```

# Don't initialize storage or memory variables with default values
Explicitly initializing a storage struct field with it's default value costs a lot of unnecesary gas, 2200 per instance.
```
NounsDAOLogicV2.sol
231: newProposal.eta = 0;
```
Lines 238-243
```
newProposal.forVotes = 0;
newProposal.againstVotes = 0;
newProposal.abstainVotes = 0;
newProposal.canceled = false;
newProposal.executed = false;
newProposal.vetoed = false;
```
Explicitly initializing a memory variable with it's default value will also consume unnecessary gas
```
NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
1111: uint256 lower = 0;
```
```
contract/base/ERC721Checkpointable.sol
221: uint32 lower = 0;
```

# When iterating through an array in a for loop, cache the array length to save 97 gas per loop iteration.
```
NounsDAOLogicV2.sol
292: for (uint256 i = 0; i < proposal.targets.length; i++) {
330: for (uint256 i = 0; i < proposal.targets.length; i++) {
357: for (uint256 i = 0; i < proposal.targets.length; i++) {
382: for (uint256 i = 0; i < proposal.targets.length; i++) {
```
Modification for each:
```
uint256 length = proposal.targets.length;
for (uint256 i = 0; i < length; i++) {
```
# Cache storage variables that are accessed again in the same function.
Will save 97 gas on each subsequent variable access.
```
NounsDAOLogicV2.sol, lines 266-268
proposalCount++;
Proposal storage newProposal = _proposals[proposalCount];
newProposal.id = proposalCount;
```
Change to:
```
uint256 newProposalId = ++proposalCount;
Proposal storage newProposal = _proposals[newProposalId];
newProposal.id = newProposalId;
```
Saves 191 gas
#### For rest of propose function
Replace each instance of newProposal.id with cached newProposalId variable.
Replace newProposal.startBlock with temp.startBlock.
Replace newProposal.endBlock with temp.endBlock.
Replace newProposal.proposalThreshold with temp.proposalThreshold.
Replace newProposal.proposer with msg.sender.
Saves 97 gas per instance.
