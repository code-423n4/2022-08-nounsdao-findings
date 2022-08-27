## More efficient Struct packing of ProposalCondensed in the contract NounsDAOInterfaces.sol


        The following structs could change the order of their stored elements to decrease memory uses.
        and number of occupied slots. Therefore will save gas at every store and load from memory.
        
In NounsDAOInterfaces.sol, ProposalCondensed is optimized to: 12 slots from: 13 slots.
The new order of types (you choose the actual variables):
        1. uint256
        2. uint256
        3. uint256
        4. uint256
        5. uint256
        6. uint256
        7. uint256
        8. uint256
        9. uint256
        10. uint256
        11. uint256
        12. address
        13. bool
        14. bool
        15. bool



## More efficient Struct packing of Proposal in the contract NounsDAOInterfaces.sol


        The following structs could change the order of their stored elements to decrease memory uses.
        and number of occupied slots. Therefore will save gas at every store and load from memory.
        
In NounsDAOInterfaces.sol, Proposal is optimized to: 15 slots from: 16 slots.
The new order of types (you choose the actual variables):
        1. uint256
        2. uint256
        3. uint256
        4. uint256
        5. address[]
        6. uint256[]
        7. string[]
        8. bytes[]
        9. uint256
        10. uint256
        11. uint256
        12. uint256
        13. uint256
        14. mapping
        15. address
        16. bool
        17. bool
        18. bool



## More efficient Struct packing of Proposal in the contract NounsDAOInterfaces.sol


        The following structs could change the order of their stored elements to decrease memory uses.
        and number of occupied slots. Therefore will save gas at every store and load from memory.
        
In NounsDAOInterfaces.sol, Proposal is optimized to: 17 slots from: 18 slots.
The new order of types (you choose the actual variables):
        1. uint256
        2. uint256
        3. uint256
        4. uint256
        5. address[]
        6. uint256[]
        7. string[]
        8. bytes[]
        9. uint256
        10. uint256
        11. uint256
        12. uint256
        13. uint256
        14. mapping
        15. uint256
        16. uint256
        17. address
        18. bool
        19. bool
        20. bool






## Unnecessary equals boolean


Boolean variables can be checked within conditionals directly without the use of equality operators to true/false.

### Code instances:

        NounsDAOLogicV2.sol, 597: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');
        NounsDAOLogicV1.sol, 505: require(receipt.hasVoted == false, 'NounsDAO::castVoteInternal: voter already voted');



## State variables that could be set immutable

In the following files there are state variables that could be set immutable to save gas. 

### Code instances:

        _symbol in ERC721.sol
        duration in NounsAuctionHouse.sol
        weth in NounsAuctionHouse.sol
        _name in ERC721.sol
        nouns in NounsAuctionHouse.sol



## Unused state variables

Unused state variables are gas consuming at deployment (since they are located in storage) and are 
a bad code practice. Removing those variables will decrease deployment gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        NounsDescriptor.sol, COPYRIGHT_CC0_1_0_UNIVERSAL_LICENSE
        NounsDescriptorV2.sol, COPYRIGHT_CC0_1_0_UNIVERSAL_LICENSE



## Unused declared local variables

Unused local variables are gas consuming, since the initial value assignment costs gas. And are 
a bad code practice. Removing those variables will decrease the gas cost and improve code quality. 
This is a full list of all the unused storage variables we found in your code base. 

### Code instances:

        MultiPartRLEToSVG.sol, _decodeRLEImage, rects
        MultiPartRLEToSVG.sol, _decodeRLEImage, bounds
        SVGRenderer.sol, _decodeRLEImage, draws
        SVGRenderer.sol, _decodeRLEImage, bounds
        NFTDescriptorV2.sol, constructTokenURI, image
        NFTDescriptor.sol, constructTokenURI, image
        NounsDescriptorV2.sol, getPartsForSeed, glasses_
        MultiPartRLEToSVG.sol, _decodeRLEImage, paletteIndex



## Change transferFrom to transfer

