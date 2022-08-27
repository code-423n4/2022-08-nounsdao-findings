
## Table of Contents

L-01 Missing checks for address(0x0) when assigning values to address state variables (1 instance)
L-02 Unused receive() function will lock Ether in contract (1 instance)
L-03 Math library unnecessarily overflows during some operations  (1 instance)

Total: 3 instances over 3 issues.

N-01 Numeric values having to do with time should use time units for readability (6 instances)
N-02 Missing event for critical parameter change  (14 instances)
N-03 Use a more recent version of solidity  (6 instances)
N-04 Non-library/interface files should use fixed compiler versions, not floating ones  (6 instances)
N-05 NatSpec is incomplete (4 instances)
N-06 Event is missing indexed fields (5 instances)
N-07 Large multiples of ten should use scientific notation (3 instances)
N-08 require()/revert() statements should have descriptive reason strings (6 instances)

Total: 50 instances over 8 issues.



## L-01 Missing checks for address(0x0) when assigning values to address state variables (1 instance)

There is 1 instance in 1 file:
File: contracts/governance/NounsDAOProxy.sol

        admin = admin_;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L71


## L-02 Unused receive() function will lock Ether in contract

If the intention is for the Ether to be used, the function should call another function, otherwise it should revert

There is 1 instance in 1 file:
File: contracts/governance/NounsDAOLogicV2.sol

    receive() external payable {}
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1030


## L-03 Math library unnecessarily overflows during some operations

In the example below, a + b may overflow. This particular case can be prevented by doing (a & b) + (a ^ b) / b. 
See this library for ways of doing math without this sort of issue. https://github.com/barakman/solidity-math-utils/blob/master/project/contracts/IntegralMath.sol

