# AWS-lab-Work


## Migration of VM to AWS
Migrated a virtual machine to AWS EC2, configured the cloud environment and validated post-migration performance and connectivity.


## ☁️ AWS Services Used

The following AWS services were used during the VM migration process:

- **Amazon S3** – To upload and store the exported OVA file.
- **AWS CLI** – To execute AWS commands from the terminal.
- **IAM (Identity & Access Management)** – To create a role with trust policy for VM import.
- **VM Import/Export** – To convert the OVA file into an Amazon Machine Image (AMI).
- **Amazon EC2** – To launch and run the virtual machine in the AWS cloud.
- **Amazon Machine Image (AMI)** – The OVA file was converted into an AMI to be used with EC2.

### Task 1: Run a VM on VMware Workstation using Windows 10 Pro ISO image

![image](https://github.com/user-attachments/assets/ac0add1d-fb88-444c-8d56-e00e244dddaf)



### Task2: Create ova file of the VM to upload into my AWS S3 bucket


 ![image](https://github.com/user-attachments/assets/c5602504-39c8-4797-8efe-794dab426e9c)



### Task3: Install AWS CLI & Logged in with my Access key ID and secret access key.

![image](https://github.com/user-attachments/assets/83f2c818-3f9b-42c4-99d7-6fde9348e24d)


![image](https://github.com/user-attachments/assets/d4556a92-9146-4b38-ab37-e8f0e2825a12)
 

### Task4: Create AWS role for VM importing 
(command - aws iam create-role --role-name vmimport --assume-role-policy-document "file://C:\import\trust-policy.json" )
(aws iam put-role-policy --role-name vmimport --policy-name vmimport --policy-document "file://C:\import\role-policy.json")

####  🔐 IAM Trust Policy - allows AWS services to assume the role
 
```json
{
   "Version": "2012-10-17",
   "Statement": [
      {
         "Effect": "Allow",
         "Principal": { "Service": "vmie.amazonaws.com" },
         "Action": "sts:AssumeRole",
         "Condition": {
            "StringEquals": {
               "sts:Externalid": "vmimport"
            }
         }
      }
   ]
}
```

####  🔐 IAM Role Policy - defines what actions are allowed when the role is assumed

```json
{
   "Version":"2012-10-17",
   "Statement":[
      {
         "Effect": "Allow",
         "Action": [
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket" 
         ],
         "Resource": [
            "arn:aws:s3:::myvmupload1",
            "arn:aws:s3:::myvmupload1/*"
         ]
      },
      {
         "Effect": "Allow",
         "Action": [
            "s3:GetBucketLocation",
            "s3:GetObject",
            "s3:ListBucket",
            "s3:PutObject",
            "s3:GetBucketAcl"
         ],
         "Resource": [
            "arn:aws:s3:::myvmupload1",
            "arn:aws:s3:::myvmupload1/*"
         ]
      },
      {
         "Effect": "Allow",
         "Action": [
            "ec2:ModifySnapshotAttribute",
            "ec2:CopySnapshot",
            "ec2:RegisterImage",
            "ec2:Describe*"
         ],
         "Resource": "*"
      }
   ]
}
```

![image](https://github.com/user-attachments/assets/4b59b616-384a-4b14-a98a-735e0b7b10d9)
 



### Task5: Upload the OVA file to S3 bucket

![image](https://github.com/user-attachments/assets/683955a5-3d24-4c08-a28e-a1c33a35c1f5)
 


### Task6: Import the AMI 
(command - aws ec2 import-image --description "My server VM" --disk-containers "file://C:\import\containers.json")


####  🔐 Import VM into AMI as a Disk Container
```json
[
  {
    "Description": "My Server VM",
    "Format": "ova",
    "UserBucket": {
        "S3Bucket": "myvmupload1",
        "S3Key": "My vm.ova"
    }
  }
]
```


![image](https://github.com/user-attachments/assets/e155f625-50ec-44f5-a3de-01785d7237dd)


 
### Task7: Launch instance using the imported AMI

![image](https://github.com/user-attachments/assets/219b3fa2-4af1-4ae8-9747-f6ffd7f5d8fb)

 

### Task8: VM running from AWS

 ![image](https://github.com/user-attachments/assets/4dd73040-7b05-4f6d-8221-890a0c952f3e)



