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
# create new permission on an account
# cleos set account permission <account> <new permission> <pub key> <parent>
cleos set account permission eosnewyorkio claim EOS6MRyAjQq8ud7hVNYcfnVPJqcVpscN5So8BhtHuGYqET5GDW5CV active --permission eosnewyorkio@active

# set which actions are allowed by the newly created permission
cleos set action permission eosnewyorkio eosio claimrewards claim --permission eosnewyorkio@active

# claim rewards
cleos system claimrewards eosnewyorkio --permission eosnewyorkio@claim
```