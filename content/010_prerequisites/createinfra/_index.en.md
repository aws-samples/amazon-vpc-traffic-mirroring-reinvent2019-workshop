+++
title = "Create Infrastructure"
weight = 10
pre = "<b>1. </b>"
+++

### Create infrastructure required for the session:

{{% notice info %}}
Before you deploy the CloudFormation template, feel free to view it [here](https://github.com/pmankad96/amazon-vpc-traffic-mirroring-workshop/tree/master/templates/aws-vpc-tm-pub-single-az-all-builtin.yaml)
{{% /notice %}}

#### Launch infrastructure using CloudFormation template in the recommended region:
{{% tabs name="Region" %}}
{{{% tab name="Oregon" include="cfn-us-west-2.md" /%}}
{{% /tabs %}}

<!--
{{% tabs name="Region" %}}
{{{% tab name="Ohio" include="cfn-us-east-2.md" /%}}
{{{% tab name="Oregon" include="cfn-us-west-2.md" /%}}
{{{% tab name="Ireland" include="cfn-eu-west-1.md" /%}}
{{{% tab name="Singapore" include="cfn-ap-southeast-1.md" /%}}
{{% /tabs %}}
-->

Upon successful creation of CloudFormation stack, you will have following resources created:

1. VPC
2. IGW
3. Public route table
4. Public subnet
5. Three EC2 instances
  1. Acting as client
  2. Acting as server
  3. Acting as destination for mirrored traffic

<!--
Another way is to use button instead of tabs shortcode:
{{% button href="https://issues.amazon.com/issues/create?template=f084dc94-e920-4d98-80f7-252d5cc7ce00" icon="fas fa-envelope" %}}Launch in Singapore{{% /button %}}
-->
