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