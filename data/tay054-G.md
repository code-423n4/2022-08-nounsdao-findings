# USING > 0 COSTS MORE GAS THAN != 0

this saves 6 gas per loop iteration instance
4 instances
line 541, 967 in NounsDAOLogicV2.sol
line 215, 243 in ERC721Checkpointable.sol


# <ARRAY>.LENGTH SHOULD NOT BE LOOKED UP IN EVERY LOOP OF A FOR-LOOP
4 instances
line 292, 330, 357, 382  in NounsDAOLogicV2.sol

# ++I COSTS LESS GAS THAN I++, ESPECIALLY WHEN IT’S USED IN FOR-LOOPS (--I/I-- TOO)

this saves 6 gas per loop

4 instances
line 292, 330, 357, 382 in NounsDAOLogicV2.sol