'transferFrom(address(this), *, **)' could be replaced by the following more gas efficient 'transfer(*, **)'
               This replacement is more gas efficient and improves the code quality.

### Code instance:

        NounsAuctionHouse.sol, 233 : nouns.transferFrom(address(this), _auction.bidder, _auction.nounId);



## Unnecessary array boundaries check when loading an array element twice


    There are places in the code (especially in for-each loops) that loads the same array element more than once. 
    In such cases, only one array boundaries check should take place, and the rest are unnecessary.
    Therefore, this array element should be cached in a local variable and then be loaded
    again using this local variable, skipping the redundant second array boundaries check: 
    
### Code instances:

        MultiPartRLEToSVG.sol._getChunk - double load of buffer[i]
        SVGRenderer.sol._getChunk - double load of buffer[i]



## Caching array length can save gas


Caching the array length is more gas efficient.
This is because access to a local variable in solidity is more efficient than query storage / calldata / memory.
We recommend to change from:    

    for (uint256 i=0; i<array.length; i++) { ... }

to: 

    uint len = array.length  
    for (uint256 i=0; i<len; i++) { ... }


### Code instances:

        SVGRenderer.sol, image, 192
        MultiPartRLEToSVG.sol, image, 152
        MultiPartRLEToSVG.sol, parts.params, 81
        NounsDAOLogicV2.sol, targets.proposal, 330
        NounsDescriptor.sol, _heads, 150
        NounsArt.sol, _backgrounds, 131
        NounsDAOLogicV1.sol, targets.proposal, 371
        NounsDAOLogicV2.sol, targets.proposal, 357
        SVGRenderer.sol, parts.params, 100
        NounsDescriptor.sol, _glasses, 160
        NounsDAOLogicV2.sol, targets.proposal, 382
        NounsDAOLogicV1.sol, targets.proposal, 281
        NounsDAOLogicV1.sol, targets.proposal, 319
        NounsDescriptor.sol, _bodies, 130
        NounsDescriptor.sol, newColors, 110
        NounsDAOLogicV1.sol, targets.proposal, 346
        NounsDescriptor.sol, _accessories, 140
        NounsDescriptor.sol, _backgrounds, 120
        NounsDAOLogicV2.sol, targets.proposal, 292



## Prefix increments are cheaper than postfix increments

Prefix increments are cheaper than postfix increments. 
Further more, using unchecked {++x} is even more gas efficient, and the gas saving accumulates every iteration and can make a real change
There is no risk of overflow caused by increamenting the iteration index in for loops (the `++i` in `for (uint256 i = 0; i < numIterations; ++i)`).
But increments perform overflow checks that are not necessary in this case.
These functions use not using prefix increments (`++x`) or not using the unchecked keyword: 

### Code instances:

        change to prefix increment and unchecked: NounsArt.sol, i, 432
        change to prefix increment and unchecked: NounsDAOLogicV2.sol, i, 330
        change to prefix increment and unchecked: MultiPartRLEToSVG.sol, p, 81
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 130
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 160
        change to prefix increment and unchecked: NounsArt.sol, i, 131
        change to prefix increment and unchecked: NounsDAOLogicV1.sol, i, 281
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 110
        change to prefix increment and unchecked: NounsDAOLogicV2.sol, i, 382
        change to prefix increment and unchecked: NounsDAOLogicV1.sol, i, 346
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 140
        change to prefix increment and unchecked: NounsDAOLogicV1.sol, i, 319
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 150
        change to prefix increment and unchecked: NounsDescriptor.sol, i, 120
        change to prefix increment and unchecked: NounsDAOLogicV2.sol, i, 357
        change to prefix increment and unchecked: NounsDAOLogicV1.sol, i, 371
        change to prefix increment and unchecked: NounsDAOLogicV2.sol, i, 292
        change to prefix increment and unchecked: SVGRenderer.sol, p, 100



## Unnecessary index init


In for loops you initialize the index to start from 0, but it already initialized to 0 in default and this assignment cost gas. 
It is more clear and gas efficient to declare without assigning 0 and will have the same meaning:

