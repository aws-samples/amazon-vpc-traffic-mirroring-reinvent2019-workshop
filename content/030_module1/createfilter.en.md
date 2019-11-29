---
title: "Create Filter"
weight: 21
pre: "<b>2. </b>"
draft: false
---

A traffic mirror filter contains one or more traffic mirror rules, and a set of network services. The filters and rules that you add define the traffic that is mirrored. In this activity we will create traffic mirror filter:

1. Launch Amazon VPC console in the region where you have created your [target](/030_module1/createtarget/):

  * {{% button href="https://us-east-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ohio{{% /button %}} {{% button href="https://us-west-2.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Oregon{{% /button %}} {{% button href="https://eu-west-1.console.aws.amazon.com/vpc/" icon="fas fa-external-link-alt" %}}Amazon VPC console in Ireland{{% /button %}}
  ![amazonVpcConsole](/images/amazonVpcConsole.png)

2. On the left navigation pane, scroll down and choose Traffic Mirroring, Mirror Filters:
![selectFilter](/images/scrollDownFilter.png)

3. Choose Create Traffic Mirror Target:
![CreateFilter](/images/createFilter.png)

4. Enter value as show below and choose create traffic mirror filter:
![ClickCreateFilter](/images/clickCreateFilter.png)
  * We are going to mirror port 80 traffic ingressing on the server(source), hence we have created inbound rule for port 80. If you want to mirror traffic egressing from the server (source) outbound traffic, you need to create outbound rule as well.

5. At this point you should have your filter successfully created:
![createTargetCompleteClose](/images/createFilterCompleteClose.png)
![createTargetComplete](/images/createFilterComplete.png)
