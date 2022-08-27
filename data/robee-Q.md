## Duplicates in array


        You allow in some arrays to have duplicates. Sometimes you assumes there are no duplicates in the array.
        
### Code instance

ERC721Enumerable._addTokenToAllTokensEnumeration pushed (tokenId) 



## Mult instead div in compares


To improve algorithm precision instead using division in comparison use multiplication in the following scenario:
        
        Instead a < b / c use a * c < b. 
    
In all of the big and trusted contracts this rule is maintained.
    
### Code instances:

        MultiPartRLEToSVG.sol, 128, chunk = string( abi.encodePacked( chunk, '<rect width="', buffer[i], '" height="10" x="', buffer[i + 1], '" y="', buffer[i + 2], '" fill="#', buffer[i + 3], '" />' ) );
        NounsAuctionHouse.sol, 110, require( msg.value >= _auction.amount + ((_auction.amount * minBidIncrementPercentage) / 100), 'Must send more than last bid by minBidIncrementPercentage amount' ); 
        MultiPartRLEToSVG.sol, 57, '<rect width="100%" height="100%" fill="#', params.background, '" />', _generateSVGRects(params, palettes), '</svg>' ) );
        SVGRenderer.sol, 169, chunk = string( abi.encodePacked( chunk, '<rect width="', buffer[i], '" height="10" x="', buffer[i + 1], '" y="', buffer[i + 2], '" fill="#', buffer[i + 3], '" />' ) );
        SVGRenderer.sol, 28, string private constant _SVG_END_TAG = '</svg>'; 



## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        NounsDAOLogicV1.sol.initialize nouns_
        NounsDAOExecutor.sol.setPendingAdmin pendingAdmin_
        ERC721.sol.transferFrom from
        ERC721.sol.safeTransferFrom to
        NounsDAOExecutor.sol.executeTransaction target
        NounsDAOLogicV2.sol.initialize nouns_
        NounsDAOLogicV1.sol._setVetoer newVetoer
        NounsArt.sol.addBodiesFromPointer pointer
        NounsArt.sol.addHeadsFromPointer pointer
        NounsAuctionHouse.sol.initialize _weth
        NounsArt.sol.setPalettePointer pointer
        ERC721Checkpointable.sol.delegateBySig delegatee
        ERC721.sol.safeTransferFrom from
        ERC721.sol.transferFrom to



## Solidity compiler versions mismatch


The project is compiled with different versions of solidity, which is not recommended because it can lead to undefined behaviors.
        
### Code instance:

        



## Init frontrun

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: https://github.com/code-423n4/2021-09-sushimiso-findings/issues/64)
The vulnerable initialization functions in the codebase are: 

### Code instance:

        NounsAuctionHouse.sol, initialize, 62



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        NounsDAOLogicV1.sol, getActions
        SVGRenderer.sol, _generateSVGRects
        NFTDescriptorV2.sol, generateSVGImage
        MultiPartRLEToSVG.sol, generateSVG
        NFTDescriptor.sol, generateSVGImage
        SVGRenderer.sol, generateSVG
        MultiPartRLEToSVG.sol, _generateSVGRects
        NounsDAOLogicV2.sol, getActions



## Never used parameters

Those are functions and parameters pairs that the function doesn't use the parameter. In case those functions are external/public this is even worst since the user is required to put value that never used and can misslead him and waste its time. 

### Code instances:

        NounsAuctionHouse.sol: function _safeTransferETH parameter to isn't used. (_safeTransferETH is internal)
        NounsAuctionHouse.sol: function _safeTransferETH parameter value isn't used. (_safeTransferETH is internal)
        ERC721.sol: function _beforeTokenTransfer parameter tokenId isn't used. (_beforeTokenTransfer is internal)
        NounsArt.sol: function addPage parameter pointer isn't used. (addPage is internal)
        ERC721.sol: function _beforeTokenTransfer parameter to isn't used. (_beforeTokenTransfer is internal)
        ERC721.sol: function _beforeTokenTransfer parameter from isn't used. (_beforeTokenTransfer is internal)
        NFTDescriptor.sol: function constructTokenURI parameter params isn't used. (constructTokenURI is public)
        SVGRenderer.sol: function generateSVGParts parameter parts isn't used. (generateSVGParts is external)
        NFTDescriptorV2.sol: function constructTokenURI parameter params isn't used. (constructTokenURI is public)



## Open TODOs

Open TODOs can hint at programming or architectural errors that still need to be fixed. 
These files has open TODOs:

### Code instances:

        Open TODO in Inflate.sol line 574 :             // TODO this is all a compile-time constant

        Open TODO in Inflate.sol line 369 :             // TODO Solidity doesn't support constant arrays, but these are fixed at compile-time




## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721.sol#L241
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721.sol#L351
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721.sol#L188



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:


        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L97
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L391
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsAuctionHouse.sol#L96
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L68
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptorV2.sol#L98
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsDescriptor.sol#L242
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721.sol#L165


## Unsafe Cast

use openzeppilin's safeCast in:
 
### Code instances:

        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L153 : unsafe cast uint8(currentX)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L258 : unsafe cast uint96(n)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1023 : unsafe cast uint16(n)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/SVGRenderer.sol#L218 : unsafe cast bytes3(b)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/NounsSeeder.sol#L28 : unsafe cast uint48(pseudorandomness)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L253 : unsafe cast uint32(n)
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L1018 : unsafe cast uint32(n)





## Div by 0


Division by 0 can lead to accidentally revert,
(An example of a similar issue - https://github.com/code-423n4/2021-10-defiprotocol-findings/issues/84)

### Code instance:

        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L908 totalSupply might be 0


## Unbounded loop on array can lead to DoS

The attacker can push unlimitedly to an array, that some function loop over this array.
If increasing the array size enough, calling the function that does a loop over the array will always revert since there is a gas limit.
This is an High Risk issue since those arrays are publicly allows to push items into them.

### Code instances:

        NounsArt.sol (L130): Unbounded loop on the array backgrounds that can be publicly pushed by ['addBackground']
        NounsArt.sol (L134): Unbounded loop on the array backgrounds that can be publicly pushed by ['addBackground']
