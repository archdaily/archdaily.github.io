---
layout:     post
title:      "The Deployment Ecosystem"
date:       2014-02-06 15:54:18
categories: deploy 
tags:       jenkins capistrano minitest
---


A few years ago, we started coding ruby/rails applications using **vagrant** + **chef** in our computers, runinig tests with **minitest/rspec/etc** and then deploying to one or two **AWS EC2** instances using **capistrano**. A few months later, we discovered [**jenkins**][jenkins], so we changed a little bit our deployment workflow: now we pushed to master at **GitHub**; **jenkins** was notified thanks to **GitHub Hooks**, and if all tests passed, the code was deployed to our **AWS EC2** instances, with **capistrano**, in the **jenkins** server.


Everything was great until the day we realized we needed to scale up automatically, with an *AWS Auto Scaling Group*. We set up our *ASG* and everything looked good, but... no! When the *ASG* scaled up, our **jenkins** server didn't get any notification of the new *EC2 instance* that needed to be deployed. This's why I'm writing this posts, because maybe you can run into the same problem.


There is two possible scenarios:

1. We need to deploy to all the current instances.
2. The ASG scales up.

 

Scenario 1: We need to deploy to all the current instances

We wrote a [little (ruby) library][aws-deploy-helper] that connects to **AWS API** and get the information of all the current *AWS EC2 instances*, creating two files:

1. ssh config: A ssh configuration files to connect to all the instances
2. cap config: A little yml which capistrano will read later

Let's asume that we have a project called *my_project*, so we setup the library this way:

```
aws:
  access_key_id: 'YOUR_ACCESS_KEY_ID'                 # aws user with read permissions to ec2
  secret_access_key: 'YOUR_SECRET_KEY'
path:
  ssh_config_file: '/var/lib/jenkins/.ssh/config'     # the file where you will save the ssh keys
  cap_config_file: '/var/lib/jenkins/cap_config.yml'  # the file where you will save the capistrano configs
sites:
  my_project:
    elb: 'myproject.elb.amazonaws.com'                 # the hostname of the project's elb
    region: 'us-east-1'                                # the region of the ASG
    keys: '~/.ssh/keys/myproject.pem'                   # the key's path 
```

[jenkins]:            http://jenkins-ci.org/
[aws-deploy-helper]:  https://github.com/archdaily/aws-deploy-helper