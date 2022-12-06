# DeFi - Dex (Uniswap V3)

In Dex protocols like Uniswap, you can set a member with the following roles, so he/she can react ASAP once there's an emergency or do the frequent rebalancing for LPs :&#x20;

**For LPs:**

* **Mint (ERC20)** : can create a new position pool with ERC20 token
* **Mint (ETH)** : can create a new position pool with native token
* **Increase Liquidity** : can add liquidity to an existed position
* **Remove Liquidity** : can remove liquidity from an existed position
* **Collect fees** : can collect fees from an existed position

**For traders** (not recommended as the trading activities are normally too flexible like the dynamic paths) :

* **Swap** : can swap among allowed paths

Here we take [Uniswap V3](https://app.uniswap.org/) as a samlpe to show you how you can set the roles:

1. **Mint (ERC20)**&#x20;
   *   Enter the role name and target contract address (for native tokens like ETH/Matic, the gateway contract is used as the target contract).

       `Polygon:` [`0xAeBF56223F044a73A513FAD7E148A9075227eD9b`](https://polygonscan.com/address/0xAeBF56223F044a73A513FAD7E148A9075227eD9b)(WrappedTokenGatewayV2)
