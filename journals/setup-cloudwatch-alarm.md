# CloudWatch alarms

CloudWatch alarms will help you measure a defined data point and determine whether it is in an `alarm` state, `OK` state, or has `insufficient data`. Based on the state of the alarm, we can set up notifications and notify the team, group, or a third-party system that will be interested in the application’s state. Additionally, an automated response can be set up to act on the alarm so that auto-remediation activities can be carried out.

# Create fundamental CPU alarm for the monitored EC2 instance and send notifications via email using AWS SNS

### Static threshold alarm

First, let’s set up a static threshold alarm.

- Navigate to `CloudWatch` | `Alarms` | `All Alarms`| `Create alarm`.

- Click `Select Metric` | `Browse` | `AWS namespaces` | `EC2` | `Per-Instance Metrics`. Now, select `CPU utilization` for the instance that you created earlier, and click `Next`.

- Select the `Greater` threshold and set the threshold value to 70%, then click on `Next`.

- Next, you will select `Create new topic` and provide your email address. Then select `Create topic` and click on `Next`.

- You will need to confirm this subscription from your email

- Provide the alarm name as `CPU Utilization`, then click `Next`, and create the alarm.

- Repeat the process to create an alarm for `CPU Credit Balance`.

![both-alarms](/imgs/both-alarms.png)

### Composite alarm

You can create composite alarms to reduce alarm fatigue and provide better visibility of the problems that are impacting your application. For example, if you consistently have high CPU usage and low CPU credit balance, it may be necessary to increase the instance size to handle high load and prevent application failures.

We have created two static threshold alarms, one for `CPU Utilization`, and the other for `CPU Credit Balance`. Let’s create a composite alarm, combining them:

- Select the `CPU Utilization` and `CPU Credit Balance` alarms and click on `Create composite alarm`

- Change the condition to `AND` and click `Next`.

![AND-condition](/imgs/AND-condition.png)

- Under `Configure actions`, select existing SNS topic and click `Next`.

- Enter an alarm name.

- Finally, review and click `Create composite alarm`.

- This creates a composite alarm and alerts when both the alarms are in the Alarm state.

![composite](/imgs/composite.png)

The following are some of the best practices to keep in mind when configuring alarms:

  - Create alarms based on meaningful metrics and KPIs
  - Test alarm actions
  - Use composite alarms to eliminate noise
  - Use alarms for automatic responses to events
  - Iterate alarm definitions over time