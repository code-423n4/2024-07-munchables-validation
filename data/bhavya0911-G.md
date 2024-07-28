## Gas Optimizations


| |Issue|Instances|
|-|:-|:-:|
| GAS-1 | Unnecessary use of uint8 for loop counter | 1 |
| GAS-2 | Accessing array.length for each loop iteration | 1 |
### [GAS-1] Unnecessary use of uint8 for loop counter
Using uint8 for loop counter introduces unnecessary conversation that solidity compiler has to make to counter that value to uint256 for checking at the start of each iteration.

*Instances (1)*:
```solidity
File: ./src/managers/LandManager.sol

247:        for (uint8 i = 0; i < staked.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/./src/managers/LandManager.sol)
### [GAS-2] Accessing array.length for each loop iteration
Instead of saving array's length in a variable, accessing it directly in loop conditions incur additional cost in each iteration of ~100 gas as solidity compiler has to access the array to fetch it's length when checking the condition at the start of each iteraction.

*Instances (1)*:
```solidity
File: ./src/managers/LandManager.sol

247:        for (uint8 i = 0; i < staked.length; i++) {

```
[Link to code](https://github.com/code-423n4/2024-07-munchables/blob/main/./src/managers/LandManager.sol)