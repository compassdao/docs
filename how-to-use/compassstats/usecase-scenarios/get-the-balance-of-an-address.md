# Get the balance of an address

_The default Sample Script is a simple script to get the balance of an address, users can edit the script and run or save it._&#x20;

![](<../../../.gitbook/assets/image (5).png>)

_the script code is as following:_

```
export const name = "My Awesome Script";
export const description = "Get balance of address";
export const tag = "eth balance";

const formatBalance = (wei: ethers.BigNumber) =>
  Number(Number(ethers.utils.formatEther(wei)).toFixed(4));

export const run = async ({ address }: Record<string, string>) => {
  const cacheKey = 'balanceOf' + String(address);
  const cachedBalance = cache[cacheKey] || 0;
  const prevBalance = ethers.BigNumber.from(cachedBalance);

  const provider = evm.getProvider("ethereum");
  const curBalance = await provider.getBalance(address);
  cache[cacheKey] = curBalance.toString();

  return {
    address,
    prevBalance: formatBalance(prevBalance),
    curBalance: formatBalance(curBalance),
  };
};
```

__

_Once the script is saved, users can fill in the parameters to run the script._&#x20;
