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

## 2. Horizontal and vertical annotations

The `horizontal annotation` feature provides a quick visual representation of metrics crossing the predefined values, such as SLA limits, offering valuable context for understanding metric values. You can add lines to represent important key values on the y axis. For example, defining maximum and minimum values expected for metrics. This will be especially useful in application operations by incorporating tacit knowledge into dashboards.

If you are visualizing the time series as a part of the x axis, `vertical annotations` will be useful to mark events that happened over time and bring tacit knowledge to dashboards. 

Let’s add annotations to the widget created earlier under "Capacity calculation for metrics".

1. Navigate to `Options` tab and click on `Add horizontal annotation`. Enter `Min Limit` for label and set value at `1000`. Click `Add horizontal annotation` again, enter `Max Limit` for label and set value at `10000`.

2. Also, add a vertical annotation called `MajorChange`

![annotations](/imgs/annotations.png)

3. Then, update the widget.

![ann-dash](/imgs/ann-dash.png)

By using vertical annotations in this example, the operations team can quickly reference historical data points and determine any changes in the application. 

By using horizontal annotation, we mapped the expected maximum and minimum threshold. This brings tacit operational knowledge onto the dashboard.

## 3. Filling in missing values or generating high cardinality metrics

When a metric is not generated often because of no traffic (for example, website traffic) and you would like to fill in the missing values, you can use the `FILL()` function to generate the missing values. See current graph below:

![missing-values](/imgs/missing-values.png)

You can see that the **ProcessedBytes** metric is missing values at certain intervals because of no traffic to the website. If you would like to fill in the missing values of the time series using mathematical functions to estimate similar traffic patterns, you can use the `FILL` command with different parameters:

- `FILL(metric, S)` – S is the static value to fill. You can see the static value is 1 in the following screenshot. Click on `Add math` | `All functions` | `FILL`. You can edit and add FILL(m1,1) where m1 is the ID of the metric selected.

![fill-m-s](/imgs/fill-m.png)

- `FILL(metric, REPEAT)` – The FILL function is used to fill the missing value with the most recent actual value before the missing value. You can edit and add FILL(m1,REPEAT)

![filled-repeat](/imgs/filled-repeat.png)

- `FILL(metric, LINEAR)` – This is used to fill in missing values and create linear interpolation by rendering a straight line between values at the beginning and the end of the gap. You can edit and add FILL(m1,LINEAR)

![filled-linear](/imgs/filled-linear.png)

## 4. Dynamic threshold based on the weekend and weekdays

By utilizing metric math functions, we can create alarms with custom thresholds based on the time and day of the week. This is especially useful for scenarios where traffic patterns vary, such as heavy traffic during weekdays compared to weekends.

We can use metric math functions to define a variable alarm threshold based on the weekend and weekdays. The `IF()` function is powerful, given that you can combine multiple functions to create complex nested expressions. 

This example shows how to combine `IF` and `TIME_SERIES()` and set an alarm based on the time of the day.

1. Metric: Select any metric that you like to calculate dynamic alarms. 

Go to `Browse` | `AWS namespaces` | `ApplicationELB` | `Per AppELB Metrics`, and select the `ActiveConnectionCount` metric. Change the Statistic to `Average`.

2. Weekday: Select the threshold you would like to set for the metric on weekdays. 

Click on `Add math` | `All functions`, and choose `TIME_SERIES`. Change the `Details` to `TIME_SERIES(1)`. Edit the label to `weekday` and the id to `weekday`. Also, edit the id of the `ActiveConnectionCount` to `metric`.

> ActiveConnectionCount on the ALB is set to 1 on weekdays.
> CPU utilization >70% should be represented as TIME_SERIES (70) if you are using CPUUtilization metric.

3. Weekend: Select the threshold you would like to set for the metric at the weekend. 

Click on `Add math` | `All functions`, and choose `TIME_SERIES`. Change the `Details` to `TIME_SERIES(2)`. Edit the label to `weekend` and the id to `weekend`.

> ActiveConnectionCount on the ALB is set to 2 on weekends.
> Expecting more traffic over the weekend. CPU utilization >90% should be represented as TIME SERIES (90).

