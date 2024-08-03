# CloudWatch metrics explorer

CloudWatch metrics explorer is a flexible tag-based tool that filters, aggregates, and visualizes metrics by resource tags. With tag-based monitoring, you create a dynamic application infrastructure health dashboard that automatically updates as you deploy new resources, using the resulting visualizations to spot patterns, correlate the results, and reduce the time taken for resolution.

## Using the CloudWatch metrics explorer to build a dynamic dashboard for an Auto Scaling web server group

1. Build an EC2 Auto Scaling web cluster using AWS CloudFormation

- Create a [CloudFormation template](/autoscaling-alb-cfn-template.json) to setup an Auto Scaling EC2 web server with two instances supporting an application behind the load balancer.

- Deploy template to AWS CloudFormation:

```sh
aws cloudformation deploy \
  --stack-name EC2AutoscalingDeploy \
  --region us-east-2 \
  --template-file "autoscaling-alb-cfn-template.json" \
  --parameter-overrides KeyName='enterkeyname' OperatorEMail='enteremail' Subnets='subneta,subnetb,etc' VpcId='entervpcid' \
  --no-execute-changeset \
  --capabilities CAPABILITY_NAMED_IAM
```

> `--no-execute-changeset` will require you to log into the console and execute the changeset manually on CloudFormation

![ec2autoscalingdeploy-stack](/imgs/ec2autoscalingdeploy-stack.png)

To effectively plan for capacity, let’s say we need to understand the combined utilization of these two instances. Using the CloudWatch metrics explorer, we will merge metrics from both instances to gain a comprehensive understanding of their usage.

2. Navigate to `AWS Console` | `CloudWatch` | `Metrics` | `Explorer`. Metrics explorer provides two different templates – namely, `generic templates` and `service-based templates`, or you can select an `empty explorer`.

3. For this exercise, select `Service based templates` and select `EC2`, under the `Explorer` dropdown, and it should select all the metrics related to EC2

4. In `From`, select `aws:autoscaling:groupName` and select the created Auto Scaling cluster

5. Look at the `Explorer` page and you will see that EC2 metrics are displayed as line graphs, featuring data from both EC2 instances.

![explorer-view](/imgs/explorer-view.png)

6. Now, navigate to `Aggregate by` and select `Sum` and `All resources`.

This allows the consolidated utilization of data from both EC2 instances and presents a unified view

![aggregate-view](/imgs/aggregate-view.png)

7. The view can be further sliced using `Split by` when you want to understand the utilization by availability zone to understand where most of your load is being distributed. This can be helpful and handy if you are troubleshooting an uneven load distribution across `availability zones (AZs)`.

8. You can also add this view to the CloudWatch dashboard by clicking on the `Add to Dashboard` button at the top. As the new instances are being built in the Auto Scaling cluster, the new EC2 instances will be added dynamically to the CloudWatch dashboard as we have filtered/grouped them using the Auto Scaling tag (`aws:autoscaling:groupName`).

> Also, you can achieve equal functionality by leveraging the Explorer widget on the CloudWatch dashboard.

Tag-based monitoring and CloudWatch metrics explorer can be leveraged further in the following scenarios:

- Multi-application environments: Filter performance by a specific application in a multi- application environment when you are sharing your AWS account
- Multi-AZ deployments: Compare performance trends across availability zones (you can use the `Split by` option)
- Multi-Region deployments: Compare performance trends across regions
- Multi-account deployments: Compare performance trends or combine metrics running in multiple accounts across multiple regions
- Multiple sub-components: Filter performance by a specific sub-component in a complex application (leveraging the Split by option)
- Monitoring deployments: You can compare performance by instance type and other resource properties that help to isolate issues and correlate with operational events such as upgrades and patching