## Customize AWS Config resource tracking in AWS Control Tower environment

This repository is intended to support AWS Control Tower AWS Config resources exclusion and mitigate excessive costs as a part of AWS Foundations product. It may be especially useful when running ephemeral workloads.
The original code is present [here](https://github.com/aws-samples/aws-control-tower-config-customization).
This github repository is part of [AWS blog post](https://aws.amazon.com/blogs/mt/customize-aws-config-resource-tracking-in-aws-control-tower-environment/).

## Overview 
[Source](https://aws.amazon.com/blogs/mt/customize-aws-config-resource-tracking-in-aws-control-tower-environment/)

This solution deploys into your AWS Control Tower management account’s home region. It utilizes each managed account’s `AWSControlTowerExecution` role to update the AWS Config recorder resource types in all AWS Control Tower governed regions. AWS Control Tower creates this role during the account enrollment process. The AWS Cloud Formation template pre-populates all of the supported AWS Config resource types as a parameter. This configuration lets you exclude resource types from the collection.

This solution leverages the lifecycle events that AWS Control Tower generates for certain management operations, such as updating the Landing Zone or managing an account.

1. An Amazon Event Bridge rule is created, which initiates the “Producer” AWS Lambda function on events like `UpdateLandingZone`, `CreateManagedAccount`, and `UpdateManagedAccount`.
2. The Producer Lambda function gets the list of all account IDs and regions where the AWS Config recorder has been deployed by querying the CloudFormation stack set created by AWS Control Tower.
3. The Producer Lambda function then sends the list of account IDs and regions to the Amazon SQS queue.
4. Every message in the Amazon SQS queue invokes the Consumer Lambda function.
5. Consumer Lambda function assumes the `AWSControlTowerExecution` role in the member account and updates the AWS Config recorder with the resource list specified as an input parameter while deploying the solution.

For the other two events, namely `CreateManagedAccount` and `UpdateManagedAccount`, the Producer Lambda function gets the list of regions where AWS Control Tower deploys AWS Config recorder using CloudFormation stack sets for the specific account ID. This avoids unnecessary resource usage and minimizes costs.

## Security

See [CONTRIBUTING](CONTRIBUTING.md#security-issue-notifications) for more information.

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

