## Part -1 : Create and deploy a DeFi App

In this tutorial we will build a DeFi Application with Solidity where users can deposit an **ERC20** token to the smart contract and it will mint and transfer Farm Tokens to them. The users can later withdraw their ERC20 tokens by burning their Farm Token on smart contract and the ERC20 tokens will be transferred back to them.

### Install Truffle and Ganache

Truffle is a development environment and testing framework for developing smart contracts for Ethereum. With Truffle it is easy to build and deploy smart contracts to the blockchain. Ganache allows us to create a local Ethereum blockchain in order to test smart contracts. It simulates the features of the real network and the first 10 accounts are funded with 100 test ether (🤑), thus making the smart contract deployment and testing free and easy.


![1_V1iQ5onbLbT5Ib2QaiOSyg.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1635149551172/-vVTaLy_5.png)
*Ganache UI desktop application*

To create the project, run below commands
```
mkdir My-DeFi
cd My-DeFi
truffle init
``` 
The created project structure is the following:

- contracts: Folder for the solidity smart contracts

- migrations: Folder for the deployment scripts

- test: Folder for testing our smart contracts

- truffle-config.js: Truffle configuration file

### **Create the ERC20 Token**

First we need to create our ERC20 token that we will use to stake on the smart contract.


- We named our token as Desi Token. ₿ 


```
pragma solidity ^0.6.2;

contract DesiToken {
    string  public name = "Desi Token";
    string  public symbol = "DSI";
    uint256 public totalSupply = 1000000000000000000000000; // 1 million tokens
    uint8   public decimals = 18;

    event Transfer(
        address indexed _from,
        address indexed _to,
        uint256 _value
    );

    event Approval(
        address indexed _owner,
        address indexed _spender,
        uint256 _value
    );

    mapping(address => uint256) public balanceOf;
    mapping(address => mapping(address => uint256)) public allowance;

    constructor() public {
        balanceOf[msg.sender] = totalSupply;
    }

    function transfer(address _to, uint256 _value) public returns (bool success) {
        require(balanceOf[msg.sender] >= _value);
        balanceOf[msg.sender] -= _value;
        balanceOf[_to] += _value;
        emit Transfer(msg.sender, _to, _value);
        return true;
    }

    function approve(address _spender, uint256 _value) public returns (bool success) {
        allowance[msg.sender][_spender] = _value;
        emit Approval(msg.sender, _spender, _value);
        return true;
    }

    function transferFrom(address _from, address _to, uint256 _value) public returns (bool success) {
        require(_value <= balanceOf[_from]);
        require(_value <= allowance[_from][msg.sender]);
        balanceOf[_from] -= _value;
        balanceOf[_to] += _value;
        allowance[_from][msg.sender] -= _value;
        emit Transfer(_from, _to, _value);
        return true;
    }
}
``` 
### Compile the ERC20 Token

To compile our smart contract, we must first check our solidity compiler version. You can check that by running the command:

```
truffle version
``` 
The default version is the Solidity v0.5.16. Since our token is written using the solidity version 0.6.2, if we run the command to compile our contracts we will get a compiler error. In order to specify which solidity compiler version to use, go to the file truffle-config.js and set to the desired compiler version as seen below:


```
compilers: {
  solc: {
    version: "^0.8.0",    
    //docker: true,        
    // settings: {          // See the solidity docs for advice about optimization and evmVersion
    //  optimizer: {
    //    enabled: false,
    //    runs: 200
    //  },
    //  evmVersion: "byzantium"
    // }
  }
}
``` 
Now we can compile our smart contract by running the following command:

```
truffle compile
``` 
### **Deploy ERC20 Token**

On the migrations folder, create a file called 2_deploy_Tokens.js. This file is where we will deploy both our ERC20 Token and our FarmToken smart contract. The code below is used to deploy our MyToken.sol contract:


```
const DToken = artifacts.require("DesiToken")

module.exports = async function (deployer, network, accounts) {
  // Deploy DToken
  await deployer.deploy(DToken)
  const dToken = await DToken.deployed()
}
``` 
To deploy our contract, run:

```
truffle migrate
``` 
In order to interact with our smart contract, run the following command:

```
truffle console
``` 
Now we can write the following commands in the terminal:

- Get the smart contract: dToken = await DToken.deployed()

- Get the array of accounts from Ganache: accounts = await web3.eth.getAccounts()

- Get the balance for the first account: balance = await dToken.balanceOf(accounts[0])

- Format the balance from 18 decimals: web3.utils.fromWei(balance.toString())

*First address has 1000000 DesiTokens*

So, that's for now. Get ready for **Part-2**.























 