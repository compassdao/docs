# Monitoring large withdrawals from a pool on Convex

_Please refer to the Script below. In this case, we will be using Convex as an example. Users can monitor other contracts as needed._&#x20;

`export const name = 'Convex Large Amount Transfer Out';`&#x20;

`export const description = 'placeholder';`&#x20;

`export const tag = 'Defi';`

`const convexPoolAbi = [`&#x20;

&#x20;`"event Withdrawn (address indexed user, uint256 indexed poolid, uint256 amount)"`&#x20;

`]`

`const E18 = ethers.BigNumber.from('1000000000000000000');`

`const HEADERS = {`&#x20;

&#x20;`'content-type': 'application/json'`&#x20;

`};`

`export const run = async ({ poolId, poolName, threshold, notifyUrl }: { poolId: number, poolName: string, threshold: number, notifyUrl: string }) =>`&#x20;

`{`&#x20;

`const provider = evm.getProvider('ethereum');`&#x20;

`const contract = new`&#x20;

`ethers.Contract("0xF403C135812408BFbE8713b5A23a04b3D48AAE31", convexPoolAbi, provider);`&#x20;

`const filterTransfer = contract.filters.Withdrawn(null, poolId);`&#x20;

`const currentBlockNumber = await provider.getBlockNumber();`&#x20;

`const cacheKey = "convexLargeTransferOut_" + poolId`&#x20;

`let prevBlockNumber = await cache[cacheKey]`&#x20;

`if (!prevBlockNumber) {`&#x20;

&#x20;   `prevBlockNumber = currentBlockNumber - 1000;`&#x20;

`}`&#x20;

`const events = await contract.queryFilter(filterTransfer, prevBlockNumber, currentBlockNumber);`&#x20;

`cache[cacheKey] = currentBlockNumber + 1`&#x20;

`const largeEvents = [];`&#x20;

`for (const evt of events) {`&#x20;

&#x20;    `const amt = evt.args.amount.div(E18).toNumber();`&#x20;

&#x20;    `if (amt >= threshold) {`&#x20;

&#x20;     `largeEvents.push({`&#x20;

&#x20;        `user: evt.args.user,`&#x20;

&#x20;        `amount: amt,`&#x20;

&#x20;        `blockNumber: evt.blockNumber,`&#x20;

&#x20;        `tx: evt.transactionHash,`&#x20;

&#x20;        `poolId: poolId`&#x20;

&#x20;      `})`&#x20;

&#x20;        `await fetch(notifyUrl, {`&#x20;

&#x20;             `method: "POST",`&#x20;

&#x20;             `body: JSON.stringify({message: "Pool " + poolName + " Large Tranfer Out, Amount: " + amt + ", tx: " + evt.transactionHash}),`&#x20;

&#x20;             `headers: HEADERS`

&#x20;       `})`

&#x20;     `}`&#x20;

&#x20;   `}`&#x20;

&#x20;   `return largeEvents;`

`};`

``

_Here're the settings for the Trigger, taking Frax as an example._

`{`&#x20;

&#x20; __  `"poolId": 32,`&#x20;

&#x20;`"poolName": "Frax",`&#x20;

&#x20;`"threshold": 2000000,`&#x20;

&#x20;`"notifyUrl": "https://hooks.slack.com/workflows/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"`

`}`

