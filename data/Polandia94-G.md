the function ***_beforeTokenTransfer*** from ***contracts/base/ERC721Enumerable.sol***  is called in three places, if we move the logic from ***_beforeTokenTransfer*** to ***_mint***, ***_transfer***, ***_burn***  four checks on every transfer will be eliminated.
 
we need to  remove lines 81-113 from  ***contracts/base/ERC721Enumerable.sol*** and replace with

```solidity
 
    function _transfer(
        address from,
        address to,
        uint256 tokenId
    ) internal virtual override{
        if (to != from) {
            _removeTokenFromOwnerEnumeration(from, tokenId); // because to and from are != 0 and to !=from 
            _addTokenToOwnerEnumeration(to, tokenId); // because to and from are != 0 and to !=from 
        }
        super._transfer(from, to, tokenId);
    }

    function _mint(
        address creator,
        address to,
        uint256 tokenId
    ) internal virtual override{
        _addTokenToAllTokensEnumeration(tokenId); // because from == 0
        _addTokenToOwnerEnumeration(to, tokenId); // because to to !=from, to can't be 0
        super._mint(creator, to, tokenId);
    }

    function _burn(uint256 tokenId) internal virtual override{
        _removeTokenFromOwnerEnumeration(ownerOf(tokenId), tokenId); / because to to !=from, from can't be 0
        _removeTokenFromAllTokensEnumeration(tokenId); // because to == 0
        super._burn(tokenId);
        
    }
```

because in mint, burn and transfer we know if the from and to are the 0 address, so its not necessary to check at runtime(because it's not allowed transfer to the 0 address or mint to the 0 address in the ERC721.sol contract)