# AWS Critical Thinking Projects and Questions

## Scenario 1:

To design an infrastructure solution that ensures a good browsing experience for users in India and London with minimal latency, we need to consider a combination of AWS services, including Content Delivery Networks (CDNs), edge caching, global load balancing, and region-specific deployment strategies. Here’s a step-by-step approach to creating an optimal solution.

### Infrastructure Solution Design

#### 1. **Architecture Overview**
   - **Global Load Balancer**: Use AWS Global Accelerator to route user traffic to the nearest application endpoints based on geographic location, improving performance and availability.
   - **Content Delivery Network (CDN)**: Use Amazon CloudFront as a CDN to cache static and dynamic content at edge locations close to users in both India and London, reducing latency.
   - **Region-Specific Deployments**: Deploy your application in multiple AWS regions (e.g., Asia Pacific (Mumbai) and Europe (London)) to ensure that users in both locations can access the service with minimal latency.
   - **Edge Caching**: Utilize CloudFront edge caching to store frequently accessed content closer to users, further enhancing load times.

#### 2. **Components and Services**
   - **AWS Regions**: 
     - **Asia Pacific (Mumbai) Region** for users in India.
     - **EU (London) Region** for users in London.
   - **AWS Global Accelerator**: A service that optimizes the path to your application, improving latency and availability.
   - **Amazon CloudFront**: A global CDN that caches content at edge locations.
   - **Application Load Balancer (ALB)**: Distributes incoming application traffic across multiple targets, such as EC2 instances or Lambda functions, in both regions.
   - **Amazon S3**: For hosting static content (images, videos, etc.) that can be served through CloudFront.
   - **Amazon RDS/Aurora**: For database services, potentially using cross-region replication for availability and performance.

#### 3. **Architecture Diagram**
You can create a diagram using a tool like Draw.io to visualize the solution. Here’s how you can structure your diagram:

- **CloudFront** at the top, with arrows pointing to:
  - **Region-specific Load Balancers** (ALB) in Mumbai and London.
- **EC2 Instances** or **ECS Services** under each ALB, representing your application servers in both regions.
- **Amazon S3 Buckets** for static content, with connections to CloudFront.
- **Amazon RDS/Aurora** databases in both regions, with a connection line indicating replication or data sync between them.

### Implementation Steps in AWS Console

1. **Create AWS Accounts in Regions**:
   - Set up an account in the **Asia Pacific (Mumbai)** and **EU (London)** regions.

2. **Deploy Application**:
   - Launch **EC2 Instances** or set up **ECS Services** in both regions to host your application.

3. **Set Up Load Balancers**:
   - Create an **Application Load Balancer** in both regions.
   - Register your EC2 instances or ECS tasks as targets.

4. **Configure Amazon S3**:
   - Create an S3 bucket for storing static assets.
   - Configure bucket policies to allow CloudFront access.

5. **Set Up CloudFront**:
   - Create a new **CloudFront distribution**:
     - Set the origin to your S3 bucket for static content.
     - Set the origin to your ALB for dynamic content.
     - Configure caching behaviors as needed.

6. **Set Up AWS Global Accelerator**:
   - Create a new Global Accelerator and configure it to point to the ALBs in both regions.
   - This will allow users to access the application through a static IP, routing them to the nearest region.

7. **Configure DNS**:
   - Use Route 53 to create a record that points to your Global Accelerator, allowing for easy access.

8. **Monitoring and Logging**:
   - Enable CloudWatch monitoring for all services.
   - Set up logging in CloudFront and S3 to track access and performance metrics.
  
## Scenario 2:

To host a static website on Amazon S3, you’ll need to follow several key steps to configure the S3 bucket, upload your static files, set permissions, and enable static website hosting. Here’s a detailed breakdown of the process, including considerations for domain configuration and CDN integration.

### Steps to Host a Static Website on Amazon S3

#### 1. **Create an S3 Bucket**
   - Go to the **Amazon S3 Console**.
   - Click on **Create bucket**.
   - Enter a **unique bucket name** (the name must be globally unique).
   - Select the **AWS region** where you want to create the bucket.
   - Uncheck the "Block all public access" option to allow public access (you will set permissions later).
   - Click **Create bucket**.

#### 2. **Configure the S3 Bucket for Static Website Hosting**
   - Select the newly created bucket.
   - Go to the **Properties** tab.
   - Scroll down to **Static website hosting** and click **Edit**.
   - Select the **Enable** option.
   - Specify the **index document** (e.g., `index.html`) and, optionally, an **error document** (e.g., `error.html`).
   - Click **Save changes**.

#### 3. **Upload Static Website Files**
   - In the S3 bucket, click on the **Upload** button.
   - Drag and drop your static files (HTML, CSS, JS, images) into the upload area or use the **Add files** button.
   - Click **Upload** to finish the process.

#### 4. **Set Permissions**
   - Go to the **Permissions** tab of the S3 bucket.
   - Click on **Bucket Policy** and add a policy to allow public read access to your files. A typical policy would look like this:

     ```json
     {
       "Version": "2012-10-17",
       "Statement": [
         {
           "Sid": "PublicReadGetObject",
           "Effect": "Allow",
           "Principal": "*",
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::your-bucket-name/*"
         }
       ]
     }
     ```

   - Make sure to replace `your-bucket-name` with the actual name of your bucket.
   - Click **Save**.

#### 5. **Enable Static Website Hosting**
   - The static website URL can be found under the **Static website hosting** section in the **Properties** tab. This URL will serve your static content.

### Considerations for Domain Configuration and CDN Integration

#### **Domain Configuration**
   - If you want to use a custom domain, you will need to configure your domain’s DNS settings. You can use Amazon Route 53 or any other DNS provider to create a CNAME record pointing to your S3 bucket’s static website URL.

