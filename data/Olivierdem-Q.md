First Non-Critical report, hope the format is acceptable.
Looking forward to feedbacks in order to improve.
Thanks

NON - CRITICAL ISSUES

1.  Public function can be external:
        The queueTransaction() function is a public function in NounsDAOExecutor.sol, even though it's not called in the contract and should therefore be an external function.
        Furthermore, it's declare as an external function in the INounsDAOExecutor interface in the NounsDAOInterfaces.sol, line 402.

2.  Scientific Notation
        For readability, it is best to use scientific notation (e.g 10e4) rather than decimal literals(10000) like it done in the bps2Uint function in the NounsDAOLogicV2.sol file.