This is my first ever audit, so I apologize if the format is wrong. I have read the docs of Code4rena and I hope what I sent is satisfactory,
but anyways, I would love some feedback to improve in the futur.

Only focused on gas optimizations for this contest, but will look for low risk and non-critical issues next time.
Thanks,
Olivier


GAS OPTIMIZATIONS

    1.  Use '++proposalCount'; instead of 'proposalCount++' to save gas (found line 226 in ./governance/NounDAOLogicV2.sol).
    2.  Use "!= 0" instead of "> 0" uses less gas (found line 541 of ./governance/NounDAOLogicV2.sol ("if (votes > 0)")).
    3.  Don't compare boolean expressions to boolean literals. Use '!receipt.hasVoted' instead of 'receipt.hasVoted == false' in the require (found line 597 of ./governance/NounDAOLogicV2.sol).
    4.  There are 8 'require(msg.sender == admin)', all with different error messages. They should be grouped in a modifier adminOnly() that takes a strings for specific error message.
    5.  Require() string longer than 32 bytes cost extra gas. For exemple, line 801 of ./governance/NounDAOLogicV2.sol, the require error string ("NounsDAO::_setPendingAdmin: admin only") is 38 bytes long when "NDAO::_setPendingAdm: adm only" is 30 bytes long, and therefore cheaper in gas and just as clear.
    6.  Avoid default assignment of i in loops. 'uint i' instead of 'uint256 i = 0'. Saves 3 gas per instance. (found line 382 in ./governance/NounDAOLogicV2.sol).
    7.  Increment 'i' at the end of the loop in 'unchecked' so save gas. 'unchecked { ++i; }' at end of loop instead of 'for(uint i; i < proposal.targets.length, i++)'. (found line 382 in ./governance/NounDAOLogicV2.sol).
    8.  Add 'unchecked{}' for subtractions where the operands cannot underflow because of previous if-statement. (found line 868 in proposalCreationBlock() in ./governance/NounDAOLogicV2.sol).
    9.  Array.length should not be looked up in every loop of a for loop. Should be assigned once before the loop.
    10. Division by two should use bit shifting. 'uint256 center = upper - (upper - lower) / 2' should be 'uint256 center = upper - ((upper - lower) >> 1)' to save some gas;
    11. Using private instead of public for constant saves gas. (ex: "bytes32 public constant DOMAIN_TYPEHASH = ", line 101 of ./governance/NounDAOLogicV2.sol)
    12. Use assembly to write storage value. Line 807 in _setPendingAdmin() in ./governance/NounDAOLogicV2.sol, instead of "pendingAdmin = newPendingAdmin;", use  assembly {sstore(pendingAdmin.slot, newPendingAdmin)} to save gas. Same for line 826 of '_acceptAdmin'.