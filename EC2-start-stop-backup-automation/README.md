# EC2-Automation-with-AWS-Lambda
### 📌 AWS Services Used
🔹 **Amazon EC2** – Manage and run cloud-based VMs.
🔹 **IAM** – Roles & policies for Lambda permissions.
🔹 **AWS Lambda** – Executes automation logic.
🔹 **Amazon CloudWatch** – Triggers Lambda on schedule.
🔹 **Amazon EBS** – Stores and snapshots EC2 volumes.

---


 ## ✅ Tasks Breakdown

### 🛠️ Task 1: Launch EC2 Instance

- Go to the **EC2 dashboard** in the AWS Console.
- Click **Launch Instance** and choose your preferred AMI (e.g., Amazon Linux or Ubuntu).
- Select instance type (e.g., `t2.micro` for free tier).
- Configure network settings (VPC, subnet).
- Attach a security group allowing SSH.
- Launch with a key pair for SSH access.
- Tag :AutoBackup and value: true

 
 ![Screenshot (165)](https://github.com/user-attachments/assets/d72f4e5a-9bf6-4570-87d1-5a313e1e1b54)




### 🛠️ Task 2: Create IAM Policy for Start, Stop & Backup EC2

- Go to **IAM > Policies** and click **Create Policy**.
- Choose the **JSON** tab and paste the following:

```json
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Action": [
        "ec2:StartInstances",
        "ec2:StopInstances",
        "ec2:CreateSnapshot",
        "ec2:DescribeInstances",
        "ec2:DescribeVolumes",
        "ec2:CreateTags"
      ],
      "Resource": "*"
    }
  ]
}

```

- Name it: Automationpolicy
- Save the policy.


  ![Screenshot (163)](https://github.com/user-attachments/assets/7c05cf3f-27b9-4618-a683-d9dd6c6308f8)


### 🛠️ Task 3: Create IAM Role and Attach Policy

- Go to IAM > Roles > Create Role.
- Select Lambda as the trusted entity.
- Attach the Automationpolicy created.
- Name the role: LambdaEC2automation.

 ![Screenshot (164)](https://github.com/user-attachments/assets/66f4424b-d9ae-48a1-b61b-78a9205a79fc)

### 🛠️ Task 4: Create Lambda Function for Automation

- Go to AWS Lambda > Create Function.
- Runtime: Python 3.x
- Use the following sample for StartEC2 (similar logic for Stop/Backup):
- Repeat for StopEC2() and BackupEC2() logic.
- Assign the role LambdaEC2automation to the function.

``` python
import boto3

def lambda_handler(event, context):
    ec2 = boto3.client('ec2')
    filters = [{'Name': 'tag:AutoBackup', 'Values': ['true']}]
    instances = ec2.describe_instances(Filters=filters)
    instance_ids = []

    for reservation in instances['Reservations']:
        for instance in reservation['Instances']:
            instance_ids.append(instance['InstanceId'])

    if instance_ids:
        ec2.start_instances(InstanceIds=instance_ids)
        print(f"Started instances: {instance_ids}")
    else:
        print("No instances found for AutoBackup.")

```

 ![Screenshot (167)](https://github.com/user-attachments/assets/92560fc5-0a60-46f0-be65-6df7e64fc6cb)
 

### 🛠️ Task 5: Schedule with CloudWatch

- Choose EventBridge > Rules > Create rule (CloudWatch Events).
- Select schedule > Recurring schedule > Cron-based schedule:
- Example (start at 8 AM UTC daily):
- Set the target as your Lambda function.
- Repeat for stop and backup with different schedules.

  ![Screenshot (168)](https://github.com/user-attachments/assets/cab6183e-4170-4d18-a795-56f79859be80)


### 🛠️ Task 6: Validate Backups (Snapshots)

- Go to EC2 > Snapshots.
- Check for EBS snapshots created by the Lambda function.
- Verify they are tagged correctly (e.g., with instance ID and timestamp).
- Confirm scheduled backups are running as expected.

![Screenshot (174)](https://github.com/user-attachments/assets/77d89b74-4b77-421f-8e35-9eee8a7b8431)






 
