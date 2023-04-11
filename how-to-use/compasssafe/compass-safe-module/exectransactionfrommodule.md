# execTransactionFromModule

## Dependency

本次示例中使用的编程语言为 `typescript`，请先安装如下依赖

* [web3.js](https://web3js.readthedocs.io/en/v1.7.4/)
* ethereumjs-tx

本次示例中我们将模拟 Rinkeby 上 [Uniswap](https://app.uniswap.org/#/swap?chain=rinkeby) 的 Swap 功能，Swap [UNI](https://rinkeby.etherscan.io/address/0x1f9840a85d5aF5bf1D1762F925BDADdC4201F984) -> [WETH](https://rinkeby.etherscan.io/address/0xc778417E063141139Fce010982780140Aa0cD5Ab)

我们将用到 [SwapRouter](https://rinkeby.etherscan.io/address/0xE592427A0AEce92De3Edee1F18E0157C05861564#code) 合约的 `exactOutputSingle` 方法，请先根据 [Set Role](../set-role.md) 和 [Set Member](../set-member.md) 来绑定权限

## Example

我们使用了 Infura 作为 provider，请自行在 [Infura](https://infura.io/) 上申请 PROJECT\_ID

请按照真实情况补充如下变量

```typescript
import Web3 from 'web3'

const moduleAddress = 'YOUR_COBO_SAFE_MODULE_ADDRESS'
const safeAddress = 'YOUR_GNOSIS_SAFE_ADDRESS'
const INFURA_KEY = 'YOUR_INFURA_PROJECT_ID'
const provider = new Web3.providers.HttpProvider(`https://rinkeby.infura.io/v3/${INFURA_KEY}`)
const senderAddress = 'YOUR_MEMBER_ADDRESS'
const privateKey = Buffer.from('YOUR_MEMBER_PRIVATE_KEY', 'hex')

```

根据 [web3.js 文档](https://web3js.readthedocs.io/en/v1.7.4/web3-eth.html?#id93)执行 **Compass Safe Module** 的 `execTransaction` 方法的代码片段

```typescript
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
  const tx = new Transaction(rawTx, { chain: 'rinkeby' })
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

使用 `execTransactionFromModule`调用 `exactOutputSingle`

```typescript
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

调用 `swapUniToWeth` 方法即可成功 Swap UNI -> WETH

可以通过`transactionHash`在 [https://rinkeby.etherscan.io/](https://rinkeby.etherscan.io/) 上查看交易详情
