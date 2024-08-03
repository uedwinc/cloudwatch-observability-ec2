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

