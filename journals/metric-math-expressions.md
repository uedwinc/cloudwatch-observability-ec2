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

