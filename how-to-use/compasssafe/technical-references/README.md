# Technical References

## Preparation

在开始使用 **Compass Safe Module** 之前，需要根据 [ Set up Safe Module 流程](../set-up-safe-module.md)创建 **Compass Safe Module** 并将 **Compass Safe Module** 绑定在 **Gnosis Safe** 上，并[Set Role](../set-role.md)、[Set Member](../set-member.md)



## 与 Defi Protocol 交互

准备工作完成！现在可以由绑定了 `Role` 的 `Member` 来进行 Defi Protocol 的交互啦！

1. 获取 Defi Protocol 中要执行的合约方法数据 data
2. 将 data 作为参数，调用 **Compass Safe Module** 的 `execTransactionFromModule` 方法或 `execTransactionsFromModule`方法
3. **Compass Safe Module** 会自动检查
   1. 当前用户是否为 `Member`
   2. 当前用户是否有 `Role`
   3. `Role`是否绑定过当前调用的方法
4. 如果检查通过，那么调用 **Gnosis Safe** 的 `execTransactionFromModule`方法，否则拒绝交易
5. **Gnosis Safe** 会自动判断当前 **Compass Safe Module** 是否在 **Gnosis Safe** 上注册过，如果注册过，那么执行 1 中的方法，否则拒绝交易

以上与 Defi Protocol 进行交互，可以通过[产品流程](../interact-with-dapps.md)进行操作，产品流程中，会自动执行 `execTransactionFromModule` 或 `execTransactionsFromModule`

也可以通过写脚本的方式执行 [`execTransactionFromModule`](https://app.gitbook.com/o/aXy1OBly1YcUyYGVUAS4/s/dAVpwa3QK5LVkObWs51w/\~/changes/3/how-to-use/compasssafe/compass-safe-module/exectransactionfrommodule) 和 [`execTransactionsFromModule`](https://app.gitbook.com/o/aXy1OBly1YcUyYGVUAS4/s/dAVpwa3QK5LVkObWs51w/\~/changes/3/how-to-use/compasssafe/compass-safe-module/exectransactionsfrommodule)
