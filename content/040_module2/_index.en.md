+++
title = "Use open source tool"
chapter = true
weight = 20
pre = "<b>4. </b>"
draft = false
+++

# Objective:

You can use open-source tools to monitor network traffic from Amazon EC2 instances. In this activity we will briefly go over how to use Suricata. For more information see the [Suricata website](https://suricata-ids.org/).

* We will build on top of previous activity: [Setup VPC Traffic Mirroring](/030_module1/)
* Suricata is already installed on the destination instance
* We will configure http rule on Suricata.
* From client we will initiate port 80 (http) traffic and look at the logs to see http rule being triggered.
