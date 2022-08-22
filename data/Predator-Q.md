Vulnerable Function:
function _addTokenToOwnerEnumeration(address to, uint256 tokenId) private {
uint256 length = ERC721.balanceOf(to);
_ownedTokens[to][length] = tokenId;
_ownedTokensIndex[tokenId] = length;
}


This is a bug because it results in the _ownedTokens mapping being incorrectly initialized. When the _addTokenToOwnerEnumeration function is called, the length variable is set to the wrong value, which means that the _ownedTokens mapping is not correctly initialized. This can lead to unexpected behavior when the contract is used.

How can this bug be fixed?

This bug can be fixed by setting the length variable to _allTokens.length instead of ERC721.balanceOf(to).