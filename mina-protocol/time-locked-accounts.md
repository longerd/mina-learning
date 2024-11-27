# Time-Locked Accounts 

时间锁定账户会禁止那些会使账户余额减少至低于某个最小值的支付操作, 该最小值取决于区块高度. 

要创建时间锁定账户, 你必须在创建新账户时提供相应的配置. 这只能在网络初始阶段的创世账本中进行操作. 在本节中, 我们将深入探讨时间锁定背后的机制, 并了解如何与时间锁定账户进行交互. 


> 在当前版本中, 时间锁定账户的值是根据你注册的先后顺序来分配的. 

## Understanding time-locks

时间锁定包含以下几个字段: `initial_minimum_balance`, `cliff`, `vesting_period`以及`vesting_increment`. 

如果账户设有时间锁定, 只要账户持有足够资金, 你仍然可以使用该账户. 在网络刚开始时, 被时间锁定的资金数量起始为`initial_minimum_balance`. 一旦网络达到的区块高度等于`cliff`对应的区块高度, 被时间锁定的金额就会开始按照每经过一个`vesting_period`减少`vesting_increment`的金额. 

如需关于此过程更具技术性的解释, 请查看[RFC-0025](https://github.com/MinaProtocol/mina/blob/master/rfcs/0025-time-locked-accounts.md), 其中有更深入的概述. 

### Liquid Balance Details:

如果你想在特定时间段内公开归属账户的流动余额, 可通过以下函数来控制(注意: 此函数用于计算账户的锁定部分): 

```
(*
 *  uint32 global_slot -- the "clock" it starts at 0 at the genesis block and ticks up every 3minutes.
 *  uint32 cliff_time -- the slot where the cliff is (similar to startup equity vesting)
 *  uint32 cliff_amount -- the amount that unlocks at the cliff
 *  amount vesting_increment -- unlock this amount every "period"
 *  uint32 vesting_period -- the period that we increment the unlocked amount
 *  balance initial_minimum_balance -- the total locked amount until the cliff
 *)
let min_balance_at_slot ~global_slot ~cliff_time ~cliff_amount ~vesting_period
    ~vesting_increment ~initial_minimum_balance =
  let open Unsigned in
  if Global_slot.(global_slot < cliff_time) then initial_minimum_balance
  else
    match Balance.(initial_minimum_balance - cliff_amount) with
    | None ->
        Balance.zero
    | Some min_balance_past_cliff -> (
        (* take advantage of fact that global slots are uint32's *)
        let num_periods =
          UInt32.(
            Infix.((global_slot - cliff_time) / vesting_period)
            |> to_int64 |> UInt64.of_int64)
        in
        let vesting_decrement =
          UInt64.Infix.(num_periods * Amount.to_uint64 vesting_increment)
          |> Amount.of_uint64
        in
        match Balance.(min_balance_past_cliff - vesting_decrement) with
        | None ->
            Balance.zero
        | Some amt ->
            amt )

```

## Creating a time-locked account 

截至当前版本, 创建时间锁定账户的唯一方式是通过创世账本进行操作. 在未来的版本中, 我们可能会向`mina client`以及 GraphQL API 中添加相关命令, 以便你能够创建新的时间锁定账户.  