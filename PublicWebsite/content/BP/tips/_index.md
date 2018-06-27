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

