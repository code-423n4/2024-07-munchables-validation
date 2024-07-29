## Title
Potential Denial of Service (DoS) in _removeTokenIdFromStakedList() due to Unbounded Loop

## Summary
The _removeTokenIdFromStakedList() function in the LandManager contract contains an unbounded loop that iterates over all staked Munchables for a user. If a user accumulates a large number of staked Munchables, this function could exceed the block gas limit, leading to a Denial of Service condition.

## Vulnerability Details
The vulnerable code is in the _removeTokenIdFromStakedList() function:
```solidity
function _removeTokenIdFromStakedList(
    address mainAccount,
    uint256 tokenId
) internal {
    uint256 stakedLength = munchablesStaked[mainAccount].length;
    bool found = false;
    for (uint256 i = 0; i < stakedLength; i++) {
        if (munchablesStaked[mainAccount][i] == tokenId) {
            munchablesStaked[mainAccount][i] = munchablesStaked[
                mainAccount
            ][stakedLength - 1];
            found = true;
            munchablesStaked[mainAccount].pop();
            break;
        }
    }
    
    if (!found) revert InvalidTokenIdError();
}
```

The function loops through all staked Munchables for a given account to find and remove a specific tokenId. As the number of staked Munchables grows, so does the gas cost of this operation.

## Impact

1. If a user accumulates a large number of staked Munchables, unstaking operations could become impossible due to exceeding the block gas limit.
2. This could lead to Munchables being permanently locked in the staking contract, as they cannot be unstaked.


## Tools Used

Manual code review


## Recommendations

1. Implement a maximum limit on the number of Munchables that can be staked per ccount.
2. Use a mapping instead of an array to store staked Munchables, allowing for O(1) removal:
```solidity
mapping(address => mapping(uint256 => bool)) private stakedMunchables;
```
            ###################################################################################################################################################################################################################################################

## Title
Integer Overflow Vulnerability in _farmPlots() Loop Counter

## Summary
The _farmPlots() function uses a uint8 as a loop counter, which can overflow if a user has more than 255 staked Munchables, potentially causing an infinite loop or denial of service.

## Vulnerability Details
In the _farmPlots() function:

```solidity
for (uint8 i = 0; i < staked.length; i++) {
    // Loop body
}
```

The loop counter 'i' is declared as uint8, which can only represent values from 0 to 255. If staked.length is greater than 255, the counter will overflow, potentially causing an infinite loop.

## Impact
- Users with more than 255 staked Munchables will be unable to farm their plots.
- Transactions calling this function for affected users will always fail, likely due to out-of-gas errors.
- This could lead to permanent denial of service for power users or late-game scenarios.

## Tools Used
- Manual code review


## Recommendations
1. Change the loop counter type from uint8 to uint256:
   ```solidity
   for (uint256 i = 0; i < staked.length; i++) {
       // Loop body
   }
   ```
2. Consider implementing a maximum limit on the number of Munchables a user can stake to prevent potential gas limit issues with extremely large numbers of stakes.