### Code instances:

        NounsDAOLogicV2.sol, 292
        NounsDAOLogicV1.sol, 281
        NounsDAOLogicV2.sol, 330
        SVGRenderer.sol, 168
        NounsDAOLogicV2.sol, 382
        MultiPartRLEToSVG.sol, 127
        SVGRenderer.sol, 100
        NounsDAOLogicV1.sol, 371
        NounsDAOLogicV1.sol, 346
        NounsDescriptor.sol, 120
        NounsDescriptor.sol, 150
        MultiPartRLEToSVG.sol, 81
        NounsDescriptor.sol, 160
        NounsDescriptor.sol, 110
        NounsDescriptor.sol, 140
        NounsDAOLogicV2.sol, 357
        NounsDAOLogicV1.sol, 319
        NounsArt.sol, 131
        NounsDescriptor.sol, 130
        NounsArt.sol, 432



## Storage double reading. Could save SLOAD

Reading a storage variable is gas costly (SLOAD). In cases of multiple read of a storage variable in the same scope, caching the first read (i.e saving as a local variable) can save gas and decrease the
 overall gas uses. The following is a list of functions and the storage variables that you read twice: 

### Code instances:

        Inflate.sol: MAXBITS is read twice in _construct
        Inflate.sol: FIXLCODES is read twice in _build_fixed
        Inflate.sol: MAXDCODES is read twice in _build_fixed



## Unnecessary default assignment


Unnecessary default assignments, you can just declare and it will save gas and have the same meaning.
    

