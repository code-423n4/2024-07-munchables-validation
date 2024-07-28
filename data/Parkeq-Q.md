## The stakeMunchable() function does not follow CEI pattern.

**Description:** the stakeMunchable() function allows users to stake NFTs. 

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L131-L171

As we can see in L152 token transfers happen before updating the state of the protocol against CEI pattern. Altough I can not verify an exploit that causes loss of tokens, it can be exploited for reentrancy, due to this. It is still highly recommended to follow CEI pattern to prevent possible reentrancy attack.

**Recommended Mitigation:** Handle NFTs transfers after updating state.