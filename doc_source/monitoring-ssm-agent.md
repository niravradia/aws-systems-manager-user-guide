# Sending instance logs to CloudWatch Logs \(SSM Agent\)<a name="monitoring-ssm-agent"></a>

AWS Systems Manager Agent \(SSM Agent\) is Amazon software that runs on your Amazon EC2 instances and your hybrid instances that are configured for Systems Manager \(hybrid instances\)\. SSM Agent processes requests from the Systems Manager service in the cloud and configures your machine as specified in the request\. For more information about SSM Agent, see [Working with SSM Agent](ssm-agent.md)\.

In addition, following the steps below, you can configure SSM Agent to send log data to Amazon CloudWatch Logs\. 

**Important**  
The unified CloudWatch agent has replaced SSM Agent as the tool for sending log data to Amazon CloudWatch Logs\. Support for using SSM Agent to send log data will be deprecated in the near future\. We recommend using only the unified CloudWatch agent for your log collection processes\. For more information, see the following topics:  
[Sending instance logs to CloudWatch Logs \(CloudWatch agent\)](monitoring-cloudwatch-agent.md)
[Migrate Windows Server instance log collection to the CloudWatch agent](monitoring-cloudwatch-agent.md#monitoring-cloudwatch-agent-migrate)
[Collect metrics from Amazon Elastic Compute Cloud instances and on\-premises servers with the CloudWatch agent](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/Install-CloudWatch-Agent.html) in the *Amazon CloudWatch User Guide*

**Before You Begin**  
Create a log group in Amazon CloudWatch Logs\. For more information, see [Create a log group in CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/Create-Log-Group.html) in the *Amazon CloudWatch Logs User Guide*\.

**To configure SSM Agent to send logs to CloudWatch**

1. Log into an instance and locate the following file:

**Linux**  
`/etc/amazon/ssm/seelog.xml.template`

**Windows**  
`%ProgramFiles%\Amazon\SSM\seelog.xml.template`

1. Change the file name from `seelog.xml.template` to `seelog.xml`\.

1. Open the `seelog.xml` file in a text editor, and locate the following section\.

------
#### [ Linux ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" filename="/var/log/amazon/ssm/amazon-ssm-agent.log" maxsize="30000000" maxrolls="5"/>
      <filter levels="error,critical" formatid="fmterror">
         <rollingfile type="size" filename="/var/log/amazon/ssm/errors.log" maxsize="10000000" maxrolls="5"/>
      </filter>
   </outputs>
   ```

------
#### [ Windows ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" maxrolls="5" maxsize="30000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\amazon-ssm-agent.log"/>
      <filter formatid="fmterror" levels="error,critical">
         <rollingfile type="size" maxrolls="5" maxsize="10000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\errors.log"/>
      </filter>
   </outputs>
   ```

------

1. Edit the file, and add a *custom name* element after the closing </filter> tag\. In the following example, the custom name as been specified as `cloudwatch_receiver`\.

------
#### [ Linux ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" filename="/var/log/amazon/ssm/amazon-ssm-agent.log" maxsize="30000000" maxrolls="5"/>
      <filter levels="error,critical" formatid="fmterror">
         <rollingfile type="size" filename="/var/log/amazon/ssm/errors.log" maxsize="10000000" maxrolls="5"/>
      </filter>
      <custom name="cloudwatch_receiver" formatid="fmtdebug" data-log-group="your-CloudWatch-log-group-name"/>
   </outputs>
   ```

------
#### [ Windows ]

   ```
   <outputs formatid="fmtinfo">
      <console formatid="fmtinfo"/>
      <rollingfile type="size" maxrolls="5" maxsize="30000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\amazon-ssm-agent.log"/>
      <filter formatid="fmterror" levels="error,critical">
         <rollingfile type="size" maxrolls="5" maxsize="10000000" filename="{{LOCALAPPDATA}}\Amazon\SSM\Logs\errors.log"/>
      </filter>
      <custom name="cloudwatch_receiver" formatid="fmtdebug" data-log-group="your-CloudWatch-log-group-name"/>
   </outputs>
   ```

------

1. Save your changes, and then restart SSM Agent or the instance\.

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1. In the navigation pane, choose **Log groups**, and then choose the name of your log group\.
**Tip**  
The log stream for SSM Agent log file data is organized by instance ID\.