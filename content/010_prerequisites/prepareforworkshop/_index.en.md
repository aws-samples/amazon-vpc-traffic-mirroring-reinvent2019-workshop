---
title: "Prepare of the Session"
weight: 15
pre: "<b>2. </b>"
draft: false
---

{{% notice info %}}
This step is required if you are going to configure Amazon VPC Traffic Mirroring using [AWS command line interface (AWS CLI)](https://aws.amazon.com/cli/)
{{% /notice %}}

* Access the **vpctm-client-ec2** instance created through CloudFormation template and run following command to configure AWS CLI:

  1. Install JQ:
  ```
  sudo yum -y install jq
  ```

  2. We should configure our AWS CLI with our current region as default:
  ```
  export AWS_ACCOUNT_ID=$(aws sts get-caller-identity --output text --query Account)
  export AWS_REGION=$(curl -s 169.254.169.254/latest/dynamic/instance-identity/document | jq -r '.region')

  echo "export ACCOUNT_ID=${ACCOUNT_ID}" >> ~/.bash_profile
  echo "export AWS_REGION=${AWS_REGION}" >> ~/.bash_profile
  aws configure set default.region ${AWS_REGION}
  aws configure get default.region
  ```

  3. Validate the IAM role. Use the [GetCallerIdentity](https://docs.aws.amazon.com/cli/latest/reference/sts/get-caller-identity.html) CLI command to validate that the **vpctm-client-ec2** instance is using the correct IAM role.

  ```
  aws sts get-caller-identity
  ```
