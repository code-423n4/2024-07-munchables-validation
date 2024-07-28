## Summary
Wrong amount of staked munchables are allowed.

## Vulnerability Details
The method `stakeMunchable` in `LandManager` contains logic for verifying that the maximum amount of staked munchables per main account does not exceed. It is meant to allow up to 10 munchables but instead if allows up to 11.

## Relevant GitHub links:
https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L140

## Impact
11 munchables per main account are allowed to be staked instead of 10. Changes feature behaviour

## Tools Used
Manual Review

## Proof of concept
There already is a test in the app which proves that the method reverts with error `TooManyStakedMunchiesError` when there are 11 munchables already staked for the main account:
```javascript
it("too many staked", async () => {
  for (let i = 1; i < 12; i++) {
    const txHash = await testContracts.munchNFT.contract.write.approve(
      [testContracts.landManagerProxy.contract.address, i],
      { account: bob }
    );
    const { request } = await testContracts.landManagerProxy.contract.simulate.stakeMunchable(
      [alice, i, i - 1],
      {
        account: bob,
      }
    );
    await assertTxSuccess({ txHash });
    const stakeMunchableTxHash = await testClient.writeContract(request);
    await assertTxSuccess({ txHash: stakeMunchableTxHash });
  }
  const { request: finalApproval } = await testContracts.munchNFT.contract.simulate.approve(
    [testContracts.landManagerProxy.contract.address, 12],
    { account: bob }
  );
  const approvalHash = await testClient.writeContract(finalApproval);
  await assertTxSuccess({ txHash: approvalHash });
  await assert.rejects(
    testContracts.landManagerProxy.contract.simulate.stakeMunchable([alice, 12, 11], {
      account: bob,
    }),
    (err: Error) => assertContractFunctionRevertedError(err, "TooManyStakedMunchiesError")
  );
});
```

## Recommendations
Change the if statement as follows:
```diff
- if (munchablesStaked[mainAccount].length > 10)
+ if (munchablesStaked[mainAccount].length >= 10)
```
