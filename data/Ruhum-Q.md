# Report

- [Low](#low)
  - [L-01: NounsDAOLogicV2 doesn't expose part of the proposal](#l-01-nounsdaologicv2-doesnt-expose-part-of-the-proposal)
- [Non-Critical](#non-critical)
  - [N-01: wrong comments](#n-01-wrong-comments)

# Low

## L-01: NounsDAOLogicV2 doesn't expose part of the proposal

The `ProposalCondensed` struct doesn't include the `calldatas`, `targets`, `values`, and `signatures` properties. The contract's `proposals()` function used to retrieve existing proposals externally only returns a `ProposalCondensed`. It makes it hard for someone to read the complete proposal externally.

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L462

# Non-Critical

## N-01: wrong comments

In `NounsDAOLogicV2._burnVetoPower()` there's a comment that's a left-over from a copy & pasting code:

- https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L852
