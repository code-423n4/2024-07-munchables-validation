# 1. Ambiguous naming (reusing) for getting `uint` from StorageKey can easily lead to human error when setting config

[Source](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L65-L79)

```solidity
        MIN_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.LockManager
        );
        MAX_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.AccountManager
        );
        DEFAULT_TAX_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.ClaimManager
        );
        BASE_SCHNIBBLE_RATE = IConfigStorage(configStorage).getUint(
            StorageKey.MigrationManager
        );
        PRICE_PER_PLOT = IConfigStorage(configStorage).getUint(
            StorageKey.NFTOverlord
        );
```

Even though keys are enumerated and may work correctly, but the enum variable names are variable names for  addressStorage, which should not be re-used for other names. To avoid potential key collision and human error, it is recommended to create a separate `StorageKeyUint` with enumerated variables specifically for these uints.

# 2. `farmPlots` call is prone to out-of-gas errors when users have a large number of staked plots.

[Source](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L247-L309)

In `farmPlots` implementation, it loops through all of user's staked plots to update the information. Each iteration performs multiple storage reads such as
- https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L250
- https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L262-L265 

and some SSTOREs such as 
- https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L300

This means each iteration will consume a large amount of gas (~10k minimum). However, a block in Blast mainnet has a gas limit of 30M. Hence if the user has 3000 staked plots, then the transaction can revert.

It is recommended to allow `partialFarmPlots` which can iterate over a smaller defined range instead of the whole staked plots to avoid out-of-gas error.