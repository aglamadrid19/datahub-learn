# Introduction

In the below tutorial, we will learn how to create a MATIC Staking dApp on Polygon to reward users who stake their MATIC with our token MANGO. We will cover the deployment of the smart contracts and the frontend for the Polygon network.

# Prerequisites

To follow through this tutorial, you need a basic knowledge and understanding of blockchain, Solidity smart contracts and React.

# Requirements

- You will need Metamask installed in your browser from the official site [https://metamask.io](https://metamask.io).

**Other technologies used in this tutorial:**
- [Git](https://git-scm.com/) - Git is a free and open source distributed version control system
- [Node](https://nodejs.org/en/) - Node.js is a JavaScript runtime built on the V8 JavaScript engine.
- [Yarn](https://yarnpkg.com/) - Yarn is a package manager for your code
- [Solidity](https://docs.soliditylang.org/en/v0.8.7/) - For writing smart contracts
- [Hardhat](https://hardhat.org/) - Hardhat is a development environment to compile, deploy, test, and debug your Ethereum software
- [ReactJS](https://reactjs.org/) - For building our user interface
- [Ethers.js](https://docs.ethers.io/v5/) - Complete and compact library for interacting with the Ethereum Blockchain and its ecosystem
- [Polygon Network](https://polygon.technology/) - For deploying our smart contract

**Topics covered in this tutorial:**

- Cloning and overview of our smart contract enviroment
- Cloning and overview of our frontend user interface
- Running the tests for our smart contracts
- Deploying smart contracts to Polygon Mumbai (Testnet)
- Linking smart contract to frontend using ethers.js
- Testing out our dApp

# Project setup

For this tutorial we have prepared 2 separate repositories (smart contracts and frontend)which include all the code and setup. We will explain the core parts of each of them so you can deploy it on your own, make modifications, or make improvements.

```bash
# Let's download our smart contracts repo
git clone https://github.com/aglamadrid19/la-finca-smart-contracts.git

# Let's now download our frontend repo
git clone https://github.com/aglamadrid19/la-finca-frontend.git
```

After downloading both repos you should see the following on your current directory:

![Folder Structure](https://la-finca-tutorial.s3.filebase.com/list-directory.png)

Next, let's `cd` inside our smart contracts folder, once inside it should look like this:

![Folder Structure](https://la-finca-tutorial.s3.filebase.com/smart-contracts-list-directory.png)

On front of us we have the following directories, let's describe them:

- `contracts/`: All the smart contracts are stored in this directory.
- `scripts/`: Scripts to deploy our contracts once we are ready.
- `test/`: All the test scripts for smart contracts are stored in this directory.
- `hardhat.config.js`: Contains configuration settings for hardhat.

Let's now install all dependencies for our project running:
```bash
# Install dependencies
yarn
```

After succesfully installing dependencies the output should look like this:

![Dependency-Output](https://la-finca-tutorial.s3.filebase.com/dependencies-installed.png)

Now our smart contract repository is reay but we will take a step back and do the same for the frontend (install dependencies), let's run the following:

![Change-Directory](https://la-finca-tutorial.s3.filebase.com/change-to-frontend.png) 

Now once inside our frontend repo let's run `yarn` again to install dependencies. The expected output is below:

![Change-Directory](https://la-finca-tutorial.s3.filebase.com/frontend-dependencies.png) 

Congratulations, we are done with our Project Setup, next we will dive deeper into each of the repositories.

# Smart Contracts Overview

Going back to our smart contracts repository, which we do as follows inside, let's go inside our folder `contracts` :

![Change-Directory](https://la-finca-tutorial.s3.filebase.com/smart-contract-list-files.png) 

- `LaFincaMaticStaking.sol` This contract is a fork of the widely known Pancakeswap WBNB staking contract. In a nutshell this contract allows you to deposit MATIC, and in exchange it provides you with Mango which is the second token you see. The reward is assign to the user on a per block basis, and it dependes on the amount of the stake of the user. Example, if user 1 stakes 10 MATIC and user 2 does the same, then each block each user will get 0.5 Mango each. Given the fact we will reward 1 Mango per block.

- `MangoToken.sol` This contract is our reward token, when user stake their MATIC in our `LaFincaMaticStaking.sol` they will be rewarded this token on a per block basis.

- `libs` Folden contains dependencies needed for the proper functionality of our contracts, I invite you to dive into the code and take a look how they are used.

# Testing and Deploying Contracts with Hardhat

Now that we understand our contracts is time to test them using Hardhat. Let's go one directory back to the root of our smart contracts folder as follows:

![Change-Directory](https://la-finca-tutorial.s3.filebase.com/back-to-smart-contracts-folder.png)

From here we are going to run one command and this one should run both of our tests
- LaFincaMaticStaking Test
- MangoToken Test

```bash
npx hardhat test
```

After successfully running the tests you should see the following in your console.

![Change-Directory](https://la-finca-tutorial.s3.filebase.com/test-smart-contracts.png)

With this we are now ready to deploy our smart contracts, and inside our script folder we have exactly what we need. A script to deploy our contracts.

Make sure inside the `.env` file there is a private key with enough funds for the deployment. The Script will deploy and verify the contracts. This is how you run it

```bash
npx hardhat run scripts/deploy_mango_token.js --network mumbai
```

The expected output is as follows:
![Change-Directory](https://la-finca-tutorial.s3.filebase.com/deploy-contracts-screenshot.png)

# Deploying frontend and connecting the contracts

Going back to our frontend folder we need to add contract address in diferent places.
