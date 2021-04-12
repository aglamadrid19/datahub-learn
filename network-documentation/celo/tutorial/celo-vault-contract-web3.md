---
description: Learn how to create a Vault in Celo, and interface with it using web3.
---

# Write, deploy, and interact (web3) with your first Celo Vault Smart Contract

## Introduction

In this tutorial, we will create, deploy, and interact with our first Vault Smart Contract on the Celo Ecosystem.

We will be deploying a Vault Smart Contract to the Alfajores Testnet using DataHub and Truffle.

After, we will build a boilerplate to showcase the ContractKit integration, to interface with our Vault Smart Contract.

### Prerequisites

Since Celo runs the Ethereum Virtual Machine, we can use Truffle to compile our smart contract. First step is to make sure that you have truffle installed:

```bash
npm install -g truffle
```

Please make sure that you completed the tutorials:

1. [Connect to Celo node with DataHub](https://learn.figment.io/network-documentation/celo/tutorial/1.connect)
2. [Query the Celo Network](https://learn.figment.io/network-documentation/celo/tutorial/3.query)
3. [Submit your first transactions](https://learn.figment.io/network-documentation/celo/tutorial/4.transactions)
4. [Write and deploy your first Celo smart contract](https://learn.figment.io/network-documentation/celo/tutorial/intro-pathway-celo-basics/5.smart-contract)

## Creating project

First, we need to create a folder to place our project files. Let's run the following command:

```bash
mkdir celo-vault
```

Second, let's initialize a barebone truffle project, by running the following command.

```node
truffle init
```

OpenZeppelin Contracts is a library for secure smart contract development.
Helping us reduce the risk of vulnerabilities in our applications by using standard, tested, community-reviewed code.

Since we gathered inpiration from the above mentioned library, our Vault Smart Contracts are going to inherit some of their best practices.

To install it, run the following command from the project root directory:

```node
npm install @openzeppelin/contracts
```

## Setup project

To set things up, we will create an account and fetch it's Private Key.



Now open truffle-config.js and replace the content of this file with the following snippet:

```javascript
const ContractKit = require('@celo/contractkit');
const Web3 = require('web3');

require('dotenv').config({path: '../.env'});

// Create connection to DataHub Celo Network node
const web3 = new Web3(process.env.REST_URL);
const client = ContractKit.newKitFromWeb3(web3);

// Initialize account from our private key
const account = web3.eth.accounts.privateKeyToAccount(process.env.PRIVATE_KEY);

// We need to add private key to ContractKit in order to sign transactions
client.addAccount(account.privateKey);

module.exports = {
  networks: {
    test: {
      host: "127.0.0.1",
      port: 7545,
      network_id: "*"
    },
    alfajores: {
      provider: client.connection.web3.currentProvider, // CeloProvider
      network_id: 44787  // latest Alfajores network id
    }
  }
};
```

This instructs Truffle to use two networks: test, and Alfajores.

{% hint style="info" %}
You can change the path of .env file if you have your .env file in the same directory instead of root directory like this `require ('dotenv').config({path: '.env'});`
{% endhint %}

## Compile the smart contract

Before we can deploy the contract to the Celo network, we need to first compile the Solidity code into Ethereum bytecode. In order to do that we need to run:

```text
truffle compile
```

After this command is done running, you should see a new `build` folder inside of your project, which contains a bunch of JSON files. Please do not edit those JSON files as they are required by Truffle to work correctly.

## Deploy the smart contract

There are 2 ways to deploy your smart contract. We could use Truffle to do so or we could submit an on-chain transaction that will deploy the smart contract. We will go with the former for the purpose of this tutorial. Use below Truffle command to deploy your smart contract:

```bash
truffle migrate --network alfajores
```

If everything goes right, this should deploy your MetaCoin smart contract to the Celo Alfajores testnet using DataHub. In the output of this command, you should find a transaction hash, which you can use to lookup transactions on [Alfajores Celo Explorer](https://alfajores-blockscout.celo-testnet.org/).

## Interacting with your smart contract

Now that our MetaCoin smart contract has been deployed it is time to interact with it.

Let’s create a new file `interactions.js` with the following content:

```javascript
const ContractKit = require('@celo/contractkit');
const Web3 = require('web3');
const MetaCoin = require('./build/contracts/MetaCoin.json');

require('dotenv').config();

const main = async () => {
  // Create connection to DataHub Celo Network node
  const web3 = new Web3(process.env.REST_URL);
  const client = ContractKit.newKitFromWeb3(web3);

  // Initialize account from our private key
  const account = web3.eth.accounts.privateKeyToAccount(process.env.PRIVATE_KEY);

  // We need to add private key to ContractKit in order to sign transactions
  client.addAccount(account.privateKey);

  // Check the Celo network ID
  const networkId = await web3.eth.net.getId();

  // Get the contract associated with the current network
  const deployedNetwork = MetaCoin.networks[networkId];

  if (!deployedNetwork) {
    throw new Error(`${networkId} is not valid`);
  }

  // Create a new contract instance with the MetaCoin contract info
  let instance = new web3.eth.Contract(
    MetaCoin.abi,
    deployedNetwork.address
  );

  // Get balance
  let balanceBefore = await instance.methods.getBalance(account.address).call();
  console.log('Balance before:', balanceBefore);

  // Send tokens
  const recipientAddress = '0xD86518b29BB52a5DAC5991eACf09481CE4B0710d';
  const amount = 100;

  const txObject = await instance.methods.sendCoin(recipientAddress, amount);
  let tx = await client.sendTransactionObject(txObject, { from: account.address });

  let receipt = await tx.waitReceipt();
  console.log('Sent coin smart contract call receipt: ', receipt);

  // Get balance again
  let balanceAfter = await instance.methods.getBalance(account.address).call();
  console.log('Balance after:', balanceAfter);
};

main().catch((err) => {
  console.error(err);
});
```

There is a lot going on here so let’s break it down a little bit.

After initializing ContractKit and adding our account to it, we have to create a new contract instance using smart contract ABI and the address of the network this smart contract has been deployed to. Once this is done, we can call the `getBalance` method of our smart contract. This call should output `Balance before: 10000` as this is the initial amount assigned to our address.

Next step is to send `100` tokens to a different address. Here we first create a transaction object and then send it to the Celo Alfajores node for processing. Once this transaction is completed we call `getBalance` to check if our balance has been updated. If all went well the output should read `Balance after: 9900`.

Ok, it's time to run the script:

```bash
node interactions.js
```

Congratulations! You’ve just interacted with your own instance of a smart contract deployed to the Celo Alfajores testnet.

## Conclusion

In this tutorial, we learned quite a lot! We took a quick look at one of Ethereum’s most powerful development tool - Truffle. We used it to compile our smart contract. Then we deployed our smart contract with few lines of Javascript code and called two methods on that smart contract.

The complete code for this tutorial can be found on [**Github**](https://github.com/figment-networks/tutorials/tree/main/celo/5_contracts).

We have only covered a very small area of contract development. We invite you to keep experimenting on your own, and we will be providing more advanced Celo tutorials shortly to help you get to the next level.

[Please fill out this short form](https://docs.google.com/forms/d/1MZLA73jCka2OOBfUuLCBzKYiQtSkWUFC1W3eQ7mj1s4/viewform?edit_requested=true) to help our friends at Celo get a better understanding of who our users are!

If you had any difficulties following this tutorial or simply want to discuss Celo and DataHub tech with us you can[ join our community](https://discord.gg/Chhuv5zHy3) today!

FRONTEND TODO

-3 Add Network Connector (based ubeswap-interface)
-2 Add celo contract kit to Network Connector
-1 - Added Celo 
0 - Fix deactivate not loading stake page
1 - Add contract kit implementation