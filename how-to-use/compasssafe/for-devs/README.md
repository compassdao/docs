# For Devs

In some scenarios, you can set the specific member as a server (hot private key) to execute automated strategies. Here we're showing how to call the compass Safe Module for developers.

First, please make sure you have properly configured the corresponding [member](../set-member.md) and [role](../set-role.md) in the safe module.&#x20;

{% hint style="info" %}
Review carefully with the permissions to avoid granting excessive authority to the server's hot wallet!
{% endhint %}

Then the member (program) can interact with the DeFi protocol through the following steps:

1. Get the data of the original transaction to be executed.
2. Use 'data' as a parameter, call the Compass Safe Module's `execTransactionFromModule`  (single tx) method or the `execTransactionsFromModule` (batch tx) method.&#x20;
3. The Compass Safe Module will check whether the current user is a Member,  whether the current user has the Role, and whether the Role has the access to currently called contract, method and parameters.
4. If the check passes, the Safe module will call the Safe's `execTransactionFromModule` method. Otherwise, the transaction will be rejected.
5. Safe will check whether the current Compass Safe Module is enabled with the current Safe. If so, the transaction will be executed. Otherwise, the transaction will be rejected.

The following two examples shows how to use the `execTransactionFromModule` and `execTransactionsFromModule` methods:
