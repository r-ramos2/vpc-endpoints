<img src="https://cdn.prod.website-files.com/677c400686e724409a5a7409/6790ad949cf622dc8dcd9fe4_nextwork-logo-leather.svg" alt="NextWork" width="300" />

# VPC Endpoints

**Project Link:** [View Project](http://learn.nextwork.org/projects/aws-networks-endpoints)

**Author:** R  

---

## VPC Endpoints

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_09bcaa8a)

---

## Introducing Today's Project!

### What is Amazon VPC?

Amazon VPC is a service that lets you launch AWS resources in a logically isolated virtual network, giving you full control over network settings like IP ranges, subnets, and gateways. It’s useful for securely managing how your resources communicate.

### How I used Amazon VPC in this project

I used Amazon VPC to create a private network for my EC2 instance, then connected it to S3 using a VPC endpoint to ensure traffic stayed within AWS, avoiding the public internet for enhanced security.

### One thing I didn't expect in this project was...

One thing I didn’t expect in this project was that simply creating a VPC endpoint wasn’t enough, as I also had to update my route table and endpoint policy to make the connection fully work.

### This project took me...

This project took me around 2 hours in total, including troubleshooting permissions and validating bucket access.

---

## In the first part of my project...

### Step 1 - Architecture set up

In this step, we're creating a VPC, launching an EC2 instance, and setting up an S3 bucket. This lays the foundation to securely connect compute resources with storage inside our private network.

### Step 2 - Connect to EC2 instance

We're connecting to the EC2 instance using EC2 Instance Connect to test access to the S3 bucket over the public internet. This helps us compare performance and security before setting up a private VPC endpoint.

### Step 3 - Set up access keys

We're heading back to the EC2 instance to configure it with our new access keys. This will allow the instance to authenticate and access the S3 bucket using the AWS CLI, so we can interact with our bucket programmatically.

### Step 4 - Interact with S3 bucket

In this step, we will configure our EC2 instance to access the S3 bucket. This will allow us to interact with the bucket from the EC2 instance, such as uploading or listing objects, using the credentials we set up earlier.

---

## Architecture set up

I started my project by launching a VPC, creating a public subnet, and deploying an EC2 instance in that subnet. I also set up a security group to allow SSH access via EC2 Instance Connect.

I also set up an S3 bucket named nextwork-vpc-endpoints-yourname in the same region as my VPC. Then I uploaded two local files to the bucket to prepare for accessing them from my EC2 instance.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_4334d777)

---

## Access keys

### Credentials

To set up my EC2 instance to interact with my AWS environment, I configured the Access Key ID, Secret Access Key, default region name, and left the default output format empty.

Access keys are credentials made up of an access key ID and secret access key. They allow programs or services like EC2 instances to securely access and interact with AWS resources using the AWS CLI or SDKs.

Secret access keys are like the password that pairs with your access key ID (the username). They are used to authenticate and authorize access to AWS services. They must be kept secure, as anyone with the secret key can access your AWS account.

### Best practice

Although I'm using access keys in this project, a best practice alternative is to use IAM roles, which securely grant permissions to EC2 instances without exposing access keys.

---

## Connecting to my S3 bucket

The command I ran was `aws s3 ls`. This command is used to list all the S3 buckets my account has access to from the EC2 instance using the AWS CLI.

The terminal responded with a list of S3 buckets, including the one named `vpc-endpoints-yourname`. This indicated that the access keys I set up successfully allowed my EC2 instance to interact with the S3 bucket.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_4334d778)

---

## Connecting to my S3 bucket

I also tested the command `aws s3 ls s3://nextwork-vpc-endpoints-yourname`, which returned a list of the files stored in my S3 bucket. This confirmed that my EC2 instance has permission to view objects in the bucket.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_4334d779)

---

## Uploading objects to S3

To upload a new file to my bucket, I first ran the command sudo touch /tmp/nextwork.txt. This command creates an empty file named nextwork.txt in the /tmp directory.

The second command I ran was aws s3 cp /tmp/nextwork.txt s3://nextwork-vpc-endpoints-yourname. This command will copy the nextwork.txt file from the EC2 instance to the specified S3 bucket.

The third command I ran was aws s3 ls s3://nextwork-vpc-endpoints-yourname. This validated that the file was successfully uploaded to the bucket, showing the list of objects including nextwork.txt.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_3e1e79a2)

---

## In the second part of my project...

### Step 5 - Set up a Gateway

To create a more secure connection between my EC2 instance and S3, I'm setting up a VPC endpoint. This will allow traffic to flow privately within AWS, without going over the public internet.

### Step 6 - Bucket policies

To create a super secure bucket policy, I’m locking down my S3 bucket so that only traffic from my VPC endpoint can access it. This will confirm that my EC2 instance is using the private connection and not the public internet.

### Step 7 - Update route tables

In this step, I'm testing whether my EC2 instance can still access the S3 bucket now that the bucket only allows traffic through the VPC endpoint. This will confirm if the endpoint setup was successful and secure.

### Step 8 - Validate endpoint conection

In this step, I’m testing my EC2 instance’s access to the S3 bucket one last time to confirm that traffic is now securely routed through the VPC endpoint and not the public internet. This validates that my setup is secure and working correctly.

---

## Setting up a Gateway

I set up an S3 Gateway, which is a VPC endpoint type used to route traffic to S3 securely by adding a route in the VPC route table—no internet needed.

### What are endpoints?

An endpoint is a private connection between your VPC and AWS services like S3, allowing data to stay within the AWS network instead of traveling over the public internet.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_09bcaa8a)

---

## Bucket policies

A bucket policy is a set of rules attached to an S3 bucket that controls who can access the bucket and what actions they can perform on it.

My bucket policy will deny all access to the bucket unless the request comes from my specific VPC endpoint, ensuring only private traffic is allowed.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_7316a13d)

---

## Bucket policies

Right after saving my bucket policy, my S3 bucket page showed 'denied access' warnings. This was because the policy blocks all access unless it comes through my VPC endpoint—so even the AWS Console is now denied by default.

I also had to update my route table because my EC2 instance wasn’t routing traffic to S3 through the VPC endpoint, which caused access to be denied even though the endpoint was set up.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_4ec7821f)

---

## Route table updates

To update my route table, I added a new route in the public subnet’s route table that directs S3-bound traffic to go through the VPC endpoint instead of the internet.

After updating my public subnet's route table, my terminal could return a list of objects in my bucket, including File 1.txt, File 2.txt, and nextwork.txt. This confirmed that my EC2 instance accessed the S3 bucket through the VPC endpoint.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_d116818e)

---

## Endpoint policies

An endpoint policy is a resource-based policy attached to a VPC endpoint that controls which AWS principals and services can use the endpoint to access specific resources, like an S3 bucket.



I updated my endpoint's policy by allowing s3:ListBucket for my IAM user and bucket. I could see the effect of this right away, because my EC2 instance could finally list the contents of the S3 bucket.

![Image](http://learn.nextwork.org/serene_teal_majestic_duck/uploads/aws-networks-endpoints_3e1e79a3)

---

---
