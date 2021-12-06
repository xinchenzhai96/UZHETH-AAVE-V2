## What is this repository for
This repository is forked from the [Aave Protocol V2 repository](https://github.com/aave/protocol-v2). It got created due a project at our university. Our focus was to deploy Aave Protocol V2 onto the network of our university, which is not connected to mainnet. Since we struggled to do this with the offical documentation, we wanted to provide our experience to other people with same interest. However, due to our lack of complete understanding of this matter, not everything might be correct and we won't take responsibility. In any case, make sure to contact Aave directly if you need help.
Note: Workflow has been removed from this forked repository

## What is Aave?
Aave is a decentralized non-custodial liquidity markets protocol where users can participate as depositors or borrowers. Check out their repository for further information.

## Documentation
The documentation of Aave V2 is in the following [Aave V2 documentation](https://docs.aave.com/developers/v/2.0/) link. At the documentation you can learn more about the protocol, see the contract interfaces, integration guides and audits. Documentation how to interact with test networks is in the [original repository](https://github.com/aave/protocol-v2).

A more detailed and technical description of the protocol can be found [here](./aave-v2-whitepaper.pdf)

## Last Note
If you have access to the uzheth network and you're interested to redeploy AAVE Protocol V2, ignore the chapters encapsulated with "( )". 

## 1. Setup
1. Fork the repository
2. Make sure Docker is installed on your device. You can find it at this [link](https://www.docker.com/)  
3. To install hardhat, you need npm to work on your device (if not available, install node.js). Aave repository comes already with a `package-lock.json` file, where hardhat is included, so you only have to type `npm install` in your console.
4. To compile, follow these steps:
  - Create `env.` file inside the root of the forked repository and fill it up with following information:
```
# Mnemonic, only first address will be used //IMPORTANT for UZHETH
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

## (2. Create new market)
1. Define your network 
  - in `helper-hardhat-config.ts` (`NETWORKS_RPC_URL`, `NETWORK_DEFAULT_GAS`, `BLOCK_TO_FORK`)
  - in `hardhat.config.ts` such that the object `networks` is extended with your network. For this use `getComonNetworkConfig(...)` and make sure all necessary params are provided

2. Create a new folder in `./markets`. Feel free to copy `uzheth` market (which in its core is a copy of `aave` market) and rename it accordingly. 
3. Update all important params and objects such as:
  - Emergency admin
    - add your address in the corresponding constant in the `env.` file
    - If you didn't copy uzheth market, you have to make sure this provided address will be called accordingly.
  - ChainlinkAggregator
    - See **Deploy Mock Oracles** further down 
  - ReserveAssets
    - See **Create ERC20 Token** further down 
  - Its own market config (in our case `uzhethConfig`). You can find this in `./index.ts` of your market.
  - Add your market to several enums and switches
 
## (3. Create new Migration task)
1. Copy migration task of `uzheth` market. You can find this under `./tasks/migration/uzheth.mainnet.ts` (it's once again in its core a copy of the aave.mainnet task)
  - Change certain parameters such as but not limit to: `POOL_NAME`
2. Create your command based on the other conventions in `package.json` thus add something like: `"yourMarket:full:migration": "npm run compile && npm run hardhat:yourMarket -- yourMarket:mainnet --skip-registry"`

## 4. Deploy
5. Run in one terminal: `docker-compose up`
6. Run in second terminal: `docker-compose exec contracts-env bash`
7. In that console, type: `npm run yourMarket:full:migration`
  - in case of uzheth: yourMarket = `uzheth`

## Further Consideration
### (Deploy Mock Oracles)
If you like to deploy Mock oracles, you can follow these steps:
1. Run in one terminal: `docker-compose up`
2. Run in second terminal: `docker-compose run contracts-env npx hardhat --network yourNetwork console`
3. In that console, type: `run(‘set-DRE’)`
4. Then: `require(‘PATH/oracles-helpers.ts’).deployAllMockAggregators(require(‘PATH/constants.ts’).MOCK_CHAINLINK_AGGREGATORS_PRICES, false)`

- **Note 1**: If your current directory is: *yourPath/UZHETH-AAVE-V2*, then *PATH* above stands for: `./helpers/`
The resulting addresses have to be added to the object ChainlinkAggregator in the directory `./markets/yourMarket/commons.ts`
- **Note 2**: To our understanding, these oracles will only be considered when there's also an ERC20 Token existing and defined in `ReserveAssets`. Otherwise they will be skipped anyway during deployment.

### (Create ERC20 Token)
Your tokens for *Reserve Assets* need to be ERC20 compliant. Make sure to deploy it on your network accordingly. You can also use WETHmocked, for testing purposes. See more under this [link](https://github.com/aave/protocol-v2/tree/master/contracts/mocks/tokens).

Add the address of your token to `ReserveAssets` in your market

## Road Map for uzheth network
aTokens are generated and one can interact with the protocol (see interaction explanation in the original repository or our report). 
However there are still a few problems:
  - Calls have to be provided with additional argument `{gasPrice: 100, gasLimit: 90000}` otherwise you'll get *UNPREDICTABLE_GAS_LIMIT* error
  - Depositing UZHETH (our ERC20 token) seems to work but the transaction is not processed behind-the-scenes. Due the lack of time of our project, we couldn't solve this problem
  - Mock Oracles are deployed instead of real Oracles

## Credits
Part of this project were:
- Micha Eschmann
- Viktor Lakic
- Yifei Liu

Thanks for the support during the seminar:
- Matija Piškorec
- Tao Yan

Special thanks to Aave support Discord channel, especially "shiv" (Discord name), for all the help

## Further help
During our project, we got provided with the following [document](https://aave.notion.site/Deploy-New-Market-de7d0e4c30034da2b8b46c2c14a3d0df) after we requested more help. It might be helfpul as a further explanation how to proceed.


