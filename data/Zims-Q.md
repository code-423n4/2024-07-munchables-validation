## Impact
The `stakeMunchable` function in the LandManager contract allows users to stake up to 11 Munchable NFTs, but there's no clear documentation specifying the intended maximum limit. This ambiguity could lead to:

1. Inconsistent understanding among developers and users about the actual staking limit.
2. Potential discrepancies in different parts of the system that may assume different limits.
3. Difficulties in future maintenance and upgrades due to unclear specifications.

While not immediately critical, this lack of clarity could cause subtle bugs or complications in the long term if not addressed.

## Proof of Concept
In the LandManager contract, the `stakeMunchable` function contains the following check:

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L140-L141

```solidity
if (munchablesStaked[mainAccount].length > 10)
    revert TooManyStakedMunchiesError();
This condition allows for an 11th NFT to be staked before throwing an error. However, there's no clear documentation or comments specifying whether the intended limit is 10 or 11 Munchables.
Tools Used
Manual review
Recommended Mitigation Steps

Clearly define and document the intended maximum number of Munchables that can be staked per account.
Update the code to precisely reflect this limit:

If the limit should be 10:
solidityCopyif (munchablesStaked[mainAccount].length >= 10)
    revert TooManyStakedMunchiesError();

If the limit should be 11, the current implementation is correct, but should be clearly documented.


Add a clear comment in the code explaining the exact staking limit:
solidityCopy// Maximum number of Munchables that can be staked per account is [10/11]
if (munchablesStaked[mainAccount].length [>=/> 10])
    revert TooManyStakedMunchiesError();

Update all relevant documentation to explicitly state the maximum number of Munchables that can be staked per account.
Review other parts of the system to ensure consistent implementation and understanding of this limit.

Addressing this ambiguity will improve code clarity, prevent potential misunderstandings, and facilitate easier maintenance and upgrades in the future.
