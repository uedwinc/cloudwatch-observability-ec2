# Amazon EventBridge

From an observability point of view, Amazon EventBridge is a service that allows you to subscribe to events and receive notifications when those events occur. It is a fault monitoring service provided by AWS. An event is a signal that a system’s state has changed, such as an EC2 instance that has been shut down.

![architecture](/imgs/eventbridge-architecture.png)

Beyond observability, Amazon EventBridge could be leveraged to build event-driven architecture for your business applications.

# Subscribing to an event for an EC2 state change for events such as shut down, restart, stopped, and so on

- Navigate to the `Services` search bar –> `EventBridge`. Then click on `Create rule`

- Provide a name for the rule, such as `EC2_State_Change`. Leave `Event bus` as `default`. Set `Rule type` to `Rule with an event pattern` and click `Next`.

- Under `Event Source`, select `AWS events or EventBridge partner events`. For `Creation method`, select `Use pattern form`.

- Modify `Event pattern` as follows:
  - Event source: AWS services
  - AWS service: EC2
  - Event type: EC2 Fast Launch State-change Notification

- Click `Next`.

- Set `Target 1` as `AWS service`, then choose `SNS Topic` for `Select a target`. Under `Topic`, choose `windows-instance-cloudwatch-alarm-topic` (this was created earlier), then click `Next`. You will have to create a new SNS topic if not already available.

- The `Configure tag(s)` field is optional, so click `Next`.

- In `Review and create`, click on `Create rule`.

You can test whether the rule is working or not by stopping the EC2 instance. You should receive email alerts for state changes of the instance, such as stopping, stopped, and so on.