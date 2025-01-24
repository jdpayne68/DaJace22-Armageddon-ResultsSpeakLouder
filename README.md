#Project Armageddon - ResultsSpeakLouder

---

# A Multi-Region Hub-and-Spoke Web Application with Centralized SIEM

## Summary

This project creates a multi-region, customer-facing web application using Terraform on AWS. The architecture utilizes a hub-and-spoke topology, featuring centralized logging and monitoring with Loki and Grafana, provisioned automatically through user data scripts.

Each spoke region contains:
- A single VPC hosting:
  - ALB (Application Load Balancer)
  - Auto Scaling Group with EC2 instances
  - NAT Gateway and Internet Gateway
  - Each EC2 instance has Promtail installed via a user data script for log collection

The central hub region houses:
- Two VPCs:
  - A SIEM VPC which hosts Loki/Grafana for centralized logging and monitoring in a private subnet, installed through a user data script
  - A web application VPC mirroring spoke regions

All regions are interconnected via Transit Gateways (TGWs), configured with peering connections to a single hub. This architecture simplifies and centralizes the networks structure
to ensure seamless connectivity. 

---

## Features
1. Multi-Region Deployment:
   - 1 central hub region and 6 spoke regions
2. Hub-and-Spoke Configuration:
   - Centralized monitoring and logging
   - Fully interconnected Transit Gateway architecture
3. Automated Setup:
   - Promtail, Loki, and Grafana are deployed automatically through user data scripts
4. Infrastructure Automation:
   - Infrastructure defined and provisioned using Terraform
5. Centralized Monitoring:
   - Promtail collects logs from all regions
   - Logs are aggregated in the central hub region and visualized in Grafana
6. High Availability:
   - Auto-scaling and load balancing in each region
   - Resilient transit gateway connections

---

## Architecture

### Network Overview
![Network Diagram](https://github.com/user-attachments/assets/6ba644a0-d35f-4fa3-b12f-2a84df6a1f97)


### Routing Architecture
![Routing Diagram](https://cdn.discordapp.com/attachments/1277613088488685632/1315015699940245515/image.png?ex=675730ce&is=6755df4e&hm=282391458150aa12184deebed85e23df188689ea02c9b89dd3936ce9861ef3ea&)

### Proof of Connectivity
![Tool Screenshot](https://cdn.discordapp.com/attachments/1277613088488685632/1314891650211905546/image.png?ex=6756bd46&is=67556bc6&hm=e0fd106efa6a6a2431a4a995358fff57bcac1636c9e645b22b58e84616288095&)

---

## Prerequisites
- Terraform (>= v1.0)
- AWS credentials configured for access to all 7 regions 
- Access to the Terraform state storage backend (e.g., S3)
- Installed tools for monitoring and logging (Loki, Grafana, Promtail)

---

## Deployment

### Step 1: Clone the Repository
```bash
git clone https://github.com/DaJace22/DaJace22-Armageddon-ResultsSpeakLouder
cd <project-directory>
```

### Step 2: Initialize Terraform
```bash
terraform init
```

### Step 3: Customize Variables
Update the required variables, such as region names, CIDR blocks, and instance sizes in the file (Note: some instance sizes might not be in every region)

#### Step 4: Validate Terraform
```bash
terraform validate
```

### Step 5: Preview the Plan for Infrastructure Set up
```bash
terraform plan -autoapprove
```

#### Step 5: Deploy the Infrastructure 
```bash
terraform apply -autoapprove
```
# Note: During the deployment of the infrastructure, you may encounter an error regarding a resource being in and "invalid state". We are still investigating a programmatic solution. In the interim type terraform apply -autoapprove again to complete the remainder of the deployment.

### Step 7: Confirm the Set-up

1. Verify that all VPCs are deployed in their respective regions
2. Confirm Promtail, Loki, and Grafana are installed:
    Promtail: Check logs on EC2 instances in spoke regions
    Loki/Grafana: Access Grafana in the central region
3. Check connectivity across regions using the transit gateways

---

## User Data Scripts

### Promtail Setup (Spoke Regions)
Each EC2 instance in the spoke regions uses a user data script to:
- Install Promtail
- Configure it to forward logs to the Loki instance in the central region

### Loki and Grafana Setup (Hub Region)
The SIEM VPC's EC2 instance in the central region uses a user data script to:
- Install Loki for log storage
- Install Grafana for visualization
- Configure Grafana to use Loki as a data source

---

### Testing and Validation
- Connectivity: Verify network connectivity between all regions using transit gateways
- Logging: Confirm logs are being sent from spoke regions to the Loki server in the central region
- Monitoring: Check the Grafana dashboard for logs and metrics from all regions

---

### Tools and Technologies
- Terraform: Infrastructure as Code
- AWS Services:
  - EC2, VPC, ALB, ASG, Transit Gateway, Internet Gateway, NAT Gateway
- Logging/Monitoring:
  - Promtail: Log collection
  - Loki: Centralized log storage
  - Grafana: Visualization and analysis

---

### Armageddon 1.2
- We will add a DB to the the logs in different AZ than the syslog server without a public subnet
- For added security, it will be in a private subnet and the DB and Syslog server will no be on the same subnet
