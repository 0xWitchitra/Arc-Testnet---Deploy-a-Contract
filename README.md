# ARC TESTNEST: CONTRACT DEPLOYMENT


> 📄 **README.md**


## Prerequisites

* First open and login your github
* Go to [github.com/codespaces](https://github.com/codespaces)
* Open blank template
* Go to [Faucet](https://faucet.circle.com/) -> take faucet in the wallet that you're using to deploy the contract

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

##  Deployment Complete!

---
