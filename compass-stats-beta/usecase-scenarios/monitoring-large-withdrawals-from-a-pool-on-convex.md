# Monitoring large withdrawals from a pool on Convex

_Please refer to the Script below. In this case, we will be using Convex as an example. Users can monitor other contracts as needed._&#x20;

```
export const name = 'Convex Large Amount Transfer Out';
export const description = 'placeholder';
export const tag = 'Defi';
 
const convexPoolAbi = [
 "event Withdrawn (address indexed user, uint256 indexed poolid, uint256 amount)"
]
 
const E18 = ethers.BigNumber.from('1000000000000000000');
 
const HEADERS = {
 'content-type': 'application/json'
};
 
export const run = async ({ poolId, poolName, threshold, notifyUrl }: { poolId: number, poolName: string, threshold: number, notifyUrl: string }) => {
 const provider = evm.getProvider('ethereum');
 const contract = new ethers.Contract("0xF403C135812408BFbE8713b5A23a04b3D48AAE31", convexPoolAbi, provider);
 const filterTransfer = contract.filters.Withdrawn(null, poolId);
 const currentBlockNumber = await provider.getBlockNumber();
 const cacheKey = "convexLargeTransferOut_" + poolId
 let prevBlockNumber = await cache[cacheKey]
 if (!prevBlockNumber) {
   prevBlockNumber = currentBlockNumber - 1000;
 }
 const events = await contract.queryFilter(filterTransfer, prevBlockNumber, currentBlockNumber);
 cache[cacheKey] = currentBlockNumber + 1
 const largeEvents = [];
 for (const evt of events) {
   const amt = evt.args.amount.div(E18).toNumber();
   if (amt >= threshold) {
     largeEvents.push({
       user: evt.args.user,
       amount: amt,
       blockNumber: evt.blockNumber,
       tx: evt.transactionHash,
       poolId: poolId
     })
     await fetch(notifyUrl, {
       method: "POST",
       body: JSON.stringify({message: "Pool " + poolName + " Large Tranfer Out, Amount: " + amt + ", tx: " + evt.transactionHash}),
       headers: HEADERS
     })
   }
 }
 return largeEvents;
};
```

_Here're the settings for the Trigger, taking Frax as an example._

```
{
   "poolId": 32,
   "poolName": "Frax",
   "threshold": 2000000,
   "notifyUrl": "https://hooks.slack.com/workflows/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"
 
}
```

