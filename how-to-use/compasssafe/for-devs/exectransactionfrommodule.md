---
description: for single transaction
---

# execTransactionFromModule

### Dependency

In this example, we are using `TypeScript` as the programming language. Please install the following dependencies first:

* [web3.js](https://web3js.readthedocs.io/en/v1.7.4/)
* ethereumjs-tx

Let's simulate the "+New Position" function on Uniswap on the Goerli network, adding a new LP position for USDC-WETH pair.

We will use the `mint` method of the [NonfungiblePositionManager](https://goerli.etherscan.io/address/0xC36442b4a4522E871399CD717aBDD847Ab11FE88) contract. Please first set the [Role](../set-role.md) and [Member](../set-member.md).  (for the detailed method and parameters, you may refer to the Best Practice of [Uniswap](../best-practices/defi-dex-uniswap-v3.md))



### Example

We use [Infura](https://www.infura.io/zh) as the provider. You may choose the provider you like:

```
import Web3 from 'web3'

const moduleAddress = 'YOUR_COMPASS_SAFE_MODULE_ADDRESS'
const safeAddress = 'YOUR_GNOSIS_SAFE_ADDRESS'
const INFURA_KEY = 'YOUR_INFURA_PROJECT_ID'
const provider = new Web3.providers.HttpProvider(`https://goerli.infura.io/v3/${INFURA_KEY}`)
const senderAddress = 'YOUR_MEMBER_ADDRESS'
const privateKey = Buffer.from('YOUR_MEMBER_PRIVATE_KEY', 'hex')
```

Execute the `execTransactionsFromModule` method of the Compass Safe Module:

```
import { AbiItem } from 'web3-utils'
import { TransactionReceipt } from 'web3-core'
import { Transaction } from 'ethereumjs-tx'

enum Operation {
  None,
  Call,
  DelegateCall,
  Both,
}

const execTransactionFromModule = async (
  roleName: string,
  to: string,
  value: string,
  data: string,
  operation: Operation
): Promise<TransactionReceipt> => {
  const web3 = new Web3(provider)
  const abi = [{
    "inputs": [
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
    "name": "execTransactionFromModule",
    "outputs": [],
    "stateMutability": "nonpayable",
    "type": "function"
  },]
  const contract = new web3.eth.Contract(abi as AbiItem[], moduleAddress)
  const encodedData = contract.methods['execTransactionFromModule'](
    roleName,
    to,
    value,
    data,
    operation,
  ).encodeABI()
  const nonce = await web3.eth.getTransactionCount(senderAddress)
  const rawTx = {
    nonce,
    to: moduleAddress,
    data: encodedData,
    gasPrice: 2162230808,
    gasLimit: 256312,
    value: 0,
  }
  const tx = new Transaction(rawTx, { chain: 'goerli' })
  tx.sign(privateKey)
  const serializedTx = tx.serialize()
  return (
    web3.eth.sendSignedTransaction('0x' + serializedTx.toString('hex'))
      .once('sending', function (payload) { console.log('sending transaction') })
      .once('sent', function (payload) { console.log('sent transaction') })
      .once('transactionHash', function (hash) { console.log('transactionHash: ', hash) })
  )
}
```

Use `execTransactionFromModule` to call `mint`

```
const mint = async () => {
    const WETH = '0xB4FBF271143F4FBf7B91A5ded31805e42b2208d6'
    const UNI = '0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984'
    const uniswapV3NftManagerAddress = '0xc36442b4a4522e871399cd717abdd847ab11fe88'

    const abi = [{
        "inputs": [
            {
                "components": [
                    {
                        "internalType": "address",
                        "name": "token0",
                        "type": "address"
                    },
                    {
                        "internalType": "address",
                        "name": "token1",
                        "type": "address"
                    },
                    {
                        "internalType": "uint24",
                        "name": "fee",
                        "type": "uint24"
                    },
                    {
                        "internalType": "int24",
                        "name": "tickLower",
                        "type": "int24"
                    },
                    {
                        "internalType": "int24",
                        "name": "tickUpper",
                        "type": "int24"
                    },
                    {
                        "internalType": "uint256",
                        "name": "amount0Desired",
                        "type": "uint256"
                    },
                    {
                        "internalType": "uint256",
                        "name": "amount1Desired",
                        "type": "uint256"
                    },
                    {
                        "internalType": "uint256",
                        "name": "amount0Min",
                        "type": "uint256"
                    },
                    {
                        "internalType": "uint256",
                        "name": "amount1Min",
                        "type": "uint256"
                    },
                    {
                        "internalType": "address",
                        "name": "recipient",
                        "type": "address"
                    },
                    {
                        "internalType": "uint256",
                        "name": "deadline",
                        "type": "uint256"
                    }
                ],
                "internalType": "struct INonfungiblePositionManager.MintParams",
                "name": "params",
                "type": "tuple"
            }
        ],
        "name": "mint",
        "outputs": [
            {
                "internalType": "uint256",
                "name": "tokenId",
                "type": "uint256"
            },
            {
                "internalType": "uint128",
                "name": "liquidity",
                "type": "uint128"
            },
            {
                "internalType": "uint256",
                "name": "amount0",
                "type": "uint256"
            },
            {
                "internalType": "uint256",
                "name": "amount1",
                "type": "uint256"
            }
        ],
        "stateMutability": "payable",
        "type": "function"
    },]
    const web3 = new Web3(provider)
    const uniswapV3NftManagerContract = new web3.eth.Contract(abi as AbiItem[], uniswapV3NftManagerAddress)


    const deadline = Math.floor(Date.now() / 1000) + 60 * 10; // 10 minutes from now

    const params = {
        token0: UNI,
        token1: WETH,
        fee: 10000,
        tickLower: -16000,
        tickUpper: 16000,
        amount0Desired: 10000000000000000,
        amount1Desired: 1,
        amount0Min: 10000000000000000,
        amount1Min: 0,
        recipient: safeAddress,
        deadline,
    }

    const mintData = uniswapV3NftManagerContract.methods['mint'](params).encodeABI()

    const name = 'ROLE_NAME'
    const roleName = Buffer.from(name, "hex").toString("utf8").replaceAll("\x00", "");

    const receipt = await execTransactionFromModule(roleName, uniswapV3NftManagerAddress, '0', mintData, Operation.Call)
    console.log('receipt: ', receipt)
}
```



You can view the transaction on [https://goerli.etherscan.io/](https://goerli.etherscan.io/) using the `transactionHash`.
