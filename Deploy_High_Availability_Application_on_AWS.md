# Deploy a Highly Available Web Application on AWS

This project demonstrates how to deploy a **Highly Available (HA)** web application on **AWS** using services such as **EC2**, **ALB (Application Load Balancer)**, **Auto Scaling**, **VPC**, **Launch Templates**, and **CloudWatch**. The architecture is designed to ensure scalability, fault tolerance, and high availability, making the application resilient to fluctuating traffic conditions.

*Note: This is not a live project. For demonstration purposes, I manually SSH into the public instance and use the `stress` command to simulate high CPU utilization. This simulates increased traffic and triggers the scaling process, causing the number of instances to increase up to the configured maximum.*

## Services Used

- **EC2**: Virtual machines hosting the web application.
- **ALB (Application Load Balancer)**: Distributes traffic across multiple EC2 instances for better performance and availability.
- **Auto Scaling**: Adjusts the number of EC2 instances based on traffic demands.
- **VPC**: Provides network isolation and segmentation for AWS resources.
- **Launch Template**: Standardizes EC2 instance configurations for consistency.
- **CloudWatch**: Monitors resources and triggers auto-scaling actions based on metrics.

---

## VPC Configuration

- **VPC Name**: `MyVPC`  
- **CIDR Block**: `10.0.0.0/16` (Supports up to **65,536 IP addresses**).

### Subnets

- **Public Subnets**:
  - `10.0.1.0/24` (Availability Zone: `us-1a`)
  - `10.0.2.0/24` (Availability Zone: `us-1b`)
  
- **Private Subnets**:
  - `10.0.3.0/24` (Availability Zone: `us-1a`)
  - `10.0.4.0/24` (Availability Zone: `us-1b`)

Public subnets enable direct internet access for external resources, while private subnets house internal services.

### Internet Gateway (IGW)
- **IGW**: Allows internet access for instances in the public subnets.

### No NAT Gateway & VPC Endpoint
- **No NAT Gateway**: Private subnets do not have direct internet access.
- **No VPC Endpoints**: Services in private subnets must use alternative access methods.

---

## EC2 Configuration

- **Instance Type**: `t2.micro` (Free-tier eligible for light traffic).
- **AMI**: Latest **Ubuntu**.
- **Security Group**: `web-sg` with inbound access on:
  - **Port 22** (SSH) for management.
  - **Port 80** (HTTP) for web traffic from the ALB.

- **VPC**: Attached to `MyVPC`.
- **Subnet**: Deployed in either public subnet `10.0.1.0/24` or `10.0.2.0/24`.

---

## Application Load Balancer (ALB)

- **VPC**: Deployed across both public subnets in `MyVPC` for multi-AZ availability.
- **Security Group**: `ALG-SG` allowing inbound HTTP traffic (port 80).

### Target Group (TG)
- **Target Group Name**: `custom-TG` routes traffic to EC2 instances based on load balancing.

---

## Launch Template

- **Launch Template Name**: `Custom-LT`
  - Standardizes EC2 configuration (instance type, security groups, etc.) for Auto Scaling.

---

## Auto Scaling Group (ASG)

- **ASG Name**: `eCommerce-ASG`
  - **Launch Template**: Attach `Custom-LT`.
  - **Load Balancer**: Attach the ALB to the Auto Scaling Group.

### Scaling Configuration
- **Desired Capacity**: 1 instance initially.
- **Maximum Capacity**: 4 instances.
- **Minimum Capacity**: 1 instance.

### Scaling Policy
- **Metric**: **CPU Utilization**.
- **Threshold**: Scaling actions trigger when CPU usage exceeds 50%.
- **Warmup Period**: 120 seconds for system stabilization.

---

## CloudWatch Monitoring

- **CloudWatch Alarms**: Triggers scaling actions when CPU usage reaches 50%.
- **Manual CPU Stress Test**: To simulate high traffic, I SSH into the public instance and run the `stress` command, manually increasing CPU utilization to demonstrate the scaling functionality. The system automatically scales as CPU usage crosses the configured threshold.

---

## Technical Workflow

- **User Traffic:** When users access the web application, the ALB manages the incoming traffic and distributes it across the available instances.

- **Traffic Surge:** If there is an increase in traffic, CPU utilization on instances rises.

- **Stress Test:** We manually run a stress command to simulate high CPU usage.

- **Auto Scaling Trigger:** When CPU utilization exceeds 50%, the Auto Scaling Group (ASG) launches new instances to balance the load.

- **CloudWatch Monitoring:** CloudWatch continuously monitors instance performance and triggers an alarm when CPU usage exceeds the threshold.

- **Notification Alert:** An email alert is sent when the ASG scales instances up or down.

- **Scale-In Policy:** When CPU utilization drops, ASG automatically removes extra instances to optimize costs.

---

## Conclusion

This project sets up a **Highly Available (HA)** web application on **AWS**, leveraging Auto Scaling and Load Balancing across multiple Availability Zones to handle varying traffic loads. By using CloudWatch for real-time monitoring and auto-scaling, the architecture ensures fault tolerance, scalability, and high availability, adapting automatically to demand without manual intervention.
