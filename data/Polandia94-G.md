In the contracts/base/ERC721Enumerable.sol the function **_beforeTokenTransfer** could be changed to consume less gas. if the condition 
**from == address(0)** is true, it's not necessary to check if **to == address(0)* because it's always false and **to != from** because it's always true. This is because its not possible to mint to the 0 address because _mint function(in contracts/base/ERC721.sol) have: **require(to != address(0), 'ERC721: mint to the zero address');**(
so the function could be:
        super._beforeTokenTransfer(from, to, tokenId);
        if (from == address(0)) {
            _addTokenToAllTokensEnumeration(tokenId);
            _addTokenToOwnerEnumeration(to, tokenId); // when is minted always to !=from
        } else{ 
            if (from != to) {
            _removeTokenFromOwnerEnumeration(from, tokenId);
            }
            if (to == address(0)) {
                _removeTokenFromAllTokensEnumeration(tokenId);
            } else if (to != from) {
                _addTokenToOwnerEnumeration(to, tokenId);
            }
        }
There are two if checks less every mint