Dead Code

## Summary
There are some occurrences of unused (dead) code in `LandManager.sol` contracts.

## Vulnerability Details

In `LandManager.sol`, the line: [292-293](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L292C1-L293C33) & line: [299](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L299) is declared but not being used .In many cases, dead code can create attack surfaces for malicious exploits.  So avoiding to have unused code in the contract is a good practice since it helps to keep the code clean and readable.

## Impact
As said before, removing unused code can keep the code clean and more readable. While no attack vectors were identified from these instances of dead code, there is a definite increase in gas usage during execution and deployment.

## Recommended Mitigation Steps
Remove the unused code.
