[QA-01] `timestamp = block.timestamp` should be outside the loop
[_farmPlots](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L248) for loops thought all of the staked NFTs and claims their rewards. However inside the loop we also initialized the time. This can be avoided as `timestamp` will always be `block.timestamp` for every NFT, thus it can be removed from the for loop.

```diff
+   timestamp = block.timestamp;
    for (uint8 i = 0; i < staked.length; i++) {
-       timestamp = block.timestamp;
```