There is 1 instance in 1 file:
File: contracts/base/ERC721Checkpointable.sol

    function add96(
        uint96 a,
        uint96 b,
        string memory errorMessage
    ) internal pure returns (uint96) {
        uint96 c = a + b;
        require(c >= a, errorMessage);
        return c;
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L263-L271



## N-01 Numeric values having to do with time should use time units for readability

There are units for seconds, minutes, hours, days, and weeks. 
https://docs.soliditylang.org/en/latest/units-and-global-variables.html#time-units

There are 6 instances in 2 files:

File: contracts/governance/NounsDAOLogicV1.sol

    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L76

    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L79

    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L85


File: contracts/governance/NounsDAOLogicV2.sol

    uint256 public constant MIN_VOTING_PERIOD = 5_760; // About 24 hours
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L68

    uint256 public constant MAX_VOTING_PERIOD = 80_640; // About 2 weeks
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L71

    uint256 public constant MAX_VOTING_DELAY = 40_320; // About 1 week
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L77


## N-02 Missing event for critical parameter change

There are 14 instances in 4 files:

File: contracts/governance/NounsDAOLogicV1.sol
 
    function getActions(uint256 proposalId)
        external
        view
        returns (
            address[] memory targets,
            uint256[] memory values,
            string[] memory signatures,
            bytes[] memory calldatas
        )
    {
        Proposal storage p = proposals[proposalId];
        return (p.targets, p.values, p.signatures, p.calldatas);
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L392-L401

    function getReceipt(uint256 proposalId, address voter) external view returns (Receipt memory) {
        return proposals[proposalId].receipts[voter];
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L412-L414

File: contracts/governance/NounsDAOLogicV2.sol

    function getActions(uint256 proposalId)
        external
        view
        returns (
            address[] memory targets,
            uint256[] memory values,
            string[] memory signatures,
            bytes[] memory calldatas
        )
    {
        Proposal storage p = _proposals[proposalId];
        return (p.targets, p.values, p.signatures, p.calldatas);
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L403-L415


    function getReceipt(uint256 proposalId, address voter) external view returns (Receipt memory) {
        return _proposals[proposalId].receipts[voter];
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L423-L425

File: contracts/governance/NounsDAOInterfaces.sol

interface INounsDAOExecutor {
    function delay() external view returns (uint256);

    function GRACE_PERIOD() external view returns (uint256);

    function acceptAdmin() external;

    function queuedTransactions(bytes32 hash) external view returns (bool);

    function queueTransaction(
        address target,
        uint256 value,
        string calldata signature,
        bytes calldata data,
        uint256 eta
    ) external returns (bytes32);

    function cancelTransaction(
        address target,
        uint256 value,
        string calldata signature,
        bytes calldata data,
        uint256 eta
    ) external;

    function executeTransaction(
        address target,
        uint256 value,
        string calldata signature,
        bytes calldata data,
        uint256 eta
    ) external payable returns (bytes memory);
}

interface NounsTokenLike {
    function getPriorVotes(address account, uint256 blockNumber) external view returns (uint96);

    function totalSupply() external view returns (uint96);
}
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L402-L440


    function delegateTo(address callee, bytes memory data) internal {
        (bool success, bytes memory returnData) = callee.delegatecall(data);
        assembly {
            if eq(success, 0) {
                revert(add(returnData, 0x20), returndatasize())
            }
        }
    }
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L94-L101


## N-03 Use a more recent version of solidity
Use a solidity version of at least 0.8.10 to have external calls skip contract existence checks if the external call has a return value. 
Use a solidity version of at least 0.8.12 to get string.concat() instead of abi.encodePacked(<str>,<str>). 
Use a solidity version of at least 0.8.13 to get the ability to use using for with a list of free functions

There are 6 instances in 6 files:
File: contracts/governance/NounsDAOLogicV1.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L61

File: contracts/governance/NounsDAOLogicV2.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L53

File: contracts/governance/NounsDAOInterfaces.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L33

File: contracts/governance/NounsDAOProxy.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L36

File: contracts/base/ERC721Checkpointable.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L35

File: contracts/base/ERC721Enumerable.sol
	pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28


## N-04 Non-library/interface files should use fixed compiler versions, not floating ones

There are 6 instances in 6 files:
File: contracts/governance/NounsDAOLogicV1.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L61

File: contracts/governance/NounsDAOLogicV2.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L53

File: contracts/governance/NounsDAOInterfaces.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L33

File: contracts/governance/NounsDAOProxy.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L36

File: contracts/base/ERC721Checkpointable.sol

	pragma solidity ^0.8.6;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L35

File: contracts/base/ERC721Enumerable.sol

	pragma solidity ^0.8.0;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Enumerable.sol#L28


## N-05 NatSpec is incomplete

There are many instances in 4 files:

File: contracts/governance/NounsDAOLogicV1.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L672-L683

File: contracts/governance/NounsDAOLogicV2.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1006-L1031

File: contracts/governance/NounsDAOInterfaces.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L402-L440

File: contracts/base/ERC721Checkpointable.sol

https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L197-L289


## N-06 Event is missing indexed fields
Each event should use three indexed fields if there are three or more fields

There are 5 instances in 2 files:

File: contracts/governance/NounsDAOInterfaces.sol

    event ProposalCreated(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        string description
    );
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L37-L47

    event ProposalCreatedWithRequirements(
        uint256 id,
        address proposer,
        address[] targets,
        uint256[] values,
        string[] signatures,
        bytes[] calldatas,
        uint256 startBlock,
        uint256 endBlock,
        uint256 proposalThreshold,
        uint256 quorumVotes,
        string description
    );
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L50-L62

    event VoteCast(address indexed voter, uint256 proposalId, uint8 support, uint256 votes, string reason);
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L70

    event RefundableVote(address indexed voter, uint256 refundAmount, bool refundSent);
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOInterfaces.sol#L120

File: contracts/base/ERC721Checkpointable.sol

    event DelegateVotesChanged(address indexed delegate, uint256 previousBalance, uint256 newBalance);
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/base/ERC721Checkpointable.sol#L73
  

 
# N-07 Large multiples of ten should use scientific notation 

(e.g. 1e6) rather than decimal literals (e.g. 1000000), for readability

There are 3 instances in 2 files:
File: contracts/governance/NounsDAOLogicV1.sol

     return (number * bps) / 10000;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV1.sol#L673

File: contracts/governance/NounsDAOLogicV2.sol

        uint256 againstVotesBPS = (10000 * againstVotes) / totalSupply;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L908

        return (number * bps) / 10000;
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1007



## N-08 require()/revert() statements should have descriptive reason strings

There are 4 instances in 2 files:
File: contracts/governance/NounsDAOLogicV2.sol

            revert AdminOnly();
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L785

            revert UnsafeUint16Cast();
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOLogicV2.sol#L1025

File: contracts/governance/NounsDAOProxy.sol

               revert(add(returnData, 0x20), returndatasize())
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L98

                revert(free_mem_ptr, returndatasize())
https://github.com/code-423n4/2022-08-nounsdao/blob/main/contracts/governance/NounsDAOProxy.sol#L118



