# Non Critical
## [N01] Use of Floating Pragma:
Contracts should be deployed with the same compiler version and flags that they have been tested with thoroughly. Locking the pragma helps to ensure that contracts do not accidentally get deployed using, for example, an outdated compiler version that might introduce bugs that affect the contract system negatively.
### Recommended Mitigation Steps:
Lock the pragma version: use `0.8.6` instead of `^0.8.6`.
