# Gas Report

- [G-01: use `++i` instead of `i++` to save gas](#g-01-use-i-instead-of-i-to-save-gas)
- [G-02: use `unchecked` when incrementing the loop iterator](#g-02-use-unchecked-when-incrementing-the-loop-iterator)
- [G-03: dont assign zero values explicitly](#g-03-dont-assign-zero-values-explicitly)
- [G-04: msg.sender can't be `address(0)`](#g-04-msgsender-cant-be-address0)
- [G-05: use `calldata` instead of `memory` if you don't mutate a function parameter](#g-05-use-calldata-instead-of-memory-if-you-dont-mutate-a-function-parameter)

## G-01: use `++i` instead of `i++` to save gas

```
./contracts/NounsDescriptor.sol:110:        for (uint256 i = 0; i < newColors.length; i++) {
./contracts/NounsDescriptor.sol:120:        for (uint256 i = 0; i < _backgrounds.length; i++) {
./contracts/NounsDescriptor.sol:130:        for (uint256 i = 0; i < _bodies.length; i++) {
./contracts/NounsDescriptor.sol:140:        for (uint256 i = 0; i < _accessories.length; i++) {
./contracts/NounsDescriptor.sol:150:        for (uint256 i = 0; i < _heads.length; i++) {
./contracts/NounsDescriptor.sol:160:        for (uint256 i = 0; i < _glasses.length; i++) {
./contracts/SVGRenderer.sol:111:            for (uint256 i = 0; i < image.draws.length; i++) {
./contracts/libs/MultiPartRLEToSVG.sol:90:            for (uint256 i = 0; i < image.rects.length; i++) {
./contracts/NounsArt.sol:131:        for (uint256 i = 0; i < _backgrounds.length; i++) {
./contracts/NounsArt.sol:432:        for (uint256 i = 0; i < len; i++) {
./contracts/governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:226:        proposalCount++;
./contracts/governance/NounsDAOLogicV1.sol:216:        proposalCount++;
```

## G-02: use `unchecked` when incrementing the loop iterator

The iterator can't overflow in most cases because of the loop's condition. Using `unchecked` will save gas.

```sol
for (uint i; i < length;) {

    unchecked {++i}
}
```

```
./contracts/NounsDescriptor.sol:110:        for (uint256 i = 0; i < newColors.length; i++) {
./contracts/NounsDescriptor.sol:120:        for (uint256 i = 0; i < _backgrounds.length; i++) {
./contracts/NounsDescriptor.sol:130:        for (uint256 i = 0; i < _bodies.length; i++) {
./contracts/NounsDescriptor.sol:140:        for (uint256 i = 0; i < _accessories.length; i++) {
./contracts/NounsDescriptor.sol:150:        for (uint256 i = 0; i < _heads.length; i++) {
./contracts/NounsDescriptor.sol:160:        for (uint256 i = 0; i < _glasses.length; i++) {
./contracts/NounsArt.sol:131:        for (uint256 i = 0; i < _backgrounds.length; i++) {
./contracts/NounsArt.sol:432:        for (uint256 i = 0; i < len; i++) {
./contracts/governance/NounsDAOLogicV2.sol:292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV2.sol:382:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
./contracts/governance/NounsDAOLogicV1.sol:371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## G-03: dont assign zero values explicitly

It's just a waste of gas, the zero value is assigned by default.

- https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L227-L245

## G-04: msg.sender can't be `address(0)`

It's a waste to check whether `msg.sender != address(0)`

- https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L375
- https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L819

## G-05: use `calldata` instead of `memory` if you don't mutate a function parameter

```
./contracts/governance/NounsDAOLogicV2.sol:185:        address[] memory targets,
./contracts/governance/NounsDAOLogicV2.sol:186:        uint256[] memory values,
./contracts/governance/NounsDAOLogicV2.sol:187:        string[] memory signatures,
./contracts/governance/NounsDAOLogicV2.sol:188:        bytes[] memory calldatas,
./contracts/governance/NounsDAOLogicV2.sol:189:        string memory description
./contracts/governance/NounsDAOLogicV2.sol:308:        string memory signature,
./contracts/governance/NounsDAOLogicV2.sol:309:        bytes memory data,
./contracts/governance/NounsDAOLogicV2.sol:536:        string memory reason
```