### Code instance:

        ERC721Checkpointable.sol (L#41) : uint8 public constant decimals = 0; 



## Rearrange state variables

You can change the order of the storage variables to decrease memory uses.

### Code instances:

In NounsAuctionHouse.sol,rearranging the storage fields can optimize to: 6 slots from: 7 slots.
The new order of types (you choose the actual variables):
        1. INounsToken
        2. uint256
        3. uint256
        4. uint256
        5. INounsAuctionHouse.Auction
        6. address
        7. uint8

In NounsToken.sol,rearranging the storage fields can optimize to: 7 slots from: 8 slots.
The new order of types (you choose the actual variables):
        1. INounsDescriptorMinimal
        2. INounsSeeder
        3. uint256
        4. string
        5. IProxyRegistry
        6. address
        7. bool
        8. bool
        9. bool
        10. address




## Use bytes32 instead of string to save gas whenever possible


    Use bytes32 instead of string to save gas whenever possible.
    String is a dynamic data structure and therefore is more gas consuming then bytes32.

    
### Code instances:

        SVGRenderer.sol (L27), string private constant _SVG_START_TAG = '<svg width="320" height="320" viewBox="0 0 320 320" xmlns="http:
        MultiPartRLEToSVG.sol (L54), return string( abi.encodePacked( '<svg width="320" height="320" viewBox="0 0 320 320" xmlns="http:
        SVGRenderer.sol (L53), return string( abi.encodePacked( _SVG_START_TAG, '<rect width="100%" height="100%" fill="#', params.background, '" />', _generateSVGRects(params), _SVG_END_TAG ) );



## Short the following require messages

The following require messages are of length more than 32 and we think are short enough to short
them into exactly 32 characters such that it will be placed in one slot of memory and the require 
function will cost less gas. 
The list: 

### Code instances:

        Solidity file: NounsDescriptor.sol, In line 109, Require message length to shorten: 33, The message: Palettes can only hold 256 colors
        Solidity file: NounsDAOLogicV1.sol, In line 364, Require message length to shorten: 33, The message: NounsDAO::veto: veto power burned
        Solidity file: NounsDAOLogicV1.sol, In line 581, Require message length to shorten: 40, The message: NounsDAO::_setQuorumVotesBPS: admin only
        Solidity file: NounsDAOLogicV1.sol, In line 651, Require message length to shorten: 37, The message: NounsDAO::_burnVetoPower: vetoer only
        Solidity file: NounsDAOLogicV2.sol, In line 853, Require message length to shorten: 37, The message: NounsDAO::_burnVetoPower: vetoer only
        Solidity file: NounsDescriptor.sol, In line 170, Require message length to shorten: 33, The message: Palettes can only hold 256 colors
        Solidity file: NounsDAOLogicV2.sol, In line 375, Require message length to shorten: 33, The message: NounsDAO::veto: veto power burned
        Solidity file: NounsDAOLogicV2.sol, In line 207, Require message length to shorten: 39, The message: NounsDAO::propose: must provide actions
        Solidity file: NounsDAOLogicV1.sol, In line 546, Require message length to shorten: 38, The message: NounsDAO::_setVotingPeriod: admin only
        Solidity file: NounsDAOLogicV1.sol, In line 198, Require message length to shorten: 35, The message: NounsDAO::propose: too many actions
        Solidity file: NounsDAOLogicV1.sol, In line 197, Require message length to shorten: 39, The message: NounsDAO::propose: must provide actions
        Solidity file: NounsDAOLogicV2.sol, In line 840, Require message length to shorten: 33, The message: NounsDAO::_setVetoer: vetoer only
        Solidity file: NounsDAOLogicV1.sol, In line 530, Require message length to shorten: 37, The message: NounsDAO::_setVotingDelay: admin only
        Solidity file: NounsDAOLogicV2.sol, In line 801, Require message length to shorten: 38, The message: NounsDAO::_setPendingAdmin: admin only
        Solidity file: NounsDAOLogicV1.sol, In line 422, Require message length to shorten: 36, The message: NounsDAO::state: invalid proposal id
        Solidity file: NounsDAOLogicV2.sol, In line 433, Require message length to shorten: 36, The message: NounsDAO::state: invalid proposal id
        Solidity file: ERC721.sol, In line 379, Require message length to shorten: 36, The message: ERC721: transfer to the zero address
        Solidity file: NounsDAOLogicV1.sol, In line 599, Require message length to shorten: 38, The message: NounsDAO::_setPendingAdmin: admin only
        Solidity file: ERC721.sol, In line 143, Require message length to shorten: 33, The message: ERC721: approval to current owner
        Solidity file: NounsDAOLogicV1.sol, In line 638, Require message length to shorten: 33, The message: NounsDAO::_setVetoer: vetoer only
        Solidity file: NounsDAOLogicV2.sol, In line 622, Require message length to shorten: 37, The message: NounsDAO::_setVotingDelay: admin only
        Solidity file: NounsDAOLogicV2.sol, In line 638, Require message length to shorten: 38, The message: NounsDAO::_setVotingPeriod: admin only
        Solidity file: NounsDAOLogicV2.sol, In line 208, Require message length to shorten: 35, The message: NounsDAO::propose: too many actions



## Use != 0 instead of > 0


Using != 0 is slightly cheaper than > 0. (see https://github.com/code-423n4/2021-12-maple-findings/issues/75 for similar issue)


### Code instances:

        ERC721Checkpointable.sol, 215: change 'amount > 0' to 'amount != 0'
        ERC721Checkpointable.sol, 243: change 'nCheckpoints > 0' to 'nCheckpoints != 0'



## Use unchecked to save gas for certain additive calculations that cannot overflow


You can use unchecked in the following calculations since there is no risk to overflow:

### Code instances:

        NounsDAOLogicV2.sol (L#291) - uint256 eta = block.timestamp + timelock.delay();
        NounsDAOLogicV2.sol (L#449) - } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {
        NounsDAOLogicV1.sol (L#280) - uint256 eta = block.timestamp + timelock.delay();
        NounsAuctionHouse.sol (L#128) - auction.endTime = _auction.endTime = block.timestamp + timeBuffer;
        NounsDAOLogicV1.sol (L#438) - } else if (block.timestamp >= proposal.eta + timelock.GRACE_PERIOD()) {



## Empty else statement can be removed to save gas


        Empty else statement can be removed to save gas.
        
    
### Code instance:

        NounsDAOLogicV2.sol._writeQuorumParamsCheckpoint



## uint8 index

Due to how the EVM natively works on 256 numbers, using a 8 bit number here introduces additional costs as the EVM has to properly enforce the limits of this smaller type. 
See the warning at this link: https://docs.soliditylang.org/en/v0.8.0/internals/layout_in_storage.html#layout-of-state-variables-in-storage 
We recommend to use uint256 for the index in every for loop instead using uint8: 

### Code instances:

        SVGRenderer.sol, uint8 p, 100
        MultiPartRLEToSVG.sol, uint8 p, 81



## Consider inline the following functions to save gas


    You can inline the following functions instead of writing a specific function to save gas.
    (see https://github.com/code-423n4/2021-11-nested-findings/issues/167 for a similar issue.)

    
### Code instances

        ERC721.sol, _baseURI, { return ''; }
        NounsAuctionHouse.sol, _safeTransferETH, { return success; }
        NounsDAOLogicV2.sol, min, { return a < b ? a : b; }
        NounsDAOLogicV1.sol, bps2Uint, { return (number * bps) / 10000; }
        NounsDAOLogicV2.sol, bps2Uint, { return (number * bps) / 10000; }
        NounsDescriptorV2.sol, _getPalette, { return art.palettes(uint8(part[0])); }
        ERC721.sol, _exists, { return _owners[tokenId] != address(0); }



## Inline one time use functions


The following functions are used exactly once. Therefore you can inline them and save gas and improve code clearness.
    

### Code instances:

        ERC721Enumerable.sol, _removeTokenFromOwnerEnumeration
        MultiPartRLEToSVG.sol, _decodeRLEImage
        NounsArt.sol, decompressAndDecode
        SVGRenderer.sol, _decodeRLEImage
        ERC721.sol, _baseURI
        NounsDAOProxy.sol, delegateTo
        ERC721Checkpointable.sol, safe96
        ERC721Enumerable.sol, _removeTokenFromAllTokensEnumeration
        Inflate.sol, _stored
        ERC721Enumerable.sol, _beforeTokenTransfer
        ERC721Checkpointable.sol, getChainId
        ERC721.sol, _safeTransfer
        Inflate.sol, _build_fixed
        NounsDAOLogicV2.sol, proposalCreationBlock
        NounsDAOLogicV2.sol, queueOrRevertInternal
        Inflate.sol, _build_dynamic_lengths
        ERC721Checkpointable.sol, safe32
        ERC721.sol, _mint
        NounsDAOLogicV2.sol, _refundGas
        ERC721Checkpointable.sol, add96
        ERC721Checkpointable.sol, sub96
        NounsDAOLogicV1.sol, queueOrRevertInternal
        SVGRenderer.sol, _getColor
        ERC721Enumerable.sol, _addTokenToOwnerEnumeration
        NounsAuctionHouse.sol, _safeTransferETH
        ERC721Enumerable.sol, _addTokenToAllTokensEnumeration
        Inflate.sol, _build_dynamic
        NounsArt.sol, getPage
        SVGRenderer.sol, _toHexString
        MultiPartRLEToSVG.sol, _generateSVGRects
        NounsDAOProxyV2.sol, delegateTo
        NounsDAOLogicV1.sol, getChainIdInternal
        NounsDAOLogicV2.sol, getChainIdInternal



## Change if -> revert pattern to require

Change if -> revert pattern to 'require' to save gas and improve code quality,
if (some_condition) { 
        revert(revert_message) 
}

to: require(!some_condition, revert_message)

In the following locations:

### Code instance:

        ERC721.sol, 420



## Do not cache msg.sender


We recommend not to cache msg.sender since calling it is 2 gas while reading a variable is more.


### Code instances:

        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOExecutor.sol#L91
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/base/ERC721Checkpointable.sol#L114
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV1.sol#L209
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxyV2.sol#L56
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOProxy.sol#L53
        https://github.com/code-423n4/2022-08-nounsdao/tree/main/contracts/governance/NounsDAOLogicV2.sol#L219
