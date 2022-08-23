**NounsDAOLogicV2**
- L55 - The NounsDAOInterfaces.sol file is imported, but inside all the contracts found are not interfaces. This is somewhat confused in its definition, which could be modified by another name.

- L819 - In the acceptAdmin() function, the following validation is performed: msg.sender == pendingAdmin && msg.sender != address(0),
being that first it is validated that the sender is == pendingAdmin, then it does not make sense to validate if msg.sender is yes or if != 0, since the zero address does not have a private key, therefore nobody can make a tx from that address .

- L908 - An input is received and in the first line it is divided by that input, totalSupply, this should be validated so that it does not revert without any message.


**ERC721Checkpointable**
- INCOMPLETE NATSPEC COMMENT in L197/210/232/253/258/263/273/282
