---
title: "BP Tips"
date: 2018-06-11T09:07:37-04:00
pre: "<b>2. </b>"
draft: false
weight: 0
---

### Setting Up a Claim Rewards Permission

{{% notice warning %}}
DO NOT USE THE PUBLIC KEY GIVEN IT IS THE TUTORIAL KEY AND THE PRIVATE KEY IS WELL KNOWN
{{% /notice %}}


```
---------- Step 1. Create custom permission on an account ----------
#cleos set account permission YOURACCOUNT claim CLAIM_PUBLIC_KEY active -p YOURACCOUNT@active
cleos set account permission eosnewyorkio claim EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV active -p eosnewyorkio@active

---------- Step 2. Link action to permission ----------
#cleos set action permission YOURACCOUNT eosio claimrewards claim -p YOURACCOUNT@active
cleos set action permission eosnewyorkio eosio claimrewards claim -p eosnewyorkio@active

---------- Claiming the rewards ----------
#cleos system claimrewards YOURACCOUNT -p YOURACCOUNT@claim
cleos system claimrewards eosnewyorkio --permission eosnewyorkio@claim

---------- Removing the permission ----------
# You must remove the action first before removing the permission

#cleos set action permission YOURACCOUNT eosio claimrewards NULL -p YOURACCOUNT@active
cleos set action permission eosnewyorkio eosio claimrewards NULL -p eosnewyorkio@active

#cleos set account permission YOURACCOUNT claim NULL active -p YOURACCOUNT@active
cleos set account permission eosnewyorkio claim NULL active -p eosnewyorkio@active
```

### Validating System Contract Update

Special thanks to [elmato](https://gist.github.com/elmato/8423b697b94dc0e816a10e6a33dfd9f2)

#### Prerequisites
1. The user is familiar with building EOS and has built the version of the software containing the contract being upgraded
2. `jq` is installed 
```
sudo apt install jq
``` 

#### Check sha256 Results
{{% notice warning %}}
Different OS versions will produce different sha256 results
{{% /notice %}}

1. Check local built version
```
sha256sum build/contracts/eosio.system/eosio.system.wasm
```

2. Check proposed contract 
```
# cleos multisig review <proposer> <proposal_name> | jq -r '.transaction.actions[0].data.code' | xxd -r -p | sha256sum
cleos multisig review eoscandacom onezeroseven | jq -r '.transaction.actions[0].data.code' | xxd -r -p | sha256sum
```

3. Confirm steps 1 and 2 results match

#### `diff` of system contracts
```
# diff <previous eos version>/contracts/eosio.system> <current eos version>/contracts/eosio.system
diff ~/git/mainnet-1.0.6/eos/contracts/eosio.system/ ~/git/mainnet/eos/contracts/eosio.system/

diff /home/deck/git/mainnet-1.0.6/eos/contracts/eosio.system/delegate_bandwidth.cpp /home/deck/git/mainnet/eos/contracts/eosio.system/delegate_bandwidth.cpp
157,159c157,160
<     *  While buying ram uses the current market price according to the bancor-algorithm, selling ram only
<     *  refunds the purchase price to the account. In this way there is no profit to be made through buying
<     *  and selling ram.
---
>     *  The system contract now buys and sells RAM allocations at prevailing market prices.
>     *  This may result in traders buying RAM today in anticipation of potential shortages
>     *  tomorrow. Overall this will result in the market balancing the supply and demand
>     *  for RAM over time.
176a178,179
>       eosio_assert( tokens_out.amount > 1, "token amount received from selling ram is too low" );
>
190c193,196
<       auto fee = tokens_out.amount / 200;
---
>
>       auto fee = ( tokens_out.amount + 199 ) / 200; /// .5% fee (round up)
>       // since tokens_out.amount was asserted to be at least 2 earlier, fee.amount < tokens_out.amount
>
209a216,218
>       eosio_assert( std::abs( (stake_net_delta + stake_cpu_delta).amount )
>                      >= std::max( std::abs( stake_net_delta.amount ), std::abs( stake_cpu_delta.amount ) ),
>                     "net and cpu deltas cannot be opposite signs" );
276,277c285,294
<          if ( req != refunds_tbl.end() ) { //need to update refund
<             refunds_tbl.modify( req, 0, [&]( refund_request& r ) {
---
>
>
>          // net and cpu are same sign by assertions in delegatebw and undelegatebw
>          // redundant assertion also at start of changebw to protect against misuse of changebw
>          bool is_undelegating = (net_balance.amount + cpu_balance.amount ) < 0;
>          bool is_delegating_to_self = (!transfer && from == receiver);
>
>          if( is_delegating_to_self || is_undelegating ) {
>             if ( req != refunds_tbl.end() ) { //need to update refund
>                refunds_tbl.modify( req, 0, [&]( refund_request& r ) {
# truncated output
```


#### Build abi_from_hex

1. TODO

#### `diff` of eosio.system ABI

1. Extract current abi
```
cleos get code -a /tmp/current.abi && cat /tmp/tmpabi | jq '' > /tmp/current.eosio.system.json
```

2. Extract proposed ABI
```
# cleos multisig review <proposer> <proposal_name> | jq -r '.transaction.actions[1].data.abi | abi_from_hex | jq '' > /tmp/eosio-1.0.7-abi.json
cleos multisig review eoscanadacom onezeroseven | jq -r '.transaction.actions[1].data.abi | abi_from_hex | jq '' > /tmp/eosio-1.0.7-abi.json
```

3. `diff` two abi
```
diff /tmp/current.eosio.system.json /tmp/eosio-1.0.7-abi.json
```
