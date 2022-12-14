# [Low] Nonce Not Used For `castVoteBySig`

The `castVoteBySig` function is vulnerable to replay attacks. Users could submit the signature to the `castVoteBySig` function multiple times, and the signature will be processed successfully.

However, due to the validation check implemented within the `castVoteInternal` function, the function will revert if the user attempts to vote for a proposal more than once. Thus, this issue has been marked as "Low" risk. However, it is still recommended to add in nonce to ensure that the signature cannot be replayed as a defense in-depth measure.

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV1.sol#L472

```solidity
function castVoteBySig(
    uint256 proposalId,
    uint8 support,
    uint8 v,
    bytes32 r,
    bytes32 s
) external {
    bytes32 domainSeparator = keccak256(
    abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
    );
    bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
    bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
    address signatory = ecrecover(digest, v, r, s);
    require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
    emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
}
```

https://github.com/code-423n4/2022-08-nounsdao/blob/452695d4764ba9d5e1d3eef0d5ecca3d004f215a/contracts/governance/NounsDAOLogicV2.sol#L564

```solidity
function castVoteBySig(
    uint256 proposalId,
    uint8 support,
    uint8 v,
    bytes32 r,
    bytes32 s
) external {
bytes32 domainSeparator = keccak256(
    abi.encode(DOMAIN_TYPEHASH, keccak256(bytes(name)), getChainIdInternal(), address(this))
    );
    bytes32 structHash = keccak256(abi.encode(BALLOT_TYPEHASH, proposalId, support));
    bytes32 digest = keccak256(abi.encodePacked('\x19\x01', domainSeparator, structHash));
    address signatory = ecrecover(digest, v, r, s);
    require(signatory != address(0), 'NounsDAO::castVoteBySig: invalid signature');
    emit VoteCast(signatory, proposalId, support, castVoteInternal(signatory, proposalId, support), '');
}
```



# [Low] Proposal Can Be Vetoed

Within the Nouns Governance, Nounders have the veto right to reject any proposal submitted. The Nounders are believed to act in good faith and only reject a proposal that is harmful to the Nouns DAO. However, users should be aware that nothing is permanent and this might change in the future unless the veto right has been burned. It is possible that a proposal that puts the Nounders in a disadvantageous position might be rejected by them. Therefore, users should take note of this risk and perform their own due diligence.

https://github.com/code-423n4/2022-08-nounsdao/blob/45411325ec14c6d747b999a40367d3c5109b5a89/contracts/governance/NounsDAOLogicV2.sol#L374

```solidity
/**
 * @notice Vetoes a proposal only if sender is the vetoer and the proposal has not been executed.
 * @param proposalId The id of the proposal to veto
 */
function veto(uint256 proposalId) external {
	require(vetoer != address(0), 'NounsDAO::veto: veto power burned');
	require(msg.sender == vetoer, 'NounsDAO::veto: only vetoer');
	require(state(proposalId) != ProposalState.Executed, 'NounsDAO::veto: cannot veto executed proposal');

	Proposal storage proposal = _proposals[proposalId];

	proposal.vetoed = true;
	for (uint256 i = 0; i < proposal.targets.length; i++) {
		timelock.cancelTransaction(
			proposal.targets[i],
			proposal.values[i],
			proposal.signatures[i],
			proposal.calldatas[i],
			proposal.eta
		);
	}

	emit ProposalVetoed(proposalId);
}
```



# [Low] Floating Pragma

The contract makes use of the floating-point pragma ^0.8.6. Contracts should be deployed using the same compiler version. Locking the pragma helps ensure that contracts are not unintentionally deployed using another pragma, such as an obsolete version, that may introduce issues in the contract system. Consider locking the pragma version. It is advised that floating pragma should not be used in production

```
pragma solidity ^0.8.6;
```

Affected Contracts:

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol



# [Low] Consider a two-phase veto right transfer

Vetoer calls `_setVetoer` function to transfer the veto right to the new address directly. As such, there is a risk that the veto right is transferred to an invalid address, thus accidentally burning the veto right. Consider implementing a two-step process where the vetoer nominates an account, and the nominated account needs to call an `acceptVetoRight()` function for the transfer of veto right to fully succeed. This ensures the nominated EOA account is a valid and active account.

```solidity
    function _setVetoer(address newVetoer) public {
        require(msg.sender == vetoer, 'NounsDAO::_setVetoer: vetoer only');

        emit NewVetoer(vetoer, newVetoer);

        vetoer = newVetoer;
    }
```