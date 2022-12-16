# DeFi - Dex (Uniswap V3)

In Dex protocols like Uniswap, you can set a member with the following roles, so he/she can react ASAP once there's an emergency or operate the frequent rebalancing for LPs :&#x20;

**For Liquidity Providers:**

* **Collect Fees** : can collect fees from an existed position to the specified recipient
* **Decrease Liquidity (only ERC20)**: can decrease/remove liquidity from an existed ERC20 position (Normally it collects the fees at the same time so you also need the **Collect Fees** permission)
* **Decrease Liquidity (ETH and ERC20)**: can decrease/remove liquidity from an existed  position and get native token(e.g.ETH) directly
* **Increase Liquidity (only ERC20)**: can add liquidity to an existed position with ERC20 token pairs
* **Increase Liquidity (ETH and ERC20)**: can add liquidity to an existed position with native token and ERC20 token pairs
* **Mint (only ERC20)** : can create a new position pool with ERC20 token pairs
* **Mint (ETH and ERC20)** : can create a new position pool with native token and ERC20 token pairs

**For Traders** (**NOT RECOMMENDED** as the trading activities are normally too flexible like the dynamic paths) :

* **Swap** : can swap among allowed pairs and paths

\-------------------------------------------------------------------------------------------

Here we take [Uniswap V3](https://app.uniswap.org/) as a samlpe to show you the details how you can set the roles:

**For Liquidity Providers:**

Enter the role name and target contract address.

`Polygon:` [`0xc36442b4a4522e871399cd717abdd847ab11fe88`](https://polygonscan.com/address/0xc36442b4a4522e871399cd717abdd847ab11fe88)`(Uniswap V3: Positions NFT) - all position related roles are set with it.`

<figure><img src="../../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

1.  &#x20;**Collect Fees**

    * Select the roles and fill the parameters (Notice: Define your **Safe Address** as the recipient address otherwise the member with this role is able to transfer your assets away by abusing the functions. You can also define the recipient as a specified address like your fee rewarding account but notice that the Uniswap front-end regards it as the owner of liquidity position by default).

    <figure><img src="../../../.gitbook/assets/image (4).png" alt=""><figcaption></figcaption></figure>
2.  **Decrease Liquidity (only ERC20)**&#x20;

    * Select the roles, fill the parameters if you need. (As the action is always together with **Collect Fees**, check the permission at the same time)

    <figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>
3. **Decrease Liquidity (ETH and ERC20)**
4.  **Increase Liquidity (only ERC20)**

    * Select the roles, fill the parameters if you need.

    <figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>
5. **Increase Liquidity (ETH and ERC20)**
6.  **Mint (only ERC20)**&#x20;

    * Select the roles and fill the parameters (Notice: define the token0 and token1 address so the member can only initialize liquidity pool between the specified token pairs.  Define your **Safe Address** as the recipient address otherwise the member with this role is able to transfer your assets away by abusing the functions).

    <figure><img src="../../../.gitbook/assets/image (27).png" alt=""><figcaption></figcaption></figure>
7. **Mint (ETH and ERC20)**
