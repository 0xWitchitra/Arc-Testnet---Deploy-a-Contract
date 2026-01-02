# Arc-Testnet---Deploy-a-Contract
# HelloArchitect — Deploying a Smart Contract on Arc Testnet (Foundry)

> 📄 **README.md**
>
> This repository demonstrates how to write, test, and deploy a Solidity smart contract to **Arc Testnet** using **Foundry**.

This guide walks you step by step through installing Foundry, writing a simple Solidity contract, testing it, and deploying it to **Arc Testnet**.

---

## Prerequisites

* Linux / macOS / WSL
* `curl` installed
* A wallet **private key** with Arc testnet funds

> ⚠️ Never share your private key publicly.

---

## Step 1: Install Foundry

```bash
curl -L https://foundry.paradigm.xyz | bash
```

Reload your shell:

```bash
source ~/.bashrc
```

Install Foundry tools:

```bash
foundryup
```

Verify installation:

```bash
forge --version
```

---

## Step 2: Initialize a New Foundry Project

```bash
forge init hello-arc && cd hello-arc
```

This creates the following structure:

```
hello-arc/
├── src/
├── test/
├── script/
├── foundry.toml
```

---

## Step 3: Configure Environment Variables

Create a `.env` file:

```bash
nano .env
```

Paste the following:

```env
ARC_TESTNET_RPC_URL="https://rpc.testnet.arc.network"
PRIVATE_KEY="YOUR_PRIVATE_KEY_HERE"
```

Save and exit:

```
Ctrl + X → Y → Enter
```

---

## Step 4: Create the Smart Contract

### Remove the default contract

```bash
rm src/Counter.sol
```

### Create a new contract file

```bash
cd src
nano HelloArchitect.sol
```

Paste the contract code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

contract HelloArchitect {
    string private greeting;

    // Event emitted when the greeting is changed
    event GreetingChanged(string newGreeting);

    // Constructor that sets the initial greeting to "Hello Architect!"
    constructor() {
        greeting = "Hello Architect!";
    }

    // Setter function to update the greeting
    function setGreeting(string memory newGreeting) public {
        greeting = newGreeting;
        emit GreetingChanged(newGreeting);
    }

    // Getter function to return the current greeting
    function getGreeting() public view returns (string memory) {
        return greeting;
    }
}
```

Save and exit:

```
Ctrl + X → Y → Enter
```

Return to project root:

```bash
cd ..
```

---

## Step 5: Clean Default Scripts & Tests

Remove unnecessary folders/files:

```bash
rm -rf script
cd test
rm -rf Counter.t.sol
```

---

## Step 6: Write Contract Tests

Create a new test file:

```bash
nano HelloArchitect.t.sol
```

Paste the test code:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.30;

import "forge-std/Test.sol";
import "../src/HelloArchitect.sol";

contract HelloArchitectTest is Test {
    HelloArchitect helloArchitect;

    function setUp() public {
        helloArchitect = new HelloArchitect();
    }

    function testInitialGreeting() public view {
        string memory expected = "Hello Architect!";
        string memory actual = helloArchitect.getGreeting();
        assertEq(actual, expected);
    }

    function testSetGreeting() public {
        string memory newGreeting = "Welcome to Arc Chain!";
        helloArchitect.setGreeting(newGreeting);
        string memory actual = helloArchitect.getGreeting();
        assertEq(actual, newGreeting);
    }

    function testGreetingChangedEvent() public {
        string memory newGreeting = "Building on Arc!";

        vm.expectEmit(true, true, true, true);
        emit HelloArchitect.GreetingChanged(newGreeting);

        helloArchitect.setGreeting(newGreeting);
    }
}
```

Save and exit:

```
Ctrl + X → Y → Enter
```

---

## Step 7: Run Tests

```bash
forge test
```

✅ All tests should pass successfully.

---

## Step 8: Build the Contract

```bash
cd ..
forge build
```

---

## Step 9: Load Environment Variables

```bash
source .env
```

---

## Step 10: Deploy Contract to Arc Testnet

```bash
forge create src/HelloArchitect.sol:HelloArchitect \
  --rpc-url $ARC_TESTNET_RPC_URL \
  --private-key $PRIVATE_KEY \
  --broadcast
```

After successful deployment, you will see:

* Contract address
* Transaction hash

---

## Step 11: Verify Deployment on Arc Explorer

1. Copy the **transaction hash**
2. Open:

👉 [https://testnet.arcscan.app/](https://testnet.arcscan.app/)

3. Paste the transaction hash to view your deployment

---

## 🎉 Deployment Complete!

---

## 📂 Repository Structure

```
hello-arc/
├── src/
│   └── HelloArchitect.sol
├── test/
│   └── HelloArchitect.t.sol
├── .env
├── foundry.toml
└── README.md
```

You have successfully:

* Written a Solidity contract
* Tested it using Foundry
* Deployed it to **Arc Testnet**

Happy building on Arc 🚀

---

## 🚀 One‑Click Deployment Script (Bash)

Create a file called `deploy_arc.sh` in the project root and paste the script below.

```bash
#!/usr/bin/env bash
set -e

# ========== CONFIG CHECK ==========
if ! command -v forge >/dev/null 2>&1; then
  echo "❌ Foundry not installed. Installing Foundry..."
  curl -L https://foundry.paradigm.xyz | bash
  source ~/.bashrc
  foundryup
fi

# ========== ENV CHECK ==========
if [ ! -f .env ]; then
  echo "❌ .env file not found"
  echo "Create a .env file with:"
  echo "ARC_TESTNET_RPC_URL=..."
  echo "PRIVATE_KEY=..."
  exit 1
fi

source .env

if [ -z "$ARC_TESTNET_RPC_URL" ] || [ -z "$PRIVATE_KEY" ]; then
  echo "❌ Missing ARC_TESTNET_RPC_URL or PRIVATE_KEY in .env"
  exit 1
fi

# ========== TEST ==========
echo "🧪 Running tests..."
forge test

# ========== BUILD ==========
echo "🏗️  Building contract..."
forge build

# ========== DEPLOY ==========
echo "🚀 Deploying HelloArchitect to Arc Testnet..."
forge create src/HelloArchitect.sol:HelloArchitect \
  --rpc-url "$ARC_TESTNET_RPC_URL" \
  --private-key "$PRIVATE_KEY" \
  --broadcast

echo "✅ Deployment finished"

```
