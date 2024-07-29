| ID | Bug Title |
| --- | --- |
| QA 1 | Vulnerable versions of packages are being used |
| QA 2 | Useless Check in LandManager.sol#stakeMunchable() Function |
| QA 3 | Missing Constraints for DEFAULT_TAX_RATE in LandManager.sol#_reconfigure() |
| QA 4 | Codebase Should Implement Formal Verification Testing |
| QA 5 | Consider Enabling via-ir for Enhanced Code Transparency and Auditability |
| QA 6 | Import Declarations Should Import Specific Identifiers |
| QA 7 | Not Using the Latest Versions of Project Dependencies |
| QA 8 | Unsafe ERC20 Operation(s) |
| QA 9 | Consider Pre-Calculating the Address of address(this) |


## QA 1. Vulnerable versions of packages are being used

This project's specific package versions are vulnerable to the specific CVEs listed below. Consider switching to more recent versions of these packages that don't have these vulnerabilities.

- `@openzeppelin/contracts` - <b>HIGH</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-4g63-c64m-25w9) - OpenZeppelin Contracts's SignatureChecker may revert on invalid EIP-1271 signers
    
    > Impact
    > 
    > 
    > `SignatureChecker.isValidSignatureNow` is not expected to revert. However, an incorrect assumption about Solidity 0.8's `abi.decode` allows some cases to revert, given a target contract that doesn't implement EIP-1271 as expected.
    > 
    > The contracts that may be affected are those that use `SignatureChecker` to check the validity of a signature and handle invalid signatures in a way other than reverting. We believe this to be unlikely.
    > 
    > ### Patches
    > 
    > The issue was patched in 4.7.1.
    > 
    > ### References
    > 
    > https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3552
    > 
    > ### For more information
    > 
    > If you have any questions or comments about this advisory, or need assistance deploying the fix, email us at [security@openzeppelin.com](mailto:security@openzeppelin.com).
    > 
    - `@openzeppelin/contracts` - <b>HIGH</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-xrc4-737v-9q75) - OpenZeppelin Contracts's GovernorVotesQuorumFraction updates to quorum may affect past defeated proposals
    
    > Impact
    > 
    > 
    > This issue concerns instances of Governor that use the module `GovernorVotesQuorumFraction`, a mechanism that determines quorum requirements as a percentage of the voting token's total supply. In affected instances, when a proposal is passed to lower the quorum requirement, past proposals may become executable if they had been defeated only due to lack of quorum, and the number of votes it received meets the new quorum requirement.
    > 
    > Analysis of instances on chain found only one proposal that met this condition, and we are actively monitoring for new occurrences of this particular issue.
    > 
    > ### Patches
    > 
    > This issue has been patched in v4.7.2.
    > 
    > ### Workarounds
    > 
    > Avoid lowering quorum requirements if a past proposal was defeated for lack of quorum.
    > 
    > ### References
    > 
    > https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3561
    > 
    > ### For more information
    > 
    > If you have any questions or comments about this advisory, or need assistance deploying the fix, email us at [security@openzeppelin.com](mailto:security@openzeppelin.com).
    > 
    - `@openzeppelin/contracts` - <b>HIGH</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-qh9x-gcfh-pcrw) - OpenZeppelin Contracts's ERC165Checker may revert instead of returning false
    
    > Impact
    > 
    > 
    > `ERC165Checker.supportsInterface` is designed to always successfully return a boolean, and under no circumstance revert. However, an incorrect assumption about Solidity 0.8's `abi.decode` allows some cases to revert, given a target contract that doesn't implement EIP-165 as expected, specifically if it returns a value other than 0 or 1.
    > 
    > The contracts that may be affected are those that use `ERC165Checker` to check for support for an interface and then handle the lack of support in a way other than reverting.
    > 
    > ### Patches
    > 
    > The issue was patched in 4.7.1.
    > 
    > ### References
    > 
    > https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3552
    > 
    > ### For more information
    > 
    > If you have any questions or comments about this advisory, or need assistance deploying the fix, email us at [security@openzeppelin.com](mailto:security@openzeppelin.com).
    > 
    - `@openzeppelin/contracts` - <b>HIGH</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-4h98-2769-gh6h) - OpenZeppelin Contracts vulnerable to ECDSA signature malleability
    
    > Impact
    > 
    > 
    > The functions `ECDSA.recover` and `ECDSA.tryRecover` are vulnerable to a kind of signature malleability due to accepting EIP-2098 compact signatures in addition to the traditional 65 byte signature format. This is only an issue for the functions that take a single `bytes` argument, and not the functions that take `r, v, s` or `r, vs` as separate arguments.
    > 
    > The potentially affected contracts are those that implement signature reuse or replay protection by marking the signature itself as used rather than the signed message or a nonce included in it. A user may take a signature that has already been submitted, submit it again in a different form, and bypass this protection.
    > 
    > ### Patches
    > 
    > The issue has been patched in 4.7.3.
    > 
    > ### For more information
    > 
    > If you have any questions or comments about this advisory, or need assistance deploying a fix, email us at [security@openzeppelin.com](mailto:security@openzeppelin.com).
    > 
    - `@openzeppelin/contracts` - <b>HIGH</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-93hq-5wgc-jc82) - GovernorCompatibilityBravo may trim proposal calldata
    
    > Impact
    > 
    > 
    > The proposal creation entrypoint (`propose`) in `GovernorCompatibilityBravo` allows the creation of proposals with a `signatures` array shorter than the `calldatas` array. This causes the additional elements of the latter to be ignored, and if the proposal succeeds the corresponding actions would eventually execute without any calldata. The `ProposalCreated` event correctly represents what will eventually execute, but the proposal parameters as queried through `getActions` appear to respect the original intended calldata.
    > 
    > ### Patches
    > 
    > This issue has been patched in v4.8.3.
    > 
    > ### Workarounds
    > 
    > Ensure that all proposals that pass through governance have equal length `signatures` and `calldatas` parameters.
    > 
    - `@openzeppelin/contracts` - <b>MODERATE</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-7grf-83vw-6f5x) - OpenZeppelin Contracts ERC165Checker unbounded gas consumption
    
    > Impact
    > 
    > 
    > The target contract of an EIP-165 `supportsInterface` query can cause unbounded gas consumption by returning a lot of data, while it is generally assumed that this operation has a bounded cost.
    > 
    > ### Patches
    > 
    > The issue has been fixed in v4.7.2.
    > 
    > ### References
    > 
    > https://github.com/OpenZeppelin/openzeppelin-contracts/pull/3587
    > 
    > ### For more information
    > 
    > If you have any questions or comments about this advisory, or need assistance deploying a fix, email us at [security@openzeppelin.com](mailto:security@openzeppelin.com).
    > 
    - `@openzeppelin/contracts` - <b>MODERATE</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-g4vp-m682-qqmp) - OpenZeppelin Contracts vulnerable to Improper Escaping of Output
    
    > Impact
    > 
    > 
    > OpenZeppelin Contracts is a library for secure smart contract development. Starting in version 4.0.0 and prior to version 4.9.3, contracts using `ERC2771Context` along with a custom trusted forwarder may see `_msgSender` return `address(0)` in calls that originate from the forwarder with calldata shorter than 20 bytes. This combination of circumstances does not appear to be common, in particular it is not the case for `MinimalForwarder` from OpenZeppelin Contracts, or any deployed forwarder the team is aware of, given that the signer address is appended to all calls that originate from these forwarders.
    > 
    > ### Patches
    > 
    > The problem has been patched in v4.9.3.
    > 
    - `@openzeppelin/contracts` - <b>MODERATE</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-wprv-93r4-jj2p) - OpenZeppelin Contracts using MerkleProof multiproofs may allow proving arbitrary leaves for specific trees
    
    > Impact
    > 
    > 
    > When the `verifyMultiProof`, `verifyMultiProofCalldata`, `processMultiProof`, or `processMultiProofCalldata` functions are in use, it is possible to construct merkle trees that allow forging a valid multiproof for an arbitrary set of leaves.
    > 
    > A contract may be vulnerable if it uses multiproofs for verification and the merkle tree that is processed includes a node with value 0 at depth 1 (just under the root). This could happen inadvertently for balanced trees with 3 leaves or less, if the leaves are not hashed. This could happen deliberately if a malicious tree builder includes such a node in the tree.
    > 
    > A contract is not vulnerable if it uses single-leaf proving (`verify`, `verifyCalldata`, `processProof`, or `processProofCalldata`), or if it uses multiproofs with a known tree that has hashed leaves. Standard merkle trees produced or validated with the [@openzeppelin/merkle-tree](https://github.com/OpenZeppelin/merkle-tree) library are safe.
    > 
    > ### Patches
    > 
    > The problem has been patched in 4.9.2.
    > 
    > ### Workarounds
    > 
    > If you are using multiproofs: When constructing merkle trees hash the leaves and do not insert empty nodes in your trees. Using the [@openzeppelin/merkle-tree](https://www.npmjs.com/package/@openzeppelin/merkle-tree) package eliminates this issue. Do not accept user-provided merkle roots without reconstructing at least the first level of the tree. Verify the merkle tree structure by reconstructing it from the leaves.
    > 
    - `@openzeppelin/contracts` - <b>MODERATE</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-mx2q-35m2-x2rh) - OpenZeppelin Contracts TransparentUpgradeableProxy clashing selector calls may not be delegated
    
    > Impact
    > 
    > 
    > A function in the implementation contract may be inaccessible if its selector clashes with one of the proxy's own selectors. Specifically, if the clashing function has a different signature with incompatible ABI encoding, the proxy could revert while attempting to decode the arguments from calldata.
    > 
    > The probability of an accidental clash is negligible, but one could be caused deliberately.
    > 
    > ### Patches
    > 
    > The issue has been fixed in v4.8.3.
    > 
    > ### Workarounds
    > 
    > If a function appears to be inaccessible for this reason, it may be possible to craft the calldata such that ABI decoding does not fail at the proxy and the function is properly proxied through.
    > 
    > ### References
    > 
    > https://github.com/OpenZeppelin/openzeppelin-contracts/pull/4154
    > 
    - `@openzeppelin/contracts` - <b>MODERATE</b> - [Link](https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories/GHSA-5h3x-9wvq-w4m2) - OpenZeppelin Contracts's governor proposal creation may be blocked by frontrunning
    
    > Impact
    > 
    > 
    > By frontrunning the creation of a proposal, an attacker can become the proposer and gain the ability to cancel it. The attacker can do this repeatedly to try to prevent a proposal from being proposed at all.
    > 
    > This impacts the `Governor` contract in v4.9.0 only, and the `GovernorCompatibilityBravo` contract since v4.3.0.
    > 
    > ### Patches
    > 
    > The problem has been patched in 4.9.1 by introducing opt-in frontrunning protection.
    > 
    > ### Workarounds
    > 
    > Submit the proposal creation transaction to an endpoint with frontrunning protection.
    > 
    > ### Credit
    > 
    > Reported by Lior Abadi and Joaquin Pereyra from Coinspect.
    > 
    > ### References
    > 
    > https://www.coinspect.com/openzeppelin-governor-dos/
    > 
    
    <i>There is one instance of this issue:</i>
    
    ```solidity
    📁 File: src/managers/LandManager.sol
    
    1: // SPDX-License-Identifier: UNLICENSED
    
    ```

## QA 2. Useless Check in `LandManager.sol#stakeMunchable()` Function

### Summary

The check in the `LandManager.sol#stakeMunchable()` function that limits the number of staked munchables per account to 10 is ineffective and can be easily bypassed by malicious users by creating new accounts.

```solidity
if (munchablesStaked[mainAccount].length > 10) revert TooManyStakedMunchiesError();
```

**Code Reference**: [LandManager.sol#stakeMunchable](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L70)

### Impact

Malicious users can evade this check and stake more than the allowed number of munchables, potentially disrupting the intended game mechanics and fairness.

---

## QA 3. Missing Constraints for `DEFAULT_TAX_RATE` in `LandManager.sol#_reconfigure()`

### Summary

The `DEFAULT_TAX_RATE` is not constrained to be between `MIN_TAX_RATE` and `MAX_TAX_RATE` in the `LandManager.sol#_reconfigure()` function.

**Code Reference**: [LandManager.sol#_reconfigure](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L84)

### Impact

If `DEFAULT_TAX_RATE` is set outside the allowed range, it can lead to unfair reward distributions and violate the protocol’s economic constraints.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Add a check to ensure that `DEFAULT_TAX_RATE` is between `MIN_TAX_RATE` and `MAX_TAX_RATE`.

```solidity
if (DEFAULT_TAX_RATE < MIN_TAX_RATE || DEFAULT_TAX_RATE > MAX_TAX_RATE) {
    revert InvalidDefaultTaxRateError();
}

```

---

## QA 4. Codebase Should Implement Formal Verification Testing

### Summary

Formal verification is essential for proving the correctness of algorithms and ensuring the reliability of smart contracts. Tools such as SMTChecker and Certora Prover can be used for this purpose.

**References**:

- [SMTChecker](https://docs.soliditylang.org/en/latest/smtchecker.html)
- [Certora Prover](https://www.certora.com/)

### Impact

Formal verification can prevent subtle bugs and security vulnerabilities, enhancing the overall security and reliability of the smart contract.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Integrate formal verification tools into the development and testing process.

---

## QA 5. Consider Enabling `via-ir` for Enhanced Code Transparency and Auditability

### Summary

Enabling the `--via-ir` command line option in Solidity improves code transparency and auditability by using an Intermediate Representation (IR) in Yul.

**References**:

- [Solidity Documentation](https://docs.soliditylang.org/en/v0.8.20/ir-breaking-changes.html#solidity-ir-based-codegen-changes)

### Impact

Enhanced transparency and auditability can make the code easier to understand and verify, reducing the risk of unnoticed bugs and vulnerabilities.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Enable the `-via-ir` option during compilation and test thoroughly to ensure there are no semantic differences that impact the contract’s behavior.

---

## QA 6. Import Declarations Should Import Specific Identifiers

### Summary

Using import declarations that import specific identifiers avoids polluting the symbol namespace and speeds up compilation.

**Code Reference**: [LandManager.sol](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L4)

```solidity
import { ILandManager } from "../interfaces/ILandManager.sol";
import { ILockManager } from "../interfaces/ILockManager.sol";
import { IAccountManager } from "../interfaces/IAccountManager.sol";
import { BaseBlastManagerUpgradeable } from "./BaseBlastManagerUpgradeable.sol";
import { INFTAttributesManager } from "../interfaces/INFTAttributesManager.sol";
import { IERC721 } from "openzeppelin-contracts/contracts/token/ERC721/IERC721.sol";

```

### Impact

Improved namespace management and faster compilation times.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Use specific import declarations as shown above.

---

## QA 7. Not Using the Latest Versions of Project Dependencies

### Summary

Outdated dependencies can contain known vulnerabilities. It is essential to keep project dependencies up to date.

**Dependencies**:

- `forge-std`: 1.7.6 -> 1.9.1
- `forge-std`: 1.2.0 -> 1.9.1

**Tools for Auditing**:

- `retire.js`
- `npm audit`
- `yarn audit`

### Impact

Using outdated dependencies increases the risk of security vulnerabilities.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Regularly update dependencies to their latest versions and use auditing tools to check for vulnerabilities.

---

## QA 8. Unsafe ERC20 Operation(s)

### Summary

Unsafe ERC20 operations should be replaced with their safe counterparts to prevent potential failures.

**Code Reference**: [LandManager.sol](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L152)

```solidity
munchNFT.safeTransferFrom(mainAccount, address(this), tokenId);
munchNFT.safeTransferFrom(address(this), mainAccount, tokenId);

```

### Impact

Ensuring safe operations prevents failures and increases contract reliability.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Replace `transfer` and `transferFrom` with `safeTransfer` and `safeTransferFrom`.

---

## QA 9. Consider Pre-Calculating the Address of `address(this)`

### Summary

Pre-calculating and using a hardcoded address instead of `address(this)` can save gas by avoiding the additional `EXTCODESIZE` operation.

**Code Reference**: [LandManager.sol](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L149)

```solidity
address self = address(this);
!munchNFT.isApprovedForAll(mainAccount, self) &&
munchNFT.getApproved(tokenId) != self
munchNFT.transferFrom(mainAccount, self, tokenId);
munchNFT.transferFrom(self, mainAccount, tokenId);

```

### Impact

Reduced gas costs and improved efficiency.

### Tools Used

Manual Review

### Recommended Mitigation Steps

Pre-calculate the contract address and use the variable instead of `address(this)`.