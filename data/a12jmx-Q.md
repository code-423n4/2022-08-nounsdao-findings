Initializing the variable in the following for loops is unnecessary as they get set to 0 by default

Contract: NounsDAOLogicV2

        line 292
        line 330
        line 357
        line 382       

Recommendation:

	for (uint256 i; i < proposal.targets.length; i++)