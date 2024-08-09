# Exploring CloudWatch Contributor Insights and its use cases

CloudWatch Contributor Insights (CCI), as the name suggests, will allow you to know the top contributors to a specific log group(s) or natively supported AWS services such as DynamoDB. CloudWatch Contributor Insights supports logs in the format of JSON or `CLF` (short for Common Log Format) (https://en.wikipedia.org/wiki/Common_Log_Format).

A contributor can be any field within a log entry that can be aggregated.

The output of the CCI can be displayed as a part of CloudWatch dashboards. Let’s look at an example and enable CCI for DynamoDB.

In the [Exploring CloudWatch Application Insights](./cloudwatch-application-insights.md) section, we deployed an application with DynamoDB as the database. Let’s enable CCI for DynamoDB and see how it works:

1. Navigate to `DynamoDB` | `Tables` | `scorekeepappinsight-stateTable-*` | `Monitor` | `Enable CloudWatch Contributor Insights`.

![cci](/imgs/cci.png)

2. Select `Turn on` for both the primary key and secondary key. Click `Save changes`.

![enable-cci](/imgs/enable-cci.png)

3. After a few application plays of the sample score-keep application, you can verify the most accessed keys by navigating to `CloudWatch` | `Logs` | `Contributor Insights`. Select any of the `scorekeepappinsight-statetable` rule to verify the consumed throughput units. You will see that the top key in terms of consuming DynamoDB capacity.