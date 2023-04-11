# For Devs

In some scenarios, you can set the specific member as a server (hot private key) to execute automated strategies.&#x20;

First, please make sure you have properly configured the corresponding [member](../set-member.md) and [role](../set-role.md) in the safe module. (Review carefully with the permissions to avoid granting excessive authority to the server's hot wallet!!!)&#x20;

The member (program) can interact with the DeFi protocol through the following steps:

1. Prepare the contract method 'data' to be executed with the DeFi Protocol.
2. Use 'data' as a parameter, call the Compass Safe Module's `execTransactionFromModule` method or the `execTransactionsFromModule` method.
3. The Compass Safe Module will automatically check:
   * Whether the current user is a Member
   * Whether the current user has the Role
   * Whether the Role has the access to currently called contract, method and parameters
4. If the check passes, call the Gnosis Safe's `execTransactionFromModule` method; otherwise, reject the transaction.
5. Gnosis Safe will automatically check whether the current Compass Safe Module is enabled on Gnosis Safe. If registered, execute the method in step 1; otherwise, reject the transaction.

The following two examples demonstrate how to use the `execTransactionFromModule` and `execTransactionsFromModule` methods:
