the function ***_beforeTokenTransfer*** from ***contracts/base/ERC721Enumerable.sol*** could be eliminated, and the four checks every transfer will be optimized. this function is called three times, all in ***contracts/base/ERC721.sol*** , the three calls could be eliminated(line 329, 251 and 381),  if we define in contracts/base/ERC721Enumerable.sol 

    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal virtual override{
        if (to != from) {
            _removeTokenFromOwnerEnumeration(from, tokenId);
            _addTokenToOwnerEnumeration(to, tokenId);
        }
        super._transfer(from, to, tokenId);
    }

    function _mint(
        address creator,
        address to,
        uint256 tokenId
    ) internal virtual override{
        _addTokenToAllTokensEnumeration(tokenId);
        _addTokenToOwnerEnumeration(to, tokenId);
        super._mint(creator, to, tokenId);
    }

    function _burn(uint256 tokenId) internal virtual override{
        _removeTokenFromOwnerEnumeration(ownerOf(tokenId), tokenId);
        _removeTokenFromAllTokensEnumeration(tokenId);
        super._burn(tokenId);
        
    }

because in mint, burn and transfer we know if the from and to are the 0 address, so its not necessary to check at runtime(because it's not allowed transfer to the 0 address or mint to the 0 address in the ERC721.sol contract)