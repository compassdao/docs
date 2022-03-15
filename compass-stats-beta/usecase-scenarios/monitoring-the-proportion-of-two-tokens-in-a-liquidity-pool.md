# Monitoring the proportion of two tokens in a liquidity pool

_The previous sample showed the most simplified example of the Script feature. It can be also utilized in more complicated scenarios. For example, monitoring the proportion of two tokens in a liquidity pool. Create a script the same as before and use the code below:_

```
export const name = 'Pool Token Proportion';
export const description = 'Pool Token Proportion';
export const tag = 'defi';
 
const erc20ABI = [
 "function balanceOf(address) view returns (uint)",
 "function decimals() view returns (uint8)"
]
 
const TEN = ethers.BigNumber.from(10);
 
const HEADERS = {
 'content-type': 'application/json'
};
 
export const run = async ({ token0, token1, pool, threshold, notifyUrl }: { token0: string, token1: string, pool: string, threshold: number, notifyUrl: string }) => {
 const provider = evm.getProvider('ethereum');
 const token0Contract = new ethers.Contract(token0, erc20ABI, provider);
 const token1Contract = new ethers.Contract(token1, erc20ABI, provider);
 const token0Decimals = await token0Contract.decimals();
 const token1Decimals = await token1Contract.decimals();
 const token0Balance = (await token0Contract.balanceOf(pool)).div(TEN.pow(token0Decimals));
 const token1Balance = (await token1Contract.balanceOf(pool)).div(TEN.pow(token1Decimals));
 
 const p = token0Balance.mul(100).div(token0Balance.add(token1Balance)).toNumber() / 100;
 if (p > threshold) {
   await fetch(notifyUrl, {
     method: "POST",
     body: JSON.stringify({message: "Pool token proportion > " + threshold}),
     headers: HEADERS
   })
 }
 return p;
};
```

``

_Once the Script is created, it can be saved and run as mentioned in step 3 in Sample A. Additionally, users can set up alerts once a specific threshold has been triggered. Open_ [_Trigger_](https://dev.compassdao.io/trigger)_, click the + button to add a Trigger. Users can name the trigger based on personal preference. Select the script you’ve previously created in the Script drop-down list, and fill in the parameters in the box below. For example, the parameters for “Alert when LUSD surpassed 50% in the Curve LUSD+3CRV” looks like this:_

```
{
   "token0": "0x5f98805A4E8be255a32880FDeC7F6728C6568bA0",
   "token1": "0x6c3F90f043a72FA612cbac8115EE7e52BDe6E490",
   "pool": "0xEd279fDD11cA84bEef15AF5D39BB4d4bEE23F0cA",
   "threshold": "0.5",
   "notifyUrl": "https://hooks.slack.com/workflows/sample/sample/sample/sample"
}
```

![](https://lh3.googleusercontent.com/Xp8ylNvLrxCZWzp9LwLJjRYfo-7cFf9tJflNi\_aOkq19oVZrcKQPvyT5epksYWaJdAR8qPO0RMdqUWZFfkU79vV8fU-Fz7DAq2GBj5f7zdWrYYX1AD8XuwXW7Pmv-EBXHNg6U1\_w)

_After the trigger is set up, users can check the history results as below. Currently, we check the stats once every minute._

![](https://lh3.googleusercontent.com/N8jbUVtZWDKCTtdaKuTmTSuRifeOhDHktzdem-omE12LNX4Md\_JZzpmBeBIzUdk2Q0zjgnJVzoa9x0jNkawP1f3bwqLZwCCpTeR32Sh8F2O77H0fZRgFXHb104FVdQdHYxLrqaBg)