#### **CDN Integration**
   - For enhanced performance and faster content delivery, consider integrating Amazon CloudFront (a CDN) with your S3 bucket:
     - Create a CloudFront distribution and set the origin to your S3 bucket.
     - Configure cache behaviors, SSL certificates (for HTTPS), and any custom settings as required.
     - Update your domain’s DNS records to point to the CloudFront distribution.

### Architectural Diagram
Here’s a simplified architectural diagram for hosting a static website on S3:

```plaintext
       +-----------------+
       |   Internet      |
       +-----------------+
               |
               |
       +-----------------+
       |   CloudFront    | (Optional, for CDN)
       +-----------------+
               |
               |
       +-----------------+
       |   S3 Bucket     |  (Static Website Hosting)
       +-----------------+
               |
               |
       +-----------------+
       |   Users         |  (Access via URL)
       +-----------------+
```

### Hosting a Sample Static Website
You can use simple HTML files for your sample website. Here’s an example:

1. **Create `index.html`:**
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>My Static Website</title>
   </head>
   <body>
       <h1>Welcome to My Static Website!</h1>
       <p>This is a sample static website hosted on Amazon S3.</p>
   </body>
   </html>
   ```

2. **Create `error.html`:**
   ```html
   <!DOCTYPE html>
   <html lang="en">
   <head>
       <meta charset="UTF-8">
       <meta name="viewport" content="width=device-width, initial-scale=1.0">
       <title>Page Not Found</title>
   </head>
   <body>
       <h1>404 - Page Not Found</h1>
       <p>Sorry, the page you are looking for does not exist.</p>
   </body>
   </html>
   ```

3. **Upload the HTML files to your S3 bucket** as described in the steps above.

## Scenerio

Analyzing the existing on-premises infrastructure for legacy applications at Company ABC requires a thorough understanding of its architecture, identifying key challenges, and proposing a robust cloud-native solution. Below is a structured approach to this task.

### 1. Analysis of Existing On-Premises Infrastructure

#### Current Setup Challenges:
1. **Single Point of Failure**:
   - Existing infrastructure may rely on a limited number of servers or components, which means if one fails, it can take down the entire application.
  
2. **Lack of Scalability**:
   - On-premises systems often require significant manual effort and downtime to scale. Increasing resources or capacity usually involves procuring new hardware and configuring it.
  
3. **Security Vulnerabilities**:
   - Legacy applications might be running outdated software versions, leading to potential vulnerabilities. Additionally, the perimeter security model may not effectively protect against modern threats.
  
4. **Inefficient Resource Allocation**:
   - Resources may not be optimally utilized, leading to wasted capacity during low usage times. Traditional server setups often involve over-provisioning to handle peak loads.

### 2. Proposed Cloud-Native Architecture Solution

To address the identified challenges, a cloud-native architecture will be implemented with the following components:

- **Separation of Database and Application**: Migrate applications to the cloud while keeping the database on-premises, allowing for better performance management and security.
  
- **Cloud Services**: Use cloud services for application hosting (e.g., AWS ECS or EKS), leveraging the elasticity of cloud environments.
  
- **High Availability**: Implement redundancy and load balancing to eliminate single points of failure in application deployment.

- **Security Measures**: Integrate modern security practices such as micro-segmentation, encryption, and identity management.

- **Resource Optimization**: Use auto-scaling features to ensure efficient resource allocation, scaling up or down based on demand.

### 3. Architectural Diagram

Below is a textual representation of the proposed architectural diagram:

```
+---------------------+                       +---------------------+
| On-Premises         |                       | Cloud Infrastructure |
| Database            |                       | (AWS/Azure/GCP)     |
|                     |                       |                     |
|   +------------+    |                       |   +-------------+   |
|   | Legacy DB  |    | <--- Secure VPN ----> |   App Server  |   |
|   +------------+    |                       |   +-------------+   |
+---------------------+                       |                     |
                                              |   +-------------+   |
                                              |   | Load       |   |
                                              |   | Balancer   |   |
                                              |   +-------------+   |
                                              |                     |
                                              +---------------------+
```

### 4. Detailed Implementation Steps

#### Step 1: Assess Current Infrastructure
- **Inventory Hardware and Software**: Document all existing servers, applications, and databases.
- **Identify Dependencies**: Map out dependencies between legacy applications and databases.

#### Step 2: Design Cloud Architecture
- **Select Cloud Provider**: Choose a cloud provider based on company needs (e.g., AWS, Azure).
- **Design Application Architecture**: Use microservices architecture for better scalability and management.

#### Step 3: Implement Security Measures
- **VPN Setup**: Establish a secure VPN connection between the on-premises database and cloud applications.
- **Identity and Access Management**: Implement IAM roles and policies to control access.

#### Step 4: Migrate Applications
- **Containerization**: Containerize applications using Docker, making them cloud-ready.
- **CI/CD Pipeline**: Set up CI/CD pipelines using tools like Jenkins or GitLab for automated deployment.

#### Step 5: Set Up Load Balancing and High Availability
- **Configure Load Balancer**: Use a cloud load balancer to distribute traffic among multiple instances.
- **Redundancy**: Deploy multiple instances of the application in different availability zones.

#### Step 6: Optimize Resource Allocation
- **Auto-Scaling Groups**: Configure auto-scaling groups to adjust the number of running instances based on load.

#### Step 7: Monitor and Optimize
- **Monitoring Tools**: Implement monitoring tools (like CloudWatch or Prometheus) to track performance and resource utilization.
- **Regular Reviews**: Schedule regular reviews to assess performance and make adjustments as necessary.










