# Code boto3 in AWS Lambda 

Boto3 is the AWS Software Development Kit for Python. It can call any AWS service from code, not just Lambda.

## Workflow:
1. EC2 started w/o proper tag
2. stop EC2
3. send email to employee

## AWS Architecture Design:
An event such as AWS cloudwatch triggers AWS lambda. AWS lambda will reach out to EC2 based on its tags and EC2 will use Amazon simple notification service to notify the employee

![](img/architecture.png)

### Create Lambda Role
AWS >> IAM >> Roles >> Create Role >> Lambda (Use Case) >> Next: Permission >> AmazonEC2FullAccess (type in search: EC2) >> AmazonSNSFullAccess (type in serach: SNS) >> Next:Tags >> Next:Review >> <Role Name> >> Create Role

### Create CloudWatch Input to Lambda
AWS >> CloudWatch >> Event - Rules >> Create Rule >> Service Name: EC2 >> Event type: EC2 Instance State-change Notification >> Specific States: running >> Show sample event(s) (dropdown)

```json
  {
    "version": "0",
    "id": "ee376907-2647-4179-9203-343cfb3017a4",
    "detail-type": "EC2 Instance State-change Notification",
    "source": "aws.ec2",
    "account": "123456789012",
    "time": "2015-11-11T21:30:34Z",
    "region": "us-east-1",
    "resources": [
      "arn:aws:ec2:us-east-1:123456789012:instance/i-abcd1111"
    ],
    "detail": {
      "instance-id": "i-abcd1111",
      "state": "running"
    }
  }
```

### Create Lambda Target
AWS >> Lambda >> Create Function >> function name: <EC2_checktags> >> Runtime: Python 3.8 >> Change default execution role (dropdown) >> use an existing role >> select <role name from earlier> >> Create Function

lambda_funtion.py:
 - google 'boto3 ec2' and find the client tag
```python
import boto3
client = boto3.client('ec2')
```
- go down to [describe_tags()](https://boto3.amazonaws.com/v1/documentation/api/latest/reference/services/ec2.html#EC2.Client.describe_tags) and paste it in the TODO section of code source
```python
  ec2_instance_id = event['details']['instance-id']
  response = client.describe_tags(
  Filters=[
      {
          'Name': 'resource-id',
          'Values': [ec2_instance_id]
      },
  ]
```
The EC2 instance ID is from the event, which is the json CloudWatch input to Lambda we copied from above.

Save >> Test (dropdown) Configure Test Event >> Event name: <test1> >> copy&paste json from above in code area >> Replace instance id with actual running EC2 instance>> Save >> Test
