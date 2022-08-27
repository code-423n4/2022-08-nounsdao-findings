### Don't Initialize Variables with Default Value

Uninitialized variables are assigned with the types default value.

Explicitly initializing a variable with it's default value costs unnecesary gas.


```
File: contracts/base/ERC721Checkpointable.sol

181:     uint32 lower = 0;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol#L181

```
File: contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol

```
File: contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

948:        uint256 lower = 0;
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol


### `<ARRAY>.LENGTH` SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A `FOR`-LOOP

The overheads outlined below are PER LOOP, excluding the first loop

storage arrays incur a Gwarmaccess (100 gas)
memory arrays use `MLOAD` (3 gas)
calldata arrays use `CALLDATALOAD` (3 gas)
Caching the length changes each of these to a `DUP<N>` (3 gas), and gets rid of the extra `DUP<N>` needed to store the stack offset


```
File: contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol

```
File: contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol

### `++I`/`I++` SHOULD BE `UNCHECKED{++I}`/`UNCHECKED{I++}` WHEN IT IS NOT POSSIBLE FOR THEM TO OVERFLOW, AS IS THE CASE WHEN USED IN `FOR`- AND `WHILE`-LOOPS

The `unchecked` keyword is new in solidity version 0.8.0, so this only applies to that version or higher, which these instances are. This saves 30-40 gas [PER LOOP](https://gist.github.com/hrkrshnn/ee8fabd532058307229d65dcd5836ddc#the-increment-in-for-loop-post-condition-can-be-made-unchecked)

```
File: contracts/governance/NounsDAOLogicV1.sol

281:        for (uint256 i = 0; i < proposal.targets.length; i++) {

319:        for (uint256 i = 0; i < proposal.targets.length; i++) {

346:        for (uint256 i = 0; i < proposal.targets.length; i++) {

371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV1.sol

```
File: contracts/governance/NounsDAOLogicV2.sol

292:        for (uint256 i = 0; i < proposal.targets.length; i++) {

330:        for (uint256 i = 0; i < proposal.targets.length; i++) {

357:        for (uint256 i = 0; i < proposal.targets.length; i++) {

382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

### Use != 0 instead of > 0 for Unsigned Integer Comparison

When dealing with unsigned integer types, comparisons with `!= 0` are cheaper then with `> 0`.

```
File: contracts/base/ERC721Checkpointable.sol

153:        return nCheckpoints > 0 ? checkpoints[account][nCheckpoints - 1].votes : 0;

215:        if (srcRep != dstRep && amount > 0) {

218:                uint96 srcRepOld = srcRepNum > 0 ? checkpoints[srcRep][srcRepNum - 1].votes : 0;

225:                uint96 dstRepOld = dstRepNum > 0 ? checkpoints[dstRep][dstRepNum - 1].votes : 0;

243:        if (nCheckpoints > 0 && checkpoints[delegatee][nCheckpoints - 1].fromBlock == blockNumber) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/base/ERC721Checkpointable.sol

```
File: contracts/governance/NounsDAOLogicV2.sol

541:        if (votes > 0) {

967:        if (pos > 0 && quorumParamsCheckpoints[pos - 1].fromBlock == blockNumber) {
```
https://github.com/code-423n4/2022-08-nounsdao/blob/c1c7c6201d0247f92472419ff657b570f9104565/contracts/governance/NounsDAOLogicV2.sol
