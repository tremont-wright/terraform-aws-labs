Terraform Lab: S3 + VPC + Public Subnet + Security Group + EC2 (us-east-2) 

Date: January 2, 2026 
 

Lab Objective 

Build a small AWS environment with Terraform in region us-east-2: 
• S3 bucket 
• VPC 
• Public subnet (auto-assign public IP) 
• Internet Gateway + public route table + association 
• Security Group (SSH inbound, all outbound) 
• EC2 instance in the public subnet (attempted) 
 

Prerequisites 

• Terraform installed and available in PATH 
• AWS credentials configured on the Mac with permissions to create S3 and EC2/VPC resources 
• Working directory: /Users/tremontwright/terraform-lab 
 

Project Files 

Observed files in the project folder: 
• main.tf (Terraform configuration) 
• main.tf.save (backup copy) 
• terraform.tfstate (state file) 
• terraform.tfstate.backup (state backup) 
 

Step-by-Step Lab Commands 

1) Confirm current directory 

Command(s): 

pwd 
ls 

Notes: Confirmed user home directory and that the terraform-lab folder exists. 

2) Enter the Terraform project directory 

Command(s): 

cd terraform-lab 
ls 

Notes: Verified the project contains main.tf and state files. 

3) Edit Terraform configuration 

Command(s): 

nano main.tf 

Notes: Opened main.tf in nano to paste/edit the lab templates. 

4) Preview changes (plan) 

Command(s): 

terraform plan 

Notes: Terraform showed 8 resources to be created (Plan: 8 to add, 0 to change, 0 to destroy). 

5) Apply changes (create resources) 

Command(s): 

terraform apply 
# type: yes 

Notes: Terraform created VPC, IGW, route table, security group, subnet, route table association, and S3 bucket. EC2 creation failed due to invalid AMI ID. 

6) Inspect tracked resources (state) 

Command(s): 

terraform state list 

Notes: State confirmed created resources were tracked (7 resources; EC2 was not created). 

7) Cleanup (destroy resources) 

Command(s): 

terraform destroy 
# type: yes 

Notes: Destroyed all tracked resources successfully (Destroy complete! Resources: 7 destroyed). 

Terraform Plan Summary 

Terraform planned to create the following resources (8 total): 
• aws_s3_bucket.bucket 
• aws_vpc.main 
• aws_subnet.public 
• aws_internet_gateway.gw 
• aws_route_table.public 
• aws_route_table_association.public_assoc 
• aws_security_group.web_sg 
• aws_instance.web 
 

Apply Results 

Created successfully: 
• VPC: vpc-00b2dd7a8bd431f7e 
• Internet Gateway: igw-0b9c27a39d82a783d 
• Route Table: rtb-0b6359b428caab79d 
• Security Group: sg-0828ca42ec03b4ac2 
• Subnet: subnet-028f4472df00ea07e 
• Route table association: rtbassoc-0a6e04d10afcb983e 
• S3 Bucket: imperium-maris-terraform-test-1801 
 

EC2 creation failed with the following error: 

InvalidAMIID.NotFound: The image id '[ami-0fc5d935ebf8bc3bc]' does not exist 

Why the EC2 Failed 

AMI IDs are region-specific and can change over time. The AMI specified in main.tf was not available/valid in us-east-2 for this AWS account at the time of the run. 

How to Fix the EC2 Step (Recommended) 

Option A (best practice): Use a data source to fetch a valid Amazon Linux AMI automatically. 

data "aws_ami" "al2023" { 
  most_recent = true 
  owners      = ["amazon"] 
 
  filter { 
    name   = "name" 
    values = ["al2023-ami-*-x86_64"] 
  } 
} 

Then reference it in the instance: 

resource "aws_instance" "web" { 
  ami                    = data.aws_ami.al2023.id 
  instance_type          = "t2.micro" 
  subnet_id              = aws_subnet.public.id 
  vpc_security_group_ids = [aws_security_group.web_sg.id] 
 
  tags = { Name = "lab-ec2" } 
} 

Option B: Look up a valid AMI in the AWS Console (EC2 → AMIs) or via AWS CLI, then replace the ami value in main.tf. 

Cleanup Confirmation 

terraform destroy removed all created resources. This prevents ongoing charges and leaves the account clean. 

 
