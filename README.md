# Automated Deployment of AWS CloudHSM Resources

This AWS CloudFormation template automatically deploys a set of [AWS CloudHSM|https://docs.aws.amazon.com/cloudhsm/latest/userguide/introduction.html] resources and supporting AWS resources to be able to use AWS CloudHSM in support of [AWS KMS custom key stores|https://docs.aws.amazon.com/kms/latest/developerguide/custom-key-store-overview.html].

## Overview

Creation of a CloudFormation stack using this template automatically creates:
* A CloudHSM cluster
* 2 HSMs to the cluster
* An EC2 client configured to manage the cluster and HSMs
* An initial crypto officer password for the cluster is stored in AWS Secrets Manager
* A CloudHSM trust anchor certificate
* A set of AWS Lambda Functions and AWS Step Functions state machines used to orchestrate management of CloudHSM resources
* A KMS custom key store backed by the cluster
* A `kmsuser` crypto user password

Deletion of the the stack results in the removal of these resources.

To do: Include a diagram showing the resources that are created by the template.

### AWS Lambda functions and AWS Step Functions state machines

Since management of CloudHSM resources is not yet available through resources built into CloudFormation, a series of AWS Lambda functions are deployed as part of the CloudFormation template to carry out the work of managing CloudHSM resources.

Since the processes required to create and delete clusters and HSMs may take longer than the maximum Lambda function execution time of 15 minutes, a set of AWS Step Functions state machines are created by the CloudFormation template to orchestrate management of CloudHSM resources. 

## Assumptions

...

## Usage


## CloudFormation Template Parameters

|Parameter|Required|Description|Default|
|---------|--------|-----------|-------|
|`VPCInput`|Optional|The VPC in which the HSM ENIs will be provisioned and in which the EC2 client will be deployed.|None|
|`ClientInstanceSubnet`|Optional|The subnet in which the EC2 client will be deployed.|None|
|`ImageId`|Optional|EC2 image ID to use for the EC2 client.|`/aws/service/ami-amazon-linux-latest/amzn2-ami-hvm-x86_64-ebs`|
|`InstanceType`|Optional|Instance type to use for the EC2 client|`t2.medium`|

## Troubleshooting deployment

...

## Changing the crypto officer password

...

## Managing the EC2 client

...

## Back up and restore

...

## Questions

* What happens when someone deletes the EC2 client? What needs to be done to replace it?
* Is the automation making both HSMs active? One?

## Enhancements

### Possibly near term

* Publish the trust anchor certificate to customer-specific S3 bucket
* Parameterize the HSM instance type
* Paramterize the subnets/AZs in which to deploy the HSMs
* Make the number of HSMs to deploy a parameter. Useful development and testing use cases.
* Via parameters, enable customers to drive the naming of the created AWS resources.
* Make the creation of KMS custom key store an option so that the template supports uses cases in which KMS is not required.
* Parameterize the CloudFormation wait timeout (currently set to 2 hours)
* Parameterize the extent of activating HSMs. e.g. one vs all.

### Future
* Support changing the number of HSMs after the initial deployment by updating a parameter for the number of HSMs and updating the stack.