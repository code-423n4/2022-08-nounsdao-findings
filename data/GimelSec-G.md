# Save gas in for loops by unchecked arithmetic

The for loop has no overflow risk of `i`. Use an unchecked block to save gas.

## Proof of Concept

```
contracts/governance/NounsDAOLogicV2.sol
292:        for (uint256 i = 0; i < proposal.targets.length; i++) {
330:        for (uint256 i = 0; i < proposal.targets.length; i++) {
357:        for (uint256 i = 0; i < proposal.targets.length; i++) {
382:        for (uint256 i = 0; i < proposal.targets.length; i++) {

contracts/governance/NounsDAOLogicV1.sol
281:        for (uint256 i = 0; i < proposal.targets.length; i++) {
319:        for (uint256 i = 0; i < proposal.targets.length; i++) {
346:        for (uint256 i = 0; i < proposal.targets.length; i++) {
371:        for (uint256 i = 0; i < proposal.targets.length; i++) {
```

## Recommendation

Use `unchecked` blocks to avoid overflow checks, or use `++i` rather than `i++` if you don't use unchecked blocks.

```
for (uint256 i = 0; i < length; ) {
    ...
    unchecked {
        ++i;
    }
}
```

# `queueOrRevertInternal` internal function which is only used once can be inlined

The internal function `queueOrRevertInternal` which is only used once can be inlined to save gas.

## Proof of Concept

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L305

## Recommendation

Remove the internal function and add these code inlined.