4. Dynamic: This function determines whether a given date and time falls within the weekend or weekday category, using the following criteria: If the date falls on a Sunday, or is after 2 AM on a Saturday, it is considered part of the weekend till 8AM on Monday. Otherwise, the date is categorized as a weekday, and the respective weekday threshold is used.

Click on `Add math` | `All functions`, and choose `IF`. Change the `Details` to `IF(((DAY(weekday) == 7 OR (DAY(weekday) == 6 AND HOUR(weekday) > 2) ) OR (HOUR(weekday) < 8 AND DAY(weekday) == 1)) , weekend, weekday)`. Edit the label to `dynamic` and the id to `dynamic`.

5. Alarm: Set the alarm value to 0 if the metric is below the threshold and 1 if above the threshold. You need to set up notifications when the alarm state has the value of 1.

Click on `Add math` | `All functions`, and choose `IF`. Change the `Details` to `IF(metric <= dynamic, 0,1)`. Edit the label to `alarm` and the id to `alarm`.

![active-conn-count](/imgs/active-conn-count.png)

You can see from the graph below that it does not trigger the weekday threshold alarm when `ActiveConnectionCount=1`, which is equal to or less than the configured threshold of 1.

![conn-count-1](/imgs/conn-count-1.png)

Whereas, the alarm was triggered when the value was `ActiveConnectionCount=2`, which is higher than the configured threshold of 1 for weekdays.

![conn-count-2](/imgs/conn-count-2.png)

This way, you can configure different thresholds based on the different days of the week and avoid false alarms.

> I didn't get to test this on a weekend
> You can use the "Create alarm" bell icon in front of the alarm metric to setup notification for when the alarm state has the value of 1.

Now, you can add to dashboard and save

![curr-dash](/imgs/curr-dash.png)

## 5. Highlighting latency above SLAs

Another powerful usage of the `IF()` function is to highlight the values that are outside of SLAs without monitoring every data point. This makes it easy to spot breaches of SLAs at a glance. This will be especially helpful for metrics related to latency, availability, and so on. 

You can create visualization for target response time breaches in a load balancer using the metric math, as follows:

1. TargetResponseTime: Select the metric for which you would like to visualize SLA breaches.

Go to `Browse` | `AWS namespaces` | `ApplicationELB` | `Per AppELB Metrics`, and select the `TargetResponseTime` metric. Change the Statistic to `Sum`. The id will be `n2`.

2. SLA: Define the SLA value of the metric in the time series.

Click on `Add math` | `All functions`, and choose `TIME_SERIES`. Change the `Details` to `TIME_SERIES(0.004)`. Edit the label to `sla` and the id to `sla`.

3. Below SLA: Provides time series metrics that are below the SLA.

Click on `Add math` | `All functions`, and choose `IF`. Change the `Details` to `IF(n2<sla,n2)`. Edit the label to `belowsla` and the id to `e2`.

4. Above SLA: Highlights the time series that are above the SLA.

Click on `Add math` | `All functions`, and choose `IF`. Change the `Details` to `IF(n2>sla,n2)`. Edit the label to `abovesla` and the id to `e1`.

![targetresponsetime](/imgs/targetresponsetime.png)

The following screenshot shows a view of the target response time view of the load balancer outside the defined SLA:

![aboveandbelow](/imgs/aboveandbelow.png)

This provides a view to visualize breaches above the threshold value to understand issues quickly.

> You can also choose to add this to the dashboard

## 6. CloudWatch search expressions

Search expressions in metric math help you to search and group metrics across multiple CloudWatch metric namespaces. Search expressions will help you query and quickly add multiple related metrics to a graph. They also enable you to create dynamic graphs that automatically add metrics to their display, even if those metrics don’t exist when you first create the graph. This will be especially useful when you are transferring CloudWatch dashboards/queries to a new AWS account.

If you would like to understand the overall size of the S3 buckets in the AWS account, we can use the metric math `SEARCH()` and `SUM()` functions to achieve the required functionality. Here is the search query that will provide a sum of the size of all the buckets in an AWS account:

```
SUM(SEARCH('{AWS/S3,BucketName,StorageType} MetricName="BucketSizeBytes"', 'Sum', 300))
```

You can see the sum of S3 buckets

![s3-buckets-sum](/imgs/s3-buckets-sum.png)

