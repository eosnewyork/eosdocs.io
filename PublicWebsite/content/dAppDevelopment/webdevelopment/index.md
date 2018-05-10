---
title: "Web Development"
date: 2018-04-30T15:53:46-04:00
draft: false
weight: 7
---

### This section still under contraction

Currently the best way to interact with the EOS blockchain from a web browser is to use the [EOSJS](https://github.com/EOSIO/eosjs) Javascript library. The README also has a number of examples.

{{% notice warning %}}
Note that if you're a windows developer, the "npm run build_browser" command will fail. To get around this run "browserify -o dist/eos.js -s Eos lib/index.js" .. this will generate the eos.js file in the dist folder (You will need to have browserify installed of course - "npm install -g browserify"). 
{{% /notice %}}