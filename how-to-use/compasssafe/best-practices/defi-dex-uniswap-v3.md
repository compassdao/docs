# DeFi - Dex (Uniswap V3)

In Dex protocols like Uniswap, you can set a member with the following roles, so he/she can react ASAP once there's an emergency or operate the frequent rebalancing for LPs :&#x20;

**For Liquidity Providers:**

* **Collect Fees** : can collect fees from an existed position to the specified recipient
* **Decrease Liquidity (only ERC20)**: can decrease/remove liquidity from an existed ERC20 position (Normally it collects the fees at the same time so the **Collect Fees** permission is included)
* **Decrease Liquidity (ETH and ERC20)**: can decrease/remove liquidity from an existed  position and get native token(e.g.ETH) directly (**Collect Fees** permission is included)
* **Increase Liquidity (only ERC20)**: can add liquidity to an existed position with ERC20 token pairs
* **Increase Liquidity (ETH and ERC20)**: can add liquidity to an existed position with native token and ERC20 token pairs
* **Mint (only ERC20)** : can create a new position pool with ERC20 token pairs (**Increase Liquidity** permission is included)
* **Mint (ETH and ERC20)** : can create a new position pool with native token and ERC20 token pairs (**Increase Liquidity** permission is included)
* **CreateAndInitializePoolIfNecessary (NOT RECOMMENDED)**: can initiate an entire new position pool (as it's a high-risk operation, we don't recommend you grant the permission to a single member, if you do have the need, initiate the pool with multi-sig first, and the member can mint\&increase liquidity later)

**For Traders** (**NOT RECOMMENDED** as the trading activities are normally too flexible like the dynamic paths) :

* **Swap** : can swap among allowed pairs and paths

\-------------------------------------------------------------------------------------------

Here we take [Uniswap V3](https://app.uniswap.org/) as a sample to show you the details how you can set the roles:

**For Liquidity Providers:**

Enter the role name and target contract address.

`Polygon:` [`0xc36442b4a4522e871399cd717abdd847ab11fe88`](https://polygonscan.com/address/0xc36442b4a4522e871399cd717abdd847ab11fe88)`(Uniswap V3: Positions NFT) - all position related roles are set with it.`

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

1.  &#x20;**Collect Fees**

    * Select the roles and fill the parameters (Notice: Define your **Safe Address** as the recipient address so the member can only collect fees to your **Safe Address**, you can also define the recipient as a specified address like your fee rewarding account but notice that the Uniswap front-end regards it as the owner of liquidity position by default and regard it as 0x address when the fee amount is too small. If the amount is not so big, you may leave it as blank so you have more flexibility).

    <figure><img src="../../../.gitbook/assets/image (4) (2).png" alt=""><figcaption></figcaption></figure>
2.  **Decrease Liquidity (only ERC20)**&#x20;

    * Select the roles, fill the parameters if you need. (As the action is always together with **Collect Fees**, check the permission at the same time)

    <figure><img src="../../../.gitbook/assets/image (3) (1).png" alt=""><figcaption></figcaption></figure>
3.  **Decrease Liquidity (ETH and ERC20)**

    * If you're using both native token(e.g. ETH) and ERC20 tokens, besides the roles above for **Decrease Liquidity (only ERC20)** , you may need additional role: sweepToken(), unwrapWETH9(), fill the parameters especially the recipient as your **Safe Address** otherwise the member with this role is able to transfer your assets away by abusing the functions.



    <figure><img src="../../../.gitbook/assets/image (5) (1).png" alt=""><figcaption></figcaption></figure>
4.  **Increase Liquidity (only ERC20)**

    * Select the roles, fill the parameters if you need.

    <figure><img src="../../../.gitbook/assets/image (37).png" alt=""><figcaption></figcaption></figure>
5.  **Increase Liquidity (ETH and ERC20)**

    * If you're using both native token(e.g. ETH) and ERC20 tokens, besides the roles above for **Increase Liquidity (only ERC20)**, you may need additional role: refundETH()

    <figure><img src="../../../.gitbook/assets/image (7) (4).png" alt=""><figcaption></figcaption></figure>
6.  **Mint (only ERC20)**&#x20;

    * Select the roles and fill the parameters (Notice: limit the token0 and token1 address so the member can only initialize liquidity pool between the specified token pairs.  Define your **Safe Address** as the recipient address otherwise the member with this role is able to transfer your assets away by abusing the functions).



    <figure><img src="../../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>
7.  **Mint (ETH and ERC20)**

    * If you're using both native token(e.g. ETH) and ERC20 tokens, besides the roles above for **Mint (only ERC20)** , you may need additional role: refundETH()



    <figure><img src="../../../.gitbook/assets/image (7).png" alt=""><figcaption></figcaption></figure>
