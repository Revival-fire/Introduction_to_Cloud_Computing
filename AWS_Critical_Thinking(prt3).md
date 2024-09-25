# AWS Critical Thinking Projects

To accomplish this infrastructure creation and deployment, we will leverage AWS services like EC2, ALB (Application Load Balancer), Auto Scaling, CloudWatch, and related automation. Below are the detailed steps for each phase:

### 1. **Infrastructure Creation**:
We will create the following resources:
- **VPC**: A Virtual Private Cloud with subnets spread across multiple Availability Zones.
- **EC2 Instances**: These will host the Node.js application.
- **Application Load Balancer (ALB)**: To distribute traffic between instances.
- **Auto Scaling Group (ASG)**: To automatically scale instances based on CPU and memory usage.
- **CloudWatch Alarms**: For monitoring and triggering scaling actions.
- **Security Groups**: To control traffic.

#### a. **Step 1: Create a VPC**
1. Create a new VPC with CIDR (e.g., `10.0.0.0/16`).
2. Create subnets in at least **two availability zones** (e.g., `10.0.1.0/24` and `10.0.2.0/24`).
3. Create an **Internet Gateway** and attach it to the VPC for internet access.
4. Create **Route Tables** and associate subnets with the correct routes to the Internet Gateway.

#### b. **Step 2: Create an Application Load Balancer (ALB)**
1. Create an **ALB** in the VPC, selecting multiple subnets in different Availability Zones for high availability.
2. Configure **listeners** (e.g., HTTP on port 80) to forward traffic to the target group (which will be the Auto Scaling Group EC2 instances).
3. Ensure **Security Groups** allow HTTP/HTTPS access.

#### c. **Step 3: Create EC2 Instances**
1. Launch **EC2 instances** in the subnets, making sure they are in multiple Availability Zones.
2. Install the required software, including **Node.js** for running the app and **Nginx** as a reverse proxy if necessary.
3. Set up **security groups** allowing HTTP traffic from the ALB and SSH access for management.

### 2. **Deploy a Simple Node.js Application**:
1. SSH into the instances and install Node.js and Git:
   ```bash
   sudo yum update -y
   sudo yum install -y gcc-c++ make
   curl -sL https://rpm.nodesource.com/setup_14.x | sudo -E bash -
   sudo yum install -y nodejs git
   ```
2. Clone a simple Node.js app from a repository (or use your own):
   ```bash
   git clone https://github.com/heroku/node-js-sample.git
   cd node-js-sample
   npm install
   ```
3. Start the application:
   ```bash
   node index.js
   ```
4. Verify the application is running by accessing the EC2 instance's public IP or through the ALB.

### 3. **Load Testing**:
We will use a tool like **Apache JMeter** or **Artillery** to simulate a load on the Node.js app to test Auto Scaling behavior.

#### Using Artillery:
1. Install Artillery on your local machine or a test EC2 instance:
   ```bash
   npm install -g artillery
   ```
2. Run a load test:
   ```bash
   artillery quick --count 100 --num 10 http://<ALB-DNS-Name>
   ```

### 4. **Auto Scaling Configuration**:

#### a. **Step 1: Create an Auto Scaling Group**
1. Create an Auto Scaling Group (ASG) with the **EC2 Launch Template** using Amazon Linux or Ubuntu instances.
2. Specify the minimum and maximum number of instances (e.g., min: 1, max: 5) for the group.
3. Attach the ASG to the **ALB Target Group** for traffic distribution.

#### b. **Step 2: Set Up CloudWatch Alarms for CPU Usage**
1. Go to **CloudWatch**, create an alarm for the **Auto Scaling Group** based on CPU usage:
   - Set it to trigger when CPU usage exceeds **80%** for 2 consecutive periods of 1 minute.
   - Attach a **scale-out policy** to increase the number of instances by 1.
2. Create another alarm to scale-in when CPU usage drops below **20%**, reducing the number of instances.

#### c. **Step 3: Set Up CloudWatch Alarms for Memory Usage**
AWS doesn’t natively provide memory metrics, so you need to set up custom CloudWatch metrics for memory usage.
1. Install **CloudWatch Agent** on EC2 instances:
   ```bash
   sudo yum install amazon-cloudwatch-agent
   sudo vim /opt/aws/amazon-cloudwatch-agent/bin/config.json
   ```
2. Configure the agent to monitor memory and push metrics to CloudWatch.
3. Create a CloudWatch alarm based on memory usage (> 80%) and associate it with a **scale-out policy**.

### 5. **Ensure Scaling Down**:
1. Configure the **Auto Scaling Group** to scale down when the load decreases.
2. Set the scale-in condition (e.g., CPU below 20%) to decrease the number of instances.

### 6. **Cleaning Up Resources**:
After completing the deployment and testing, it is essential to delete all resources to avoid incurring charges.



