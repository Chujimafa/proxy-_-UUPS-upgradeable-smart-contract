# 🧱 UUPS Upgradeable Smart Contract Demo

This project demonstrates a simple **UUPS (Universal Upgradeable Proxy Standard)** pattern using **Foundry**, **OpenZeppelin**, and Solidity. It includes deploying an initial logic contract (`BoxV1`), upgrading it to a new version (`BoxV2`), and testing the full upgrade workflow.

---

## 📁 Project Structure

```
├── script/
│ ├── DeployBox.s.sol // Deploys BoxV1 + ERC1967 Proxy
│ └── UpgradeBox.s.sol // Deploys BoxV2 and upgrades the proxy
├── src/
│ ├── BoxV1.sol // Initial implementation contract
│ └── BoxV2.sol // Upgraded implementation
├── test/
│ └── DelpoyAndUpgardeTest.t.sol // Tests for deployment and upgrade
```
## 🛠️ Scripts Overview

### 🔧 DeployBox.s.sol – Deploy Initial Implementation and Proxy
This script deploys:

- The logic contract (BoxV1)
- An ERC1967Proxy that points to BoxV1

The proxy is deployed without initialization data, meaning you must call the initialized() function separately to set up ownership and proxy functionality.
```
BoxV1 box = new BoxV1(); // Logic contract
ERC1967Proxy proxy = new ERC1967Proxy(address(box), ""); // Proxy
```

### ♻️ UpgradeBox.s.sol – Upgrade Proxy to New Implementation

This script performs a UUPS upgrade of the proxy contract:
- Deploys the new implementation contract (BoxV2)
- Retrieves the address of the **most recently deployed** proxy using Foundry DevOps Tools
- Upgrades the proxy to use the new implementation
```
address proxy = DevOpsTools.get_most_recent_deployment("ERC1967Proxy", block.chainid);
BoxV2 newBox = new BoxV2();
proxy.upgradeToAndCall(address(newBox), "");
```

## 🧪 Test Overview
**Goals**:
Confirm that the proxy starts as a BoxV1 instance
Attempting BoxV2.setNumber() before upgrade should fail

**After upgrade**:
version() returns 2
setNumber() and getNumber() work correctly

## 🔒 Security Notes
- The _authorizeUpgrade() function is protected by onlyOwner, restricting upgrades to the contract owner.
- _disableInitializers() in the constructor prevents misuse; initialization is performed manually.

