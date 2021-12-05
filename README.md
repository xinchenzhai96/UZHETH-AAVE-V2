This repository is forked from the [Aave Protocol V2 repository](https://github.com/aave/protocol-v2). Our focus was to deploy a market and provide the necessary information so that other people can replicate it, this because the documentation for this in the original repository is limited. Some part of this readme is 1:1 copied from the Aaave Protocol V2 repository. 
It contains the smart contracts source code and markets configuration for Aave Protocol V2. The repository uses Docker Compose and Hardhat as development enviroment for compilation, testing and deployment tasks.

## What is Aave?

Aave is a decentralized non-custodial liquidity markets protocol where users can participate as depositors or borrowers. Depositors provide liquidity to the market to earn a passive income, while borrowers are able to borrow in an overcollateralized (perpetually) or undercollateralized (one-block liquidity) fashion.

## Documentation

The documentation of Aave V2 is in the following [Aave V2 documentation](https://docs.aave.com/developers/v/2.0/) link. At the documentation you can learn more about the protocol, see the contract interfaces, integration guides and audits. Documentation how to interact with test networks is in the [original repository](https://github.com/aave/protocol-v2).

For getting the latest contracts addresses, please check the [Deployed contracts](https://docs.aave.com/developers/v/2.0/deployed-contracts/deployed-contracts) page at the documentation to stay up to date.

A more detailed and technical description of the protocol can be found in this repository, [here](./aave-v2-whitepaper.pdf)

## Setup
1. Fork the repository
2. Make sure Docker is installed on your device. You can find it at this [link](www.docker.com)  
3. To install hardhat, you need npm to work on your device (if not available, install node.js). AAVE repository comes already with a package-lock.json file, where hardhat is included, so you only have to type `npm install` in your console.
4. To compile, follow these steps:
  - Create env. file inside the root of the forked repository and fill it up with following information:
```
# Mnemonic, only first address will be used //IMPORTANT UZHETH
MNEMONIC=""

# Emergency Account - something like "0x ..." //IMPORTANT for UZHETH
EMERGENCY_ACCOUNT = ""

# Add Alchemy or Infura provider keys, alchemy takes preference at the config level //ignore for UZHETH deployment
ALCHEMY_KEY=""
INFURA_KEY=""

# Optional Etherscan key, for automatize the verification of the contracts at Etherscan //ignore for UZHETH deployment
ETHERSCAN_KEY=""

# Optional, if you plan to use Tenderly scripts //ignore for UZHETH deployment
TENDERLY_PROJECT=""
TENDERLY_USERNAME=""
```
  - Open terminal and type: `docker-compose up`
  - Open second terminal and type: `run export SKIP_LOAD="true" npx hardhat compile`

## Create new market
1. Define your network 
  - in `helper-hardhat-config.ts` (`NETWORKS_RPC_URL`, `NETWORK_DEFAULT_GAS`, `BLOCK_TO_FORK`)
  - in `hardhat.config.ts` such that the object `networks` is extended with your network. For this use `getComonNetworkConfig(...)` and make sure all necessary params are provided

2. Create a new folder in `./markets`. Feel free to copy `uzheth` market (which in its core is a copy of `aave` market) and rename it accordingly. 
3. Update all important params and objects such as:
  - Emergency admin
    - add your address in the corresponding constant in the `env.` file
    - If you didn't copy uzheth market, you have to make sure this provided address will be called accordingly.
  - ChainlinkAggregator
    - See Deploy Mock Oracles further down 
  - ReserveAssets
    - See Deploy ERC20 Token further down 
  - its own market config (in our case uzhethConfig)
  - add your market to several enums and switches
 
## Create new Migration task
1. 

5. Run in one terminal: `docker-compose up`
6. Run in second terminal: `docker-compose exec contracts-env bash`
7. In that console, type: `npm run uzheth:full:migration`
 
## Deploy Mock Oracles
If you like to deploy Mock oracles again for some reason, you can follow these steps:
1. Run in one terminal: `docker-compose up`
2. Run in second terminal: `docker-compose run contracts-env npx hardhat --network uzheth console`
3. In that console, type: `run(‘set-DRE’)`
4. Then: `require(‘PATH/oracles-helpers.ts’).deployAllMockAggregators(require(‘PATH/constants.ts’).MOCK_CHAINLINK_AGGREGATORS_PRICES, false)`

Note: If you are current directory is: yourPath/UZHETH-AAVE-V2, then PATH above stands for: `./helpers/`
The resulting addresses have to be added to the object ChainlinkAggregator in the directory `./markets/uzheth/commons.ts`


