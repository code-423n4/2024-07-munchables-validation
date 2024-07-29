# L-01: uint to int cast may overflow in _farmPlots() function.

## Impact

`uint256` to `int256` cast may result in overflow for schnibblesTotal values between 2^255 and 2^256 - 1 . This depends on the time of which a munchable has been stake and the `BASE_SCHNIBBLE_RATE`. This would lead to complete stuck state for the farming/staking/unstaking activities as all functions will make the transactions revert.
 
```solidity
schnibblesTotal = (timestamp - _toiler.lastToilDate) *
	                BASE_SCHNIBBLE_RATE;
schnibblesTotal = uint256(
                (int256(schnibblesTotal) +
                    (int256(schnibblesTotal) * finalBonus)) / 100
            );
```

## Mitigation

Keep in mind the possibility for this overflow when setting BASE_SCHNIBBLE.

# L-02: Users wont be able to unstake munchable when contract is in paused stake

## Impact

Users wont be able unstake munchable NFTs out of the contract if it is paused state. If those NFTs are considered to have financial value, users may want to be able to withdraw them at any time.

```solidity
function unstakeMunchable(uint256 tokenId) external override forceFarmPlots(msg.sender) notPaused {
	...
}
```

## Mitigation

Remove `notPaused` modifier and allow users to withdraw their NFTs.
