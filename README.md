# Deploy HA Web Application on AWS

This project focuses on deploying a **Highly Available (HA)** web application on **Amazon Web Services (AWS)** using various services, such as **EC2**, **Application Load Balancer (ALB)**, **Auto Scaling**, **VPC**, **Launch Templates**, and **CloudWatch**. The architecture is designed for scalability, fault tolerance, and high availability, ensuring the web application is resilient and responsive to changing traffic conditions.

## Services Used

- **EC2**: Virtual machines that will host the web application.
- **ALB (Application Load Balancer)**: Distributes incoming application traffic across multiple EC2 instances.
- **Auto Scaling**: Automatically adjusts the number of EC2 instances based on traffic demand.
- **VPC**: Provides networking and isolation for AWS resources.
- **Launch Template**: Simplifies the creation and management of EC2 instances.
- **CloudWatch**: Monitors AWS resources and triggers auto-scaling actions based on metrics.

---

## VPC Configuration

A **Virtual Private Cloud (VPC)** is created to provide network isolation and segmentation for the AWS infrastructure.

### 1. Create VPC

- **VPC Name**: `MyVPC`
- **CIDR Block**: `10.0.0.0/16`  
  - This allows the VPC to have up to **65,536 IP addresses**.
  
### 2. Subnets

The VPC will be divided into both **public** and **private** subnets across multiple availability zones for high availability.

- **Public Subnets**:
  - `10.0.1.0/24` (Availability Zone: `us-1a`)
  - `10.0.2.0/24` (Availability Zone: `us-1b`)

- **Private Subnets**:
  - `10.0.3.0/24` (Availability Zone: `us-1a`)
  - `10.0.4.0/24` (Availability Zone: `us-1b`)

Public subnets are used for resources that need direct internet access, while private subnets are used for internal services that don't require direct exposure.

### 3. Internet Gateway (IGW)

- **Internet Gateway (IGW)** is attached to the public subnets to allow EC2 instances in those subnets to access the internet for software updates or for serving the web application.

### 4. No NAT Gateway & No VPC Endpoint

- No **NAT Gateway** is configured, meaning the private subnets do not have direct access to the internet.
- No **VPC Endpoints** are set up for private services like S3, meaning services in private subnets need alternative access methods.

---

## EC2 Configuration

**EC2 instances** will host the web application, which is accessible via the load balancer.

### 1. Create EC2 Instance for Web Server

- **Instance Name**: `Web Server`
- **Instance Type**: `t2.micro`  
  - This instance type is part of the AWS Free Tier and is suitable for low to moderate traffic.

- **Security Group**: `web-sg`
  - **Port 22** (SSH): To allow secure remote access to the instance for management and debugging.
  - **Port 80** (HTTP): To allow inbound web traffic from the ALB (Application Load Balancer).
  - Security group `web-sg` is configured to allow traffic from the ALB security group.

- **AMI**: Ubuntu (latest version)
  - Ubuntu is a popular choice for deploying web applications due to its stability and ease of use.

- **VPC**: Attach to the `MyVPC`
- **Subnet**: Attach to one of the public subnets (10.0.1.0/24 or 10.0.2.0/24)

---

## ALB (Application Load Balancer)

**Application Load Balancer (ALB)** is used to distribute incoming web traffic across multiple EC2 instances to ensure better availability and performance.

### 1. Create ALB

- **VPC**: The ALB is created in the same `MyVPC` and will span across both **public subnets** to ensure it’s available in multiple Availability Zones for fault tolerance.
- **Security Group**: `ALG-SG`
  - **Inbound Rule**: Allow all traffic (port 80) from anywhere, as the application will be publicly accessible via HTTP.
  
### 2. Create Target Group (TG)

- **Target Group Name**: `custom-TG`
  - A target group defines the EC2 instances that the ALB will route traffic to.
  - EC2 instances are registered with this target group, allowing the ALB to know where to send the incoming web traffic.

### 3. Attach Target Group to ALB

- The target group (`custom-TG`) is attached to the ALB so that traffic can be directed to the EC2 instances based on the load balancing policy.

---

## Launch Template

A **Launch Template** is used to standardize the configuration of EC2 instances, ensuring that all instances launched by the Auto Scaling Group are identical in configuration.

### 1. Create Launch Template

- **Launch Template Name**: `Custom-LT`
- The launch template is created based on the configuration of the EC2 instance (`Web Server`) to ensure all instances launched via Auto Scaling have the same setup (instance type, security groups, etc.).

---

## Auto Scaling Group (ASG)

**Auto Scaling** automatically adjusts the number of running EC2 instances based on traffic demands to maintain application availability and minimize costs.

### 1. Create Auto Scaling Group

- **Auto Scaling Group Name**: `eCommerce-ASG`
- **Launch Template**: Attach the `Custom-LT` launch template.
- **Load Balancer**: Attach the previously created ALB to the Auto Scaling Group, ensuring that traffic is directed to the right instances.
  
### 2. Scaling Configuration

- **Desired Capacity**: 1 instance (initially, only one EC2 instance is running).
- **Minimum Capacity**: 1 instance (minimum number of instances required to serve traffic).
- **Maximum Capacity**: 4 instances (maximum number of instances allowed to scale).

### 3. Scaling Policy

- **Scaling Policy Name**: `target-policy`
- **Metric**: CPU Utilization
- **Threshold**: When CPU utilization reaches **50%**, the Auto Scaling group will either add more instances (scale out) or remove instances (scale in) to maintain the desired CPU usage level.
- **Warmup Period**: 120 seconds, ensuring that the system has enough time to stabilize before scaling again.

### 4. CloudWatch Monitoring & Notifications

- **CloudWatch**: Enable CloudWatch monitoring for EC2 instances and Auto Scaling policies.
- **Scaling Alarms**: Create CloudWatch alarms that will trigger scaling actions when CPU utilization reaches or exceeds 50%.
  - These alarms notify you about the scaling events and help you monitor the system's performance.

### 5. Manual CPU Stress Test

To verify that the Auto Scaling functionality works as expected, the CPU is manually stressed using the `stress` command. This simulates high traffic and triggers scaling actions when the CPU utilization crosses the threshold.

---

## Conclusion

This project sets up a highly available (HA) web application on AWS, using Auto Scaling and Load Balancing to ensure the application can handle varying traffic loads efficiently. The use of multiple Availability Zones ensures that the application remains available even if an entire zone goes down. CloudWatch monitoring allows for real-time scaling, making sure that the infrastructure automatically adjusts based on demand.

This architecture provides scalability, fault tolerance, and the flexibility to handle growing traffic without manual intervention.
