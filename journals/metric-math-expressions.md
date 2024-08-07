# Metric math expressions

Metric math functionality enables you to query many CloudWatch metrics and use mathematical expressions to derive meaningful insights from them and create new metrics. The following are some intriguing use cases that can be achieved by utilizing metric math functions:

## 1. Capacity calculation from metrics

You can calculate capacity metrics such as `transactions per second (TPS)` and bytes written to disk per second (BPS).

Let’s say that you would like to understand the `amount of traffic flowing on the load balancer per second` and understand any performance bottlenecks. You can leverage the `PERIOD()` function to achieve that functionality.

[Previously](/journals/cloudwatch-metrics-explorer.md), we deployed an application load balancer using a CloudFormation template. We will see how to calculate the overall bytes processed by the Application Load Balancer using the metric math expression:

1. Let’s navigate to `CloudWatch` | `Metrics` | `All metrics` | `ApplicationELB` | `per AppELB Metrics`. Check the `ProcessedBytes` metric. Now, navigate to the `Graphed metrics` tab and change `Statistic` to `Sum`.

![bytes-processed](/imgs/bytes-processed.png)

2. Click on `Add math` | `All functions`, and select `PERIOD`

3. Rename the label from `Expression1` to `ProcessedBytesPerInterval`.

![interval](/imgs/interval.png)

Now, you can observe the traffic flowing on the load balancer per interval, as selected in the `Period` column.

4. You can quickly add the widget to a dashboard by clicking on `Actions` | `Add to dashboard`

5. You can add to a new dashboard or an existing dashboard by clicking `Create new` or browsing the existing dashboards.

![app-dash](/imgs/app-dash.png)

Given the metric `m1`, we can use the `PERIOD()` function to divide the values of the metric by the period in the `Period` column, which will provide the value per interval for the selected metric.

This `PERIOD()` function is useful for calculating capacity-related metrics such as `TPS on Elastic Block Store (EBS)` volumes, as well as for measuring `I/O`(bytes written to disk per second, etc).

