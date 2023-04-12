# DeFi - Lending (Aave)

In lending protocols like Aave, you can set a member with the following roles, so he/she can react ASAP once there's an emergency:

* User with '**repay**' roles can repay debts immediately when the health rate is reaching the liquidation line.
* User with '**withdraw**' roles can withdraw deposits immediately when there's a danger signal(hacker attack, rug, extreme utilization) of the pool.&#x20;

Here we take [Aave](https://app.aave.com/) as a samlpe to show you how you can set the roles:

1.  _**Repay ETH & Withdraw ETH**_&#x20;

    *   Enter the role name and target contract address (for native tokens like ETH/Matic, the gateway contract is used as the target contract).

        `Polygon:` [`0xAeBF56223F044a73A513FAD7E148A9075227eD9b`](https://polygonscan.com/address/0xAeBF56223F044a73A513FAD7E148A9075227eD9b)(WrappedTokenGatewayV2)

    <figure><img src="../../../.gitbook/assets/image (1) (4).png" alt=""><figcaption></figcaption></figure>

    * Select the roles and fill the parameters (Notice: the 2 parameters marked below must be the **Safe Address** otherwise the member with this role is able to transfer your assets away by abusing the functions).

    <figure><img src="../../../.gitbook/assets/image (9).png" alt=""><figcaption></figcaption></figure>
2. _**Repay ERC20 & Withdraw ERC20**_
   *   Enter the role name and target contract address.

       `Polygon:` [0x8dFf5E27EA6b7AC08EbFdf9eB090F32ee9a30fcf (](https://polygonscan.com/address/0x8dFf5E27EA6b7AC08EbFdf9eB090F32ee9a30fcf)Aave: Lending Pool V2)



       <figure><img src="../../../.gitbook/assets/image (36).png" alt=""><figcaption></figcaption></figure>


   * Select the roles and fill the parameters (Notice: the 2 parameters marked below must be the **Safe Address** otherwise the member with this role is able to transfer your assets away by abusing the functions).

<figure><img src="../../../.gitbook/assets/image (8).png" alt=""><figcaption></figcaption></figure>
