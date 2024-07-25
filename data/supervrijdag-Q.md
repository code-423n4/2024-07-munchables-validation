# QA-01

## Using bool found in _removeTokenIdFromStakedList is redundant

### Context
https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L312

### Description

In the internal _removeTokenIdFromStakedList function it sets the bool found on true when the munchablesStaked[mainAccount][i] matches the tokenId. 

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

It can be simplified so you don't need the boolean but instead returning immediately once the token ID is found and removed

```solidity

    function _removeTokenIdFromStakedList(
        address mainAccount,
        uint256 tokenId
    ) internal {
        uint256 stakedLength = munchablesStaked[mainAccount].length;
        for (uint256 i = 0; i < stakedLength; i++) {
            if (munchablesStaked[mainAccount][i] == tokenId) {
                munchablesStaked[mainAccount][i] = munchablesStaked[
                    mainAccount
                ][stakedLength - 1];
                munchablesStaked[mainAccount].pop();
                return;
            }
        }
        revert InvalidTokenIdError();
    }
```

The difference:

```dif
 function _removeTokenIdFromStakedList(
     address mainAccount,
     uint256 tokenId
 ) internal {
     uint256 stakedLength = munchablesStaked[mainAccount].length;
-    bool found = false;
     for (uint256 i = 0; i < stakedLength; i++) {
         if (munchablesStaked[mainAccount][i] == tokenId) {
             munchablesStaked[mainAccount][i] = munchablesStaked[
                 mainAccount
             ][stakedLength - 1];
-            found = true;
             munchablesStaked[mainAccount].pop();
-            break;
+            return;
         }
     }

-    if (!found) revert InvalidTokenIdError();
+    revert InvalidTokenIdError();
 }
```