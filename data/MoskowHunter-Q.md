LandManager.sol

https://github.com/code-423n4/2024-07-munchables/blob/94cf468aaabf526b7a8319f7eba34014ccebe7b9/src/managers/LandManager.sol#L323

   found = true;
   munchablesStaked[mainAccount].pop();
   break;

if your change it to: 

 munchablesStaked[mainAccount].pop();
 found = true;
 break;

it will avoid calling unnecessarily the pop() operation. The pop() must be calling once after the token was removed.