[QA-01] `timestamp = block.timestamp` should be outside the loop
[_farmPlots](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L248) for loops thought all of the staked NFTs and claims their rewards. However inside the loop we also initialized the time. This can be avoided as `timestamp` will always be `block.timestamp` for every NFT, thus it can be removed from the for loop.

```diff
+   timestamp = block.timestamp;
    for (uint8 i = 0; i < staked.length; i++) {
-       timestamp = block.timestamp;
```

[QA-02] Users can avoid staking restriction by using another account
Users would be able to avoid this check inside [stakeMunchable](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L140-L141) by using another account.
```solidity
        if (munchablesStaked[mainAccount].length > 10) {
            revert TooManyStakedMunchiesError();
        }
```