---
title: "Create Target"
weight: 20
pre: "<b>1. </b>"
draft: false
---

Mirrored traffic is sent to destination. In this activity we will configure [Amazon ENI](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/using-eni.html) attached to an [Amazon EC2 instance](https://docs.aws.amazon.com/AWSEC2/latest/UserGuide/concepts.html) as a target.

1. Launch Amazon VPC console in the region where you have [created infrastructure](/010_prerequisites/createinfra):

  * {{% button href="https://us-east-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ireland{{% /button %}}
  ![amazonVpcConsole](/images/amazonVpcConsole.png)

2. On the left navigation pane, scroll down and choose Traffic Mirroring, Mirror Targets:
![selectTarget](/images/scrollDownTarget.png)

3. Choose Create Traffic Mirror Target:
![CreateTarget](/images/createTarget.png)

4. Enter details as shown below and choose create. Verify Target Type is set to Network Interface:
![verifyTargetType](/images/verifyTargetType.png)

  * When you click on Target, it will show list of available Amazon ENIs:
  ![selectENI](/images/listENI.png)

  * select ENI associated with Amazon EC2 instance that you want to use as target:
  ![enterENI](/images/enterENI.png)

  * For this activity we have already created an instance that will act as a target. Click on the link in recommended region:

      * {{% button href="https://us-east-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/ec2/home?region=eu-west-1#Instances:search=vpctm-ids-ec2;instanceState=running,stopped;sort=instanceId"icon="fas fa-external-link-alt" %}}Ireland{{% /button %}}

  * Click on **Network Interface    eth0** and it will display the ENI
  ![selectENI](/images/selectENI.png)

5. At this point you should have your target successfully created:
![createTargetComplete](/images/createTargetComplete.png)
