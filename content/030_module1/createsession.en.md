---
title: "Create Session"
weight: 23
pre: "<b>3. </b>"
draft: false
---

Up until this point we have created traffic mirror target - mirrored traffic is send to target, traffic mirror filter - we created a filter with inbound rule to mirror port 80 traffic. Now we will create traffic mirror session. Traffic mirror session sends mirrored packets from the source to a target so that you can monitor and analyze traffic.


1. Launch Amazon VPC console in the region where you have created your [target](/030_module1/createtarget/) and [filter](/030_module1/createfilter/):

  * {{% button href="https://us-east-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ireland{{% /button %}}
  ![amazonVpcConsole](/images/amazonVpcConsole.png)

2. On the left navigation pane, scroll down and choose Traffic Mirroring, Mirror Session:
![selectFilter](/images/scrollDownSession.png)

3. Choose Create Traffic Mirror Target:
![CreateFilter](/images/createSession.png)

4. Enter value as show below and choose create traffic mirror session:
  * For mirror source, choose the network interface of the instance that you want to monitor.
      * For this activity we have already created an instance that will act as a source. Click on the link in recommended region:
          * {{% button href="https://us-east-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-server-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-server-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-server-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ireland{{% /button %}}

      * Click on **Network Interface    eth0** and it will display the ENI
      ![selectENISession](/images/selectENISession.png)

  * For mirror target, choose the traffic mirror target.
  * For filter, choose traffic mirror filter.
  * Refer to [documentation](https://docs.aws.amazon.com/vpc/latest/mirroring/traffic-mirroring-getting-started.html#step-create-traffic-mirroing-sessions) to understand meaning and importance of each field
  ![ClickCreateFilter](/images/clickCreateSession.png)

5. At this point you should have your filter successfully created:
![createTargetComplete](/images/createSessionComplete.png)
