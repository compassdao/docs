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
const swapUniToWeth = async () => {
  const WETH = '0xc778417E063141139Fce010982780140Aa0cD5Ab'
  const UNI = '0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984'
  const swapRouterAddress = '0xE592427A0AEce92De3Edee1F18E0157C05861564'
  const swapRouterAbi = [{
    inputs: [{
      components: [{ internalType: 'address', name: 'tokenIn', type: 'address' }, { internalType: 'address', name: 'tokenOut', type: 'address' }, { internalType: 'uint24', name: 'fee', type: 'uint24' }, { internalType: 'address', name: 'recipient', type: 'address' }, { internalType: 'uint256', name: 'deadline', type: 'uint256' }, { internalType: 'uint256', name: 'amountOut', type: 'uint256' }, { internalType: 'uint256', name: 'amountInMaximum', type: 'uint256' }, { internalType: 'uint160', name: 'sqrtPriceLimitX96', type: 'uint160' }],
      internalType: 'struct ISwapRouter.ExactOutputSingleParams',
      name: 'params',
      type: 'tuple'
    }],
    name: 'exactOutputSingle',
    outputs: [{ internalType: 'uint256', name: 'amountIn', type: 'uint256' }],
    stateMutability: 'payable',
    type: 'function'
  }, {
    inputs: [{ internalType: 'uint256', name: 'amountMinimum', type: 'uint256' }, { internalType: 'address', name: 'recipient', type: 'address' }],
    name: 'unwrapWETH9',
    outputs: [],
    stateMutability: 'payable',
    type: 'function'
  }]
  const web3 = new Web3(provider)
  const swapRouterContract = new web3.eth.Contract(swapRouterAbi as AbiItem[], swapRouterAddress)
  const params = {
    tokenIn: UNI,
    tokenOut: WETH,
    fee: 10000,
    recipient: safeAddress,
    deadline: 1656573006,
    amountOut: 100000000000000,
    amountInMaximum: 119575749648988,
    sqrtPriceLimitX96: 0,
  }
  const exactInputSingleData = swapRouterContract.methods.exactOutputSingle(params).encodeABI()

  const name = 'ROLE_NAME'
  const roleName = Buffer.from(name, "hex").toString("utf8").replaceAll("\x00", "");

  const receipt = await execTransactionFromModule(roleName, swapRouterAddress, '0', exactInputSingleData, Operation.Call)
  console.log('receipt: ', receipt)
}
```



You can view the transaction on [https://goerli.etherscan.io/](https://goerli.etherscan.io/) using the `transactionHash`.
