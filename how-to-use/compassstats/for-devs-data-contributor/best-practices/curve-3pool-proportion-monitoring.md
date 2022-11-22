# Curve 3pool Proportion Monitoring

The previous sample showed the most simplified example of the Script feature. It can be also utilized in more complicated scenarios. For example, monitoring the proportion of tokens in a liquidity pool.&#x20;

Here we take Curve 3pool as an example to monitor the proportion of  USDT:

![](<../../../../.gitbook/assets/image (14) (1).png>)

_Check the script here:_ [_https://compassdao.com/scripts/0x7361a257b248491a_](https://compassdao.com/scripts/0x7361a257b248491a)__

The script code is as following:

_Here we use \[_[_Cache_](../../devs-documentation.md)_] to store last result, and define a delta variant to monitor the change of the ratio._

```
export const name = '3pool USDT';
export const description = 'curve 3pool monitor';
export const tag = 'curve 3pool';

const { Contract, utils, BigNumber } = ethers;
const ERC20 = ['function balanceOf(address) view returns (uint)'];

const CURVE_LIQUIDITY_FARMING_POOL =
  '0xbEbc44782C7dB0a1A60Cb6fe97d0b483032FF1C7';
  
const formatNumber = (num: number, decimals:number = 4 ) => Number(num.toFixed(decimals));

const formatBN = (wei: BigNumber, unit:number = 18) => Number(utils.formatUnits(wei, unit));

export const run = async () => {
  const provider: Web3Provider = evm.getProvider('ethereum');

  const USDT = new Contract('0xdAC17F958D2ee523a2206206994597C13D831ec7', ERC20, provider);
  const USDC = new Contract('0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48', ERC20, provider);
  const DAI = new Contract('0x6B175474E89094C44Da98b954EedeAC495271d0F', ERC20, provider);

  const USDTAmount = formatBN(await USDT.balanceOf(CURVE_LIQUIDITY_FARMING_POOL), 6);
  const USDCAmount = formatBN(await USDC.balanceOf(CURVE_LIQUIDITY_FARMING_POOL), 6)
  const DAIAmount = formatBN(await DAI.balanceOf(CURVE_LIQUIDITY_FARMING_POOL), 18)

  const totalAmount = USDTAmount + USDCAmount + DAIAmount;
  const usdtRatio = formatNumber(USDTAmount / totalAmount * 100, 2);

  const key = '3pool-usdt';
  let lastUSDTRatio = await cache[key] || 0;
  const ratioDeltaAbs=Math.abs(usdtRatio-lastUSDTRatio);
  if(ratioDeltaAbs>=2){
    cache[key] = usdtRatio;
  }

  return {
    USDTAmount,
    USDCAmount,
    DAIAmount,
    usdtRatio,
    ratioDeltaAbs
  };
};
```

_Once the Script is created, it can be saved and run, the result is as below:_

```
{
  "USDTAmount": 247110557.436453,
  "USDCAmount": 357517694.26593,
  "DAIAmount": 349215555.09886163,
  "usdtRatio": 25.91,
  "ratioDeltaAbs": 7.7499999999999964
}
```

Now you can [schedule a task](../../for-all-users/schedule-tasks.md) for the script:

![](<../../../../.gitbook/assets/image (1) (2).png>)

Then [set the alert](../../for-all-users/set-alerts.md) to notify my Twitter bot:

_So the alert will notify me when the change is above 2%, and some variants from the result are filled in the notify message. The condition will be checked every 1 minute._

> ⚠️ Warning: $USDT ratio of @CurveFinance 3pool is now {usdtRatio}%, $USDC balance is {USDCAmount}, $DAI balance is {DAIAmount}, for more info, see: https://compassdao.com/scripts/0x7361a257b248491a. Powered by @compassDAO

![](<../../../../.gitbook/assets/image (15) (1).png>)

Now you get your perfect Twitter bot:

![https://twitter.com/compassBots/](<../../../../.gitbook/assets/image (3) (1).png>)



A similar case you can use to monitor the Curve stETH pool (just simply change the token addresses in script): [https://compassdao.com/scripts/0x326fb04cd0b74269](https://compassdao.com/scripts/0x326fb04cd0b74269)
