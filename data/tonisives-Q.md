### Initialize is not called on upgrade

The governance proposal has [2 method calls](https://github.com/code-423n4/2022-08-nounsdao/blob/main/test/governance/NounsDAO/V2/end2end.test.ts/#L194): setImplementation and setDynamicQuorumParams. However `NounsDAOLogicV2.initalize` is not called in `end2end.test.ts`

Since initialize is not called, it can be removed from the contract to save gas when deploying and clarify code. Maybe add a comment that V1 already initializes the storage and that `_setDynamicQuorumParams` is called on proxy upgrade.

Note1: There is `NounsDAOProxyV2`, that calls initialize on `NounsDAOLogicV2`. This proxy is used in tests only.

Note2: There doesn’t seem to be extra attack surface for an uninitialized NounsDAOLogicV2 contract. All the public methods are reverted for 0x0 storage variables. And when implementation is set, the old proxy's storage will be used.