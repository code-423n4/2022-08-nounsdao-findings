1. `INounsDAOExecutor.executeTransaction` has `payable` keyword. But `LogicV2.execute`(caller) and `NounsDAOExecutor.executeTransaction` doesn't have `payable` keyword. I think `LogicV2.execute` is called by proxy so payable is not mandatory. `NounsDAOExecutor.executeTransaction` is out of scope and it can be converted to `payable` function by the interface. Anyway, I think it's better to write `payable` keyword.

2. There is a mismatch between `LogicV2.execute` and `NounsDAOExecutor.executeTransaction`.
When `block.timestamp == proposal.eta + timelock.GRACE_PERIOD()`, `LogicV2.state` will return `ProposalState.Expired`. So the proposal can not be executed.
But in `NounsDAOExecutor.executeTransaction`, the proposal can be executed in the timestamp.
So I think `block.timestamp > proposal.eta + timelock.GRACE_PERIOD()` is more correct in `LogicV2.state`.