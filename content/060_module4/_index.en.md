+++
title = "Automate your VPC Traffic Mirroring Setup"
chapter = true
weight = 35
pre = "<b>7. </b>"
draft = false
+++

* The current traffic mirroring offering provides support to mirror network traffic only from elastic network interfaces attached to an EC2 instance. This serverless application allows the user to automate setting up of traffic mirroring based on VPCs, subnets, and tags as input. This application is based on AWS SAM framework and uses CloudFormation to set up the infrastructure. The application currently supports three use-cases:

  * Setting up traffic mirroring on existing EC2 instances
  * Setting up traffic mirroring on newly launched EC2 instances
  * Setting up traffic mirroring on EC2 instances which trigger a GuardDuty event

* Refer to [VPC TrafficMirroring Source Automation Application](https://github.com/aws-samples/aws-vpc-traffic-mirroring-source-automation) for more details.
