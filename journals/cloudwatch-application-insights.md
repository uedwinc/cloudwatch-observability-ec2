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

1. Navigate to `Application Insights` | `Add an application` | `Resource group based application` | `Confirm`.

2. From the dropdown, select `my_resource_group` and check the boxes for `Automatic monitoring of new resources`, `Monitor EventBridge events`, and `Integrate with AWS Systems Manager OpsCenter` and click `Next`.

3. CloudWatch Application Insights discovers all the resources that are deployed as a part of the CloudFormation template, including the Java application components installed on EC2. You can select and edit any of the components. Click `Next`.

![review-components](/imgs/review-components.png)

4. In `Specify component details`, you can also add additional logs to be gathered from the Java application leveraging the CloudWatch agent. Add the log file `/tmp/scorekeep.log`. We can also change it at a later stage to accommodate additional run operations if necessary.

![component-details](/imgs/component-details.png)

5. Select `Next` and `Submit` to start the onboarding of the application.

6. You can verify that the resource group has been added successfully using Application Insights.

![resource-group](/imgs/resource-group.png)

You can see that it is additionally asking to set up X-Ray, which is already done as a part of the CloudFormation template and application instrumentation.

7. Once you navigate to Application Insights, you will see monitored assets, telemetry, and a summary along with the detected problems as a unified dashboard.

![insights-dash-1](/imgs/insights-dash-1.png)

![insights-dash-2](/imgs/insights-dash-2.png)

Application Insights uses CloudFormation to deploy the required resources to monitor EC2 instances, as shown in the following figure. This will also address the challenge of agent installation in the newly built instances as a part of the resource group when you are leveraging Auto Scaling mechanisms as discovery is a continuous process.

![extra-stacks](/imgs/extra-stacks.png)

8. When you navigate to any problem summary generated by Application Insights, it will provide you with an insights dashboard for the issue and also request feedback. 

In the following screenshot, I have navigated to the DynamoDB issue, where the user errors indicate client-side errors and suggests all the possible issues that could be resolved.

![dynamo-1](/imgs/dynamo-1.png)

![dynamo-2](/imgs/dynamo-2.png)

9. As we also created an OpsItem in AWS Systems Manager, it has provided a summary of the related resources and provided navigation of CloudTrail (audit events), CloudFormation (resources), and also CloudWatch alarms in a single view.

Click on the SSM OpsItem link on the problem summary page

![opsitem](/imgs/opsitem.png)

Application Insights provides a way to setup collecting infrastucutre and application related metrices and logs and setup alarms and anomaly detection automatically along with installation of CloudWatch agent in few clicks compared to instrumenting the application manually. 

We can also implement Application Insights using CloudFormation by using the resource type `AWS::ApplicationInsights:Application`: https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-applicationinsights-application.html.