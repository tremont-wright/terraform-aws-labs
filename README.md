# Terraform Lab: S3 + VPC + Public Subnet + Security Group + EC2 (us-east-2)

**Date:** January 2, 2026

---

## Lab Objective

Build a small AWS environment with Terraform in region `us-east-2`:

- S3 bucket  
- VPC  
- Public subnet (auto-assign public IP)  
- Internet Gateway + public route table + association  
- Security Group (SSH inbound, all outbound)  
- EC2 instance in the public subnet (attempted)

---

## Prerequisites

- Terraform installed and available in PATH  
- AWS credentials configured locally with permissions to create S3 and EC2/VPC resources  
- Working directory: `/Users/tremontwright/terraform-lab`

---

## Project Files

- `main.tf` — Terraform configuration  
- Templates and documentation  

> Note: Terraform state files and secrets are intentionally excluded from version control.

---

## Step-by-Step Lab Commands

### 1) Enter the Terraform project directory

```bash
cd terraform-lab

 
