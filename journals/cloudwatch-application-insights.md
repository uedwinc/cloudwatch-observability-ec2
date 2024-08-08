# CloudWatch Application Insights

CloudWatch Application Insights streamlines the monitoring of enterprise applications with its intuitive and automated setup process. This reduces the time and effort required to configure monitoring, as it automatically sets up metrics, telemetry, logs, and alarms. The key advantage of using Application Insights is that it automatically discovers and configures application-specific monitoring, making it easy to effectively monitor your applications.

It provides a dashboard for detected problems and provides insights and observations. It has predefined, customizable rules for alerting. It also helps you create an `AWS System Manager (SSM)` OpsItem to take remediation action with SSM runbooks.

CloudWatch Application Insights supports gathering metrics, logs, and application-specific metrics using the console. Currently, X-Ray tracing is not supported.

You can use two different methods to discover the application using Application Insights:

- Resource group based: You can create a new resource group for an application using AWS resource group and tags as a component of your CloudFormation template. As part of the exercise in this section, we will use this method to create a new resource group.

- Account based: By implementing the account-based method, you can create application monitoring for all the resources in the AWS account. This is ideal when you are looking to instrument all future applications deployed in your AWS account.

## Application Setup and Configuration

We will deploy a Java application on an EC2 instance and set up observability for the application using Application Insights and get additional insights related to the application besides the path of the user journey provided by X-Ray.

1. Create a [CloudFormation template](/basic-ec2-template_rg_no-monitoring.yml) to deploy and setup sample application

2. Go to CloudFormation on the console and click on `Create stack`. Choose to upload a template file and select the template file created above. 

3. Enter the stack name as `scorekeepappinsight` and update the email field as required. Leave other parameters as populated from the template default parameter values. Click on the `Next` button on all the subsequent pages. On the last one, named `Review scorekeepappinsight`, do not forget to check the box with the text `The following resource(s) require capabilities: [AWS::IAM::Role]` and click on the `Create Stack` button.

4. Wait until the Stack switches to the CREATE_COMPLETE state. After a short time (around 10 to 15 minutes to be more precise), your account will have the following resources deployed:

- An EC2 instance
- A Lambda function
- A PostgreSQL database
- Five DynamoDB tables
- An SNS topic
- Security groups 
- A resource group
- Roles and permissions, modified to allow management using AWS Systems Manager (as Application Insights leverages AWS SSM to install and configure the CloudWatch agent)

![resources](/imgs/application-composer-2024-08-08T145512.650Zh56-basic-ec2-template_rg_nomonitoring.yml.png)

> Note that the EC2 instance does not have CloudWatch agent installed or configured on it.

## Exploring CloudWatch Application Insights

Once it’s successfully deployed, let’s discover the application using CloudWatch Application Insights:

