## Some Storage Keys are confusing

As can be seen from the below codes, some of contract state names and their storage key names do not match at all.

```solidity
    function _reconfigure() internal {
        ... ...
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
        ... ...
    }
```

## It would be better if `finalBonus` in `_farmPlots()` has its upper limit.

After checking `BonusManager::getFeedBonus()` function, I have feeling that bonus values have upper limit as 100%.
So I guess it would be better that `finalBonus` has upper limit of 100%.

## A bad landlord can do sandwich operation when a toiler tries to farm plot

This is actually not a bug or an error, but may affect the trustness of the game.

A bad landlord can raise his tax before farming, then reduce it to original after farming to gain illegal Schnibbles income.

So my suggestion is adopting timelock to changing tax rate by landlord.
