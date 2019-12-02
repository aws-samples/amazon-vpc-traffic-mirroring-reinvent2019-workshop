+++
title = "Network Load Balancer as a target"
chapter = true
weight = 30
pre = "<b>6. </b>"
draft = false
+++

# Objective:
You might want to use single target for multiple sources. In doing so target can become resource constrained. In this activity we explore how you can use scale target by front ending them with a network load balancer (NLB) and use NLB as a target instead. At the end of this activity you will learn how to successfully configure NLB as a target


1. Launch Amazon VPC console in the region where you have created your [target](/030_module1/createtarget/) and [filter](/030_module1/createfilter/):

  * {{% button href="https://us-west-2.console.aws.amazon.com/ec2/v2/home?region=us-west-2#LoadBalancers:sort=loadBalancerName" icon="fas fa-external-link-alt" %}}Amazon ELB console in Oregon{{% /button %}}

  ![awsElbConsole](/images/awsElbConsole.png)

2. Click on Create Load Balancer:
![clickCreateNlb](/images/clickCreateNlb.png)

3. Choose UDP as the Load Balancer Port and VXLAN port: 4789 as the Load Balancer Port:
![loadBalancerPort](/images/loadBalancerPort.png)
![lbProtoPort](/images/lbProtoPort.png)

4. Select appropriate VPC - the that CloudFormation created for: mkt210-vpc
![selectVpc](/images/selectVpc.png)

5. Configure Target Group:
![configureTg](/images/configureTg.png)

6. Select vpctm-ids-ec2 as the target to register:
![selectIds.png](/images/selectIds.png)

7. Click create and you will have an NLB created:
![nlbComplete](/images/nlbComplete.png)
