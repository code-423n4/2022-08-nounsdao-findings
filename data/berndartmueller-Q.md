# QA Report

- [Low Risk](#low-risk)
  - [[L-01] Restrict ETH funds receivable by contract](#l-01-restrict-eth-funds-receivable-by-contract)
  - [[L-02] Proposals with certain states can be canceled](#l-02-proposals-with-certain-states-can-be-canceled)

## Low Risk

### [L-01] Restrict ETH funds receivable by contract

#### Description

Native ETH fund transfers to contracts should be, if possible, limited to a limited set of addresses to prevent accidental native fund transfers from other sources.

#### Findings

[NounsDAOLogicV2.sol#L1030](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L1030)

```solidity
receive() external payable {}
```

#### Recommended mitigation steps

Modify the `receive()` function to only accept transfers from expected addresses.

### [L-02] Proposals with certain states can be canceled

#### Description

Governance proposals which have not been executed so far could be canceled by the proposal proposer or by anyone if the proposer delegates dropped below the proposal threshold.

However, the current validation to prevent canceling executed proposals does not prevent canceling proposals with the following states:

- `ProposalState.Defeated`
- `ProposalState.Expired`
- `ProposalState.Vetoed`

Canceling proposals with one of these states will hide the "real" and previous state of the proposal and could lead to confusion.

#### Findings

[NounsDAOLogicV2.sol#L347](https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L347)

```solidity
function cancel(uint256 proposalId) external {
  require(state(proposalId) != ProposalState.Executed, 'NounsDAO::cancel: cannot cancel executed proposal');

  [..]
}
```

#### Recommended mitigation steps

Consider preventing canceling proposals with the above-mentioned states.
