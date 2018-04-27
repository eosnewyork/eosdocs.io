---
title: "Getting the software"
date: 2018-04-24T09:21:04-04:00
weight: 2
draft: false
---

There are a number of methods available to getting the software. 

* [Amazon AWS instances](amazonaws) - Use a Amazon Machine Image (AMI) provide by EOSDocs.io - if you're familar with how to use AWS, this is by far the fastest way to get stared. 

* [Docker](docker) - Use the EOS provided docker image to compile. This is an excellent option if you're familiar with docker. It note that both nodeos and keosd are both servers and bind to port 8888 by default. Even if you're using docker we still suggest running nodeos on a different machien while following these tutorials.    

* [Compile](compile) - Directly compile the source code. This is the most complex method. 


{{% notice warning %}}
As mentioned in [The big picture](../thebigpicture) we suggest running nodeos on a seperate machine. It is possible to run all software on a single machine, but seperating the software makes learning easier. 
{{% /notice %}}