| ID | Bug Title |
| --- | --- |
| QA 1 | Useless Check in LandManager.sol#stakeMunchable() Function |
| QA 2 | Missing Constraints for DEFAULT_TAX_RATE in LandManager.sol#_reconfigure() |
| QA 3 | Codebase Should Implement Formal Verification Testing |
| QA 4 | Consider Enabling via-ir for Enhanced Code Transparency and Auditability |
| QA 5 | Import Declarations Should Import Specific Identifiers |
| QA 6 | Not Using the Latest Versions of Project Dependencies |
| QA 7 | Unsafe ERC20 Operation(s) |
| QA 8 | Consider Pre-Calculating the Address of address(this) |

### 1. Useless Check in `LandManager.sol#stakeMunchable()` Function

### Summary

The check in the `LandManager.sol#stakeMunchable()` function that limits the number of staked munchables per account to 10 is ineffective and can be easily bypassed by malicious users by creating new accounts.

```solidity
if (munchablesStaked[mainAccount].length > 10) revert TooManyStakedMunchiesError();
```

**Code Reference**: [LandManager.sol#stakeMunchable](https://github.com/code-423n4/2024-07-munchables/blob/main/src/managers/LandManager.sol#L70)

### Impact

Malicious users can evade this check and stake more than the allowed number of munchables, potentially disrupting the intended game mechanics and fairness.

---

### 2. Missing Constraints for `DEFAULT_TAX_RATE` in `LandManager.sol#_reconfigure()`

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

### 3. Codebase Should Implement Formal Verification Testing

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

### 4. Consider Enabling `via-ir` for Enhanced Code Transparency and Auditability

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

### 5. Import Declarations Should Import Specific Identifiers

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

### 6. Not Using the Latest Versions of Project Dependencies

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

### 7. Unsafe ERC20 Operation(s)

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

### 8. Consider Pre-Calculating the Address of `address(this)`

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