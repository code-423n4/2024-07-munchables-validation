L-1
when we stake munchable using stakeMunchable function, these 2 state variables are updated.
`munchablesStaked[mainAccount].push(tokenId);`
`munchableOwner[tokenId] = mainAccount;`
https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L159-L160
and again when we unstake munchable using unstakeMunchable function, this will takes place
`munchableOwner[tokenId] = address(0);`
        `_removeTokenIdFromStakedList(mainAccount, tokenId);`
https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L192-L193
so both munchablesStaked[mainAccount] array and munchableOwner[tokenId] mapping state variable are only updated using stakeMunchable and unstakeMunchable functions.In _removeTokenIdFromStakedList function they are using a variable named (bool found) which is initially set to false and set to true when we found required tokenId in munchablesStaked[mainAccount] array. And if after iteration through munchablesStaked[mainAccount] array still we don't find required tokenId then it will remain false and due to this `(!found) revert InvalidTokenIdError();` it will revert.But here the issue is if the tokenId was not staked then due to this check `if (munchableOwner[tokenId] != mainAccount) revert InvalidOwnerError();` 
https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L179
unstakeMunchable function will revert so there itself we are checking weather the mentioned tokenId was staked or not. so if mentioned tokenId is staked then definetely it will be in munchablesStaked[mainAccount] array `munchablesStaked[mainAccount].push(tokenId);`
so there is no point in using (bool found) variable in that function except wasting gas 🤣. 
`bool found = false;`
`found = true;`
`if (!found) revert InvalidTokenIdError();`
https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L317-L329