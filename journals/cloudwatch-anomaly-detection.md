# CloudWatch anomaly detection

CloudWatch anomaly detection applies machine-learning intelligence to automate, accelerate, and improve the detection of abnormal system and application behavior.

[Previously](./metric-math-expressions.md), we set up dynamic thresholds based on the day/time of the week, but this is still a manual methodology to determine thresholds. If you are looking to automate this and leverage machine learning, consider using the CloudWatch anomaly detection tool.

Some of the use cases for applying anomaly detection are as follows:

- Detect Unexpected Volume Changes
- Monitor Dynamic Applications
- Identify Deployment Side Effects
- Proactive Troubleshooting
- Monitor Business Metrics

Let’s see how to configure CloudWatch anomaly detection and also delete existing models from CloudWatch:

1. In the AWS Management Console, on the `Services` menu, click `CloudWatch`.

2. In the left navigation menu, click on `Metrics`.

3. Click on `All metrics` | `ApplicationELB` | `per AppELB Metrics` | `TargetResponseTime` and navigate to the `Graphed metrics` tab.

![enable-anomaly](/imgs/enable-anomaly.png)

4. Under the `Actions` column, the first icon that looks like a power charge is the anomaly detection icon. Click on the `Anomaly detection` icon.

5. `Anomaly detection (AD)` will be enabled immediately. A model is created based on the metric data from the past 2 weeks. AD will also be enabled even if there is no data available for the two-week period.

![anomaly-band](/imgs/anomaly-band.png)

6. Notice the expression in the `ANOMALY_DETECTION_BAND(m1,2)` graph. This indicates that the metric m1 anomaly detection has been enabled with a standard deviation of 2. You can adjust the standard deviation number to increase the deviation scope for the metric data point if required. You can simply edit the expression to say `ANOMALY_DETECTION_BAND(m1,3)` and click `Apply`.

7. At times, there may be certain data that you wish to exclude from the training model, such as periods during which traffic deviates from the norm, such as a promotional campaign. You could do that by clicking on `ANOMALY_DETECTION_BAND(m1,3)` and clicking on `Edit anomaly detection model`

![edit-anomaly](/imgs/edit-anomaly.png)

![edit-anomaly-page](/imgs/edit-anomaly-page.png)

8. Select the exclusion time from the model training and click on the `Apply` button.

9. Select the time zone of the period to exclude or add additional timings to exclude from the AD model training and click `Update`.

10. To delete the model, you can simply click `Delete anomaly detection model` and then click `Delete`.

You can also create alarms, as discussed [previously](./setup-cloudwatch-alarm.md), for anomaly detection based on the model created.