---
description: for batch transactions
---

# execTransactionsFromModule

### Dependency

In this example, we are using `TypeScript` as the programming language. Please install the following dependencies first:

* [web3.js](https://web3js.readthedocs.io/en/v1.7.4/)
* @ethereumjs/tx
* @ethereumjs/common

Let's simulate the "Remove Liquidity" function on Uniswap on the Goerli network, removing liquidity for UNI-WETH pair.

We will use the `decreaseLiquidity` and `collect` method of the [NonfungiblePositionManager](https://goerli.etherscan.io/address/0xC36442b4a4522E871399CD717aBDD847Ab11FE88) contract. Please first set the [Role](../set-role.md) and [Member](../set-member.md). (for the detailed configurations for methods and parameters, you may refer to the Best Practice of [Uniswap](../best-practices/defi-dex-uniswap-v3.md))

### Example

We use [Infura](https://www.infura.io/zh) as the provider. You may choose the provider you like:

```typescript
import Web3 from 'web3'

const moduleAddress = 'YOUR_COMPASS_SAFE_MODULE_ADDRESS'
const safeAddress = 'YOUR_GNOSIS_SAFE_ADDRESS'
const INFURA_KEY = 'YOUR_INFURA_PROJECT_ID'
const provider = new Web3.providers.HttpProvider(`https://goerli.infura.io/v3/${INFURA_KEY}`)
const senderAddress = 'YOUR_MEMBER_ADDRESS'
const privateKey = Buffer.from('YOUR_MEMBER_PRIVATE_KEY', 'hex')
```

Execute the `execTransactionsFromModule` method of the Compass Safe Module:

```typescript
import { AbiItem } from 'web3-utils'
import { TransactionReceipt } from 'web3-core'
import { Transaction } from '@ethereumjs/tx'
import { Chain, Common } from '@ethereumjs/common'
import { ethers } from 'ethers'

enum Operation {
  None,
  Call,
  DelegateCall,
  Both,
}

interface Call {
  roleName: string,
  to: string,
  value: string
  data: string,
  operation: Operation,
}

const execTransactionsFromModule = async (
  calls: Call[],
): Promise<TransactionReceipt> => {
  const web3 = new Web3(provider)
  const abi = [{
    "inputs": [
      {
        "components": [
          {
            "internalType": "bytes32",
            "name": "roleName",
            "type": "bytes32"
          },
          {
            "internalType": "address",
            "name": "to",
            "type": "address"
          },
          {
            "internalType": "uint256",
            "name": "value",
            "type": "uint256"
          },
          {
            "internalType": "bytes",
            "name": "data",
            "type": "bytes"
          },
          {
            "internalType": "enum Operation",
            "name": "operation",
            "type": "uint8"
          }
        ],
        "internalType": "struct SafeModule.Exec[]",
        "name": "execs",
        "type": "tuple[]"
      }
    ],
    "name": "execTransactionsFromModule",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },]
  const contract = new web3.eth.Contract(abi as AbiItem[], moduleAddress)
  const encodedData = contract.methods['execTransactionsFromModule'](calls).encodeABI()
  const nonce = await web3.eth.getTransactionCount(senderAddress)
  const rawTx = {
    nonce,
    to: moduleAddress,
    data: encodedData,
    gasPrice: 2162230808,
    gasLimit: 256312,
    value: 0,
  }
  const common = new Common({ chain: Chain.Goerli })
  const tx = Transaction.fromTxData(rawTx, { common })

  const signedTx = tx.sign(privateKey)
  const serializedTx = signedTx.serialize()
  return (
    web3.eth.sendSignedTransaction('0x' + serializedTx.toString('hex'))
      .once('sending', function (payload) { console.log('sending transaction') })
      .once('sent', function (payload) { console.log('sent transaction') })
      .once('transactionHash', function (hash) { console.log('transactionHash: ', hash) })
  )
}
```

Please note: to obtain the parameter roleName, we can directly retrieve it from the role list in Compass Safe, or compile it based on the Role Name.

```typescript
const name = "Uniswap V3 Mint"
const roleName = ethers.utils.formatBytes32String(name)
```

Use `execTransactionsFromModule` to call `decreaseLiquidity` and `collect` method

```typescript
const decreaseLiquidity = async () => {

    const UNI = '0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984'
    const EMPTY_ADDRESS = '0x0000000000000000000000000000000000000000'
    const uniswapV3NftManagerAddress = '0xc36442b4a4522e871399cd717abdd847ab11fe88'

    const abi = [
        {
            "anonymous": false,
            "inputs": [
                {
                    "indexed": true,
                    "internalType": "uint256",
                    "name": "tokenId",
                    "type": "uint256"
                },
                {
                    "indexed": false,
                    "internalType": "uint128",
                    "name": "liquidity",
                    "type": "uint128"
                },
                {
                    "indexed": false,
                    "internalType": "uint256",
                    "name": "amount0",
                    "type": "uint256"
                },
                {
                    "indexed": false,
                    "internalType": "uint256",
                    "name": "amount1",
                    "type": "uint256"
                }
            ],
            "name": "DecreaseLiquidity",
            "type": "event"
        },
        {
            "anonymous": false,
            "inputs": [
                {
                    "indexed": true,
                    "internalType": "uint256",
                    "name": "tokenId",
                    "type": "uint256"
                },
                {
                    "indexed": false,
                    "internalType": "address",
                    "name": "recipient",
                    "type": "address"
                },
                {
                    "indexed": false,
                    "internalType": "uint256",
                    "name": "amount0",
                    "type": "uint256"
                },
                {
                    "indexed": false,
                    "internalType": "uint256",
                    "name": "amount1",
                    "type": "uint256"
                }
            ],
            "name": "Collect",
            "type": "event"
        },
    ]
    const web3 = new Web3(provider)
    const  uniswapV3NftManagerContract = new web3.eth.Contract(abi as AbiItem[], uniswapV3NftManagerAddress)


    const deadline = Math.floor(Date.now() / 1000) + 60 * 10; // 10 minutes from now
    const decreaseLiquidityParams = [
        60162,// tokenId The id of the erc721 token
        4999000099994999900,
        2499999999999999,
        0,
        deadline,
    ]
    const decreaseLiquidityData = uniswapV3NftManagerContract.methods['decreaseLiquidity'](
        decreaseLiquidityParams[0],
        decreaseLiquidityParams[1],
        decreaseLiquidityParams[2],
        decreaseLiquidityParams[3],
        decreaseLiquidityParams[4],
    ).encodeABI();


    const amount0Max = ethers.utils.parseUnits("100", 18);
    const amount1Max = ethers.utils.parseUnits("100", 18);
    const collectParams = [
        60162,// tokenId The id of the erc721 token
        EMPTY_ADDRESS,
        amount0Max,
        amount1Max,
    ]
    const collectData = uniswapV3NftManagerContract.methods['collect'](
        collectParams[0],
        collectParams[1],
        collectParams[2],
        collectParams[3],
    ).encodeABI();


    const name = 'YOUR_ROLE_NAME'
    const roleName = ethers.utils.formatBytes32String(name);

    const calls = [
        {
            roleName,
            to: uniswapV3NftManagerAddress,
            value: '0',
            data: decreaseLiquidityData,
            operation: Operation.Call,
        },
        {
            roleName,
            to: uniswapV3NftManagerAddress,
            value: '0',
            data: collectData,
            operation: Operation.Call,
        },
    ]

    const receipt = await execTransactionsFromModule(calls)
    console.log('receipt: ', receipt)
}
```

You can view the transaction on [https://goerli.etherscan.io/](https://goerli.etherscan.io/) using the `transactionHash`.
