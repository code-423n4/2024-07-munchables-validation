Potential Denial of Service (DoS) in _removeTokenIdFromStakedList() due to Unbounded Loop

Summary
The _removeTokenIdFromStakedList() function in the LandManager contract contains an unbounded loop that iterates over all staked Munchables for a user. If a user accumulates a large number of staked Munchables, this function could exceed the block gas limit, leading to a Denial of Service condition.

Vulnerability Details
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

Impact

1. If a user accumulates a large number of staked Munchables, unstaking operations could become impossible due to exceeding the block gas limit.
2. This could lead to Munchables being permanently locked in the staking contract, as they cannot be unstaked.


Tools Used

Manual code review


Recommendations

1. Implement a maximum limit on the number of Munchables that can be staked per ccount.
2. Use a mapping instead of an array to store staked Munchables, allowing for O(1) removal:
```solidity
mapping(address => mapping(uint256 => bool)) private stakedMunchables;
```