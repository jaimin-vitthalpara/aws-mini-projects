# AWS Auto Scaling: Real-time Monitoring with CloudWatch & SNS

## 📌 Project Overview
In this project, we set up an **Auto Scaling infrastructure** with monitoring using AWS. The system is designed to automatically scale in or out based on CPU utilization, ensuring efficient resource allocation. Additionally, an **SNS notification system** is implemented to alert the administrator when specific thresholds are exceeded. This project covers the creation of a **VPC, EC2 instances, an Application Load Balancer (ALB), an Auto Scaling Group (ASG), CloudWatch Alarms, and SNS notifications** to provide a highly available and responsive environment.

## 🏗️ AWS Architecture Diagram
![AWS Architecture Diagram](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/cee96e4368551d0afeffec5e211b6e6180e1a326/Autoscaling%20-%20Monitoring%20-%20V1.jpg)

## 📍 Technical Workflow

### 1️⃣ Creating the VPC & Networking
A **Virtual Private Cloud (VPC)** allows us to define an isolated network environment. We create a custom VPC named `MyVPC` with the CIDR block `10.0.0.0/16`. This will provide our cloud infrastructure with IP addressing and subnet organization.

#### 🔹 Subnets
We define four subnets for better availability and separation:
- **Public Subnet 1:** `10.0.1.0/24` (Availability Zone: `us-1a`)
- **Public Subnet 2:** `10.0.2.0/24` (Availability Zone: `us-1b`)
- **Private Subnet 1:** `10.0.3.0/24` (Availability Zone: `us-1a`)
- **Private Subnet 2:** `10.0.4.0/24` (Availability Zone: `us-1b`)

#### 🔹 Internet & NAT Gateways
- **Internet Gateway (IGW):** Attached to the VPC to allow public instances to communicate over the internet.
- **NAT Gateway:** Placed in the public subnet to allow private instances to access the internet securely.
- **Route Tables:** Configured to ensure proper routing between subnets.

---

### 2️⃣ Launching an EC2 Instance
A web server is deployed on an EC2 instance. This acts as the core component that serves application traffic.

#### 🖥️ EC2 Instance Configuration
- **Instance Name:** `Web Server`
- **Instance Type:** `t2.micro` (Free-tier eligible)
- **Security Group:** `web-sg` (Allows **SSH (22)** and **HTTP (80)** access)
- **AMI:** Ubuntu
- **Subnet Association:** Public subnet in `MyVPC`

#### 🛠️ User Data Script (For Automated Setup)
```bash
#!/bin/bash
apt-get update
sudo apt install nginx -y
echo "<h1>This is instance: $(hostname)</h1>" > /var/www/html/index.html
```
---

### 3️⃣ Registering EC2 Instance in a Target Group
A **Target Group (TG)** is created to register the EC2 instance. This allows the Application Load Balancer (ALB) to distribute traffic among instances based on health checks.

#### 🎯 Target Group Configuration
- **Name:** `custom-TG`
- **Instance Registration:** Web Server
- **Health Checks:** Configured to ensure only healthy instances receive traffic

---

### 4️⃣ Setting Up the Application Load Balancer (ALB)
An **Application Load Balancer (ALB)** is used to distribute traffic across multiple EC2 instances, improving availability and reliability.

#### 🔄 ALB Configuration
- **VPC Selection:** `MyVPC` (with both public subnets)
- **Security Group:** `ALG-SG` (Allows all inbound traffic)
- **Attach Target Group:** `custom-TG`

---

### 5️⃣ Creating a Launch Template
A **Launch Template (LT)** defines instance settings to automate EC2 instance launches in an Auto Scaling Group.

#### ⚙️ Steps to Create a Launch Template
1. Navigate to **EC2 Dashboard > Actions > Image & Template > Create Template**
2. **Launch Template Name:** `Custom-LT`
3. Use **Web Server** as the source

---

### 6️⃣ Configuring the Auto Scaling Group (ASG)
An **Auto Scaling Group (ASG)** dynamically adjusts the number of instances based on demand, ensuring high availability.

#### 📊 ASG Configuration
- **ASG Name:** `eCommerce-ASG`
- **Launch Template:** `Custom-LT`
- **Load Balancer:** Attach `ALB`
- **Capacity Settings:**
  - **Minimum:** 1
  - **Desired:** 1
  - **Maximum:** 4
- **Scaling Policy:**
  - Name: `Target Policy`
  - **Trigger:** CPU Utilization > 50%
  - **Warmup Time:** 120 seconds
- **CloudWatch Monitoring:** Enabled for auto-scale tracking

---

### 7️⃣ Setting Up CloudWatch & SNS Notifications
To monitor our instances, we configure **CloudWatch Alarms** and **SNS (Simple Notification Service)** to receive alerts when CPU usage exceeds a threshold.

#### 📈 CloudWatch Alarm
- **Metric:** Auto Scaling EC2 CPUUtilization > 50%
- **Action:** Automatically add new EC2 instances to the ASG

#### 📧 SNS Notification
- **SNS Topic:** Created to receive alerts
- **Subscription:** Email notification enabled when alarm triggers

---

### 8️⃣ Simulating High CPU Usage Using Stress Command
To test auto scaling, we simulate high CPU utilization using the **stress** tool.

#### 🏗️ Steps to Stress the CPU
```bash
ssh -i "your-key.pem" ubuntu@your-ec2-public-ip
sudo apt-get update
sudo apt-get install stress -y
stress -c 4 -t 3600
```

📌 **Monitor Auto Scaling:**
- If CPU usage exceeds 50%, ASG will **automatically launch** new instances.
- An **email notification** will be received from **SNS** confirming the scaling event.
  
---

## 📢 Conclusion
This project successfully implements **Auto Scaling** with AWS, ensuring optimal resource utilization and high availability. By leveraging **CloudWatch monitoring, Load Balancer distribution, and SNS notifications**, the system dynamically scales up or down based on demand.

---

## 📸 Project Screenshots
1. **Creating the VPC & Networking**
![Creating the VPC & Networking](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/7_VPC.png)

2. **Launching an EC2 Instance**
![Launching an EC2 Instance](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/1_Ec2_single.png)

3. **Registering EC2 Instance in a Target Group**
![Registering EC2 Instance in a Target Group](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/6_TG.png)

4. **Setting Up the Application Load Balancer**
![Setting Up the Application Load Balancer](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/5_ALB.png)

5. **Creating a Launch Template**
![Creating a Launch Template](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/4_LaunchTemplate.png)

6. **Configuring the Auto Scaling Group**
![Configuring the Auto Scaling Group](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/4_AutoScaling_overview.png)
![Configuring the Auto Scaling Group Policy](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/4_AutoScaling_Policy.png)

7. **Use Stress cmd to stimulate whole Process**
![Use Stress cmd to stimulate whole Process](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/c230f0e58243a8b435a03b16706dca47bf92fef4/Screenshot%20(26).png)

8. **CloudWatch In Alarm State**
![CloudWatch In Alarm State](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/3_IN-ALARM-DASHBOARD.png)
![Graph-1-Metrics](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/3_IN-ALARM-Graph-in-Metrics.png)
![Graph-2-Graph](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/3_IN-ALARM-Graph.png)

9. **SNS Notifications**
![SNS Notifications](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/8_SNS.png)
![SNS Mail](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/5_Got_Alarm_mail.png)

10. **New Isntances add automatically**
![New Isntances add automatically](https://github.com/jaimin-vitthalpara/TestingJenkinsRepo/blob/3fd415b027f9e998c368018fb4ff493f15dbbacd/2_Five_ec2_add_after_alarming_activated.png)





