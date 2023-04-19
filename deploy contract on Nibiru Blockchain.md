# Quick start guide for Deploying contract on Nibiru Blockchain
Based on the instructions of Smart Contract Tasks in Nibiru Incentivized Testnet #2, the following content will guide you step by step to deploy and execute a sample contract on the Nibiru Blockchain.


Nibiru is a POS (Proof of Stake) blockchain built on the Cosmos platform. As such, programming and deploying contracts on Nibiru is similar to other blockchains in the Cosmos ecosystem.
To deploy the contract, we will first need to upload the contract file to the blockchain then declare a contract using that contract file. Details follow the steps below:

# Prepare the environment
* OS: Ubuntu 22.04 
* Sample contract: cw20_base.swap (CW20_base is a basic smart contract on the Cosmos network for deploy new Token)

# Step 1 - Install Nibiru cli (if not already)
Update lib
```
sudo apt update -y
sudo apt upgrade -y
sudo apt install curl make clang pkg-config libssl-dev build-essential git jq ncdu bsdmainutils -y
```
Install Nibiru cli
```
cd ~
curl -s https://get.nibiru.fi/! | bash
```
Point the config of the nibid binary to the incentivized testnet.
```
nibid config node https://rpc.itn-1.nibiru.fi:443
nibid config chain-id nibiru-itn-1
nibid config broadcast-mode block
```
# Step 2 - Prepare Nibiru account
You need a Nibiru address with a balance of about 10 Nibiru. Nibiru can be earned on faucet discord channel

Import Wallet
```
saod keys add wallet_name --recover
```
Enter your seed phrase and then a password to import wallet

# Step 3 - Download sample contract file
```
wget https://github.com/NibiruChain/cw-nibiru/raw/main/artifacts-cw-plus/cw20_base.wasm
```
".wasm" format is the smart contract file after it has been built. In this case, we use the available eg file provided by Nibiru Team.
# Step 4 - Deploy contract file (upload/store contract file to Nibiru Blockchain) 
```
KEY_NAME="wallet_name"           # ← Replace with your wallet name (step 2)
CONTRACT_WASM="cw20_base.wasm"   # ← Name of wasm file 
NIBI=000000unibi
nibid tx wasm store $CONTRACT_WASM --from $KEY_NAME --gas=25000000 --fees=2$NIBI
```
You may notice something different from the Nibiru Team instructions. 
Firstly, gas fees have been adjusted higher to ensure transactions are made. 
Second, we don't use the option to output json file because it will cause every json incompatibility error. 
```
parse error: Invalid numeric literal at line 1, column 5
```
You will be asked for a password to be able to deploy the contract. After proceeding, if we receive code 0 it means we have succeeded.

In the returned data find and save the code_id. That is the id of the contract file we just saved successfully. 
This code_id will be needed for later use. 
You can find it in the json snippet like below
```
{"key":"code_id","value":"21925"}
```
# Step 5 - Prepare Contract
Set environment variables
```
code_id=21925
```
Create a configuration file for our contract. Let's create a file named "cw20_config.json". 
```
vi cw20_config.json
```
Enter the file content as follows 
```
{
  "name": "BitBeri CW20 token",
  "symbol": "BERI",
  "decimals": 6,
  "initial_balances": [
    {
      "address": "YOUR_Nibiru_ADDRESS-nibi1***",
      "amount": "10000000000"
    }
  ],
  "mint": { "minter": "YOUR_Nibiru_ADDRESS-nibi1***" },
  "marketing": {}
}
```
The contents of this json file are the parameters to initialize the contract. 
It is not fixed for every contract but it depends on the specific requirement in the source code of the contract. 
For cw20_base you can learn more at the project's repo: [CW20 Spec](https://github.com/CosmWasm/cw-plus/blob/main/packages/cw20/README.md)

# Step 6 - Instantiate Contract
```
nibid tx wasm inst $code_id "$(cat cw20_config.json)" --label="mint BERI contract" --no-admin --from=$KEY_NAME --fees=50000unibi
```
If successful, you will get code = 0 and the contract information is returned.

Of these, the most important is contract_address find and save it. contract_address is the address where you or others can interact with your contract.

You'll find contract_address in a string that looks like this:
```
- key: _contract_address
      value: nibi14ks4g65df4gdf65g6dfg86đfgfsdfhfjghjghjfg5hfghtrjj4gh5j4pp
```
# Done !
you have completed the deployment of a smart contract on Nibiru Blockchain

# Let try to execute your contract
Now that your wallet address has the right to distribute BERI tokens, we will try to distribute some tokens to another wallet address.

Init env
```
CONTRACT="nibi14ks4g65df4gdf65g6dfg86đfgfsdfhfjghjghjfg5hfghtrjj4gh5j4pp" # ← Replace with your contract address
```
Create transfer input file
```
vi cw_transfer.json
```
With the content
```
{
  "transfer": {
    "recipient": "nibi1dfgdfsgsdff6g5sdf6jklhdfg56fh2dfg6gh5fgh",
    "amount": "5000000"
  }
}
```
Execute transaction
```
nibid tx wasm execute $CONTRACT "$(cat cw_transfer.json)" --from $KEY_NAME --gas 8000000 --fees=200000unibi -y
```
If you receive code 0, congratulations on completing the task for the 2nd phase of Nibiru Incentivized Testnet
# Reference
https://nibiru.fi/blog/posts/010-itn-2-cosmwasm-governance.html

# Note
If you encounter any problems or difficulties, feel free to create an issue in this repo. 

Thanks




