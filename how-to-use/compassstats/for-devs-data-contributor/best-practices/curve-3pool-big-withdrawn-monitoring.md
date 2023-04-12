# Curve 3pool Big Withdrawn Monitoring

Here we take Curve 3pool as an example to monitor the proportion of  USDT:

![](<../../../../.gitbook/assets/image (12) (1).png>)

_Check the script here:_ [_https://compassdao.com/scripts/0xf9c4e606cb0c497c_](https://compassdao.com/scripts/0xf9c4e606cb0c497c)

The script code is as following:

_Here we use \[_[_Cache_](../../devs-documentation.md)_] to store last checkpoint of block number._

```
export const name = 'Curve 3Pool Big Withdraw';
export const description = 'Big transfer';
export const tag = 'curve';

const { utils, BigNumber } = ethers;
const POOLS = [
  {
    address: '0xdac17f958d2ee523a2206206994597c13d831ec7',
    symbol: 'USDT',
    decimals: 6,
  },
  {
    address: '0x6b175474e89094c44da98b954eedeac495271d0f',
    symbol: 'DAI',
    decimals: 18,
  },
  {
    address: '0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48',
    symbol: 'USDC',
    decimals: 6,
  },
];

export const run = async () => {
  const logs = await sdk.onLogs('ethereum', {
    topics: [
      '0xddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef',
      utils.hexZeroPad('0xbebc44782c7db0a1a60cb6fe97d0b483032ff1c7', 32),
    ],
  }, {version: "001"});
  const normalized = logs
    .map((log) => {
      const pool = POOLS.find((i) => i.address === log.address.toLowerCase());
      if (!pool) {
        return null;
      }

      const { symbol, decimals } = pool;
      const {
        data,
        topics: [, , paddedToAddress],
        transactionHash,
        blockNumber
      } = log;
      const toAddress = '0x' + paddedToAddress.slice(-40);
      const amount = Number(utils.formatUnits(BigNumber.from(data), decimals));

      return {
        symbol,
        blockNumber,
        transactionHash,
        toAddress,
        amount,
      };
    })
    .filter((i) => !!i);

  return normalized;
};
```

Once the Script is created, it can be saved and run, the result is as below (array):

```
[
  {
    "symbol": "USDT",
    "blockNumber": 15178570,
    "transactionHash": "0x5a9af11bb5f35cf31db318fa185827f682c2db7c76abc67d208b27309033d491",
    "toAddress": "0x4a14347083b80e5216ca31350a2d21702ac3650d",
    "amount": 14.189023
  },
  {
    "symbol": "USDT",
    "blockNumber": 15178579,
    "transactionHash": "0xd7dbb2c7688660545f793daaa72a0ea5ccc855a5fef535ce1bfbfcf8aa52f3ae",
    "toAddress": "0x0eb747bf142ce0faabbf7716681b0f6bb12a0a28",
    "amount": 2242352.016762
  },
  {
    "symbol": "USDC",
    "blockNumber": 15178584,
    "transactionHash": "0x16fe1c1f45143c20e6404deefe365f980081731fca4693dc0d6fa6687c1ec93b",
    "toAddress": "0x7cf67a1a486d5716517a989f180112ba26d1afcf",
    "amount": 202.082687
  }
]
```

Now you can [schedule a task](../../for-all-users/schedule-tasks.md) for the script:

![](<../../../../.gitbook/assets/image (11) (1).png>)

Then [set the alert](../../for-all-users/set-alerts.md) to notify my Twitter bot:

_So the alert will notify me when the amount is above 1,500,000, and some variants from the result are filled in the notify message. The condition will be checked every 1 minute._

> 👀 A big withdraw {amount} {symbol} from @CurveFinance 3pool: https://etherscan.io/tx/{transactionHash} Customize your own monitoring here: https://compassdao.com/scripts/0xf9c4e606cb0c497c. Powered by @compassDAO

![](<../../../../.gitbook/assets/image (18) (1).png>)

Now you get your perfect Twitter bot, by assembling the message with Etherscan prefix, you can even link your auto Tweet with the Etherscan link:

![https://twitter.com/compassBots/](<../../../../.gitbook/assets/image (17).png>)
