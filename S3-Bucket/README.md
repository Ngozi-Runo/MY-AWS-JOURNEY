Static Website Hosting on AWS
This guide provides detailed instructions for hosting static websites on AWS using two different approaches:

Amazon S3 (Simple Storage Service)
Amazon EC2 (Elastic Compute Cloud)
## Table of Contents
- Overview
- S3 Static Website Hosting
- EC2 Static Website Hosting
- Production-Level Configurations
- Sample Scenarios
- Project: Multi-Region Content Delivery Solution

## Overview
Static websites consist of HTML, CSS, JavaScript, images, and other client-side files that don't require server-side processing. AWS offers multiple ways to host these websites, each with its own advantages:

## S3 Advantages
- Serverless (no server management)
- Highly available and durable
- Cost-effective for static content
Scales automatically
Can be integrated with CloudFront for global distribution
EC2 Advantages
Full control over the web server
Can run dynamic content alongside static content
Customizable server configurations
Suitable for complex applications
Can be part of a larger architecture
S3 Static Website Hosting

### Step 1: Create an S3 Bucket
Sign in to the AWS Management Console
Navigate to the S3 service
Click ```Create bucket```
Enter a globally unique bucket name
Select the AWS Region closest to your users
Unblock all public access (for website hosting)
Enable bucket versioning (recommended for production)
Click ```Create bucket```

### Step 2: Configure Static Website Hosting

- Select your bucket from the S3 console
- Go to the "Properties" tab
Scroll down to "Static website hosting"
Click ```Edit```
- Select ```Enable```
- Set ```Index document``` to ***index.html***
- Set "Error document" to ***error.html***
Click ```Save changes```
- **NOTE** the "Bucket website endpoint" URL

## Step 3: Set Bucket Policy for Public Access
Go to the "Permissions" tab
Under "Bucket policy", click "Edit"
Add the following policy (replace your-bucket-name with your actual bucket name):
```bash
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
- Click ```Save changes```
## Step 4: Upload Website Files
Go to the "Objects" tab
Click "Upload"
Add your website files (HTML, CSS, JS, images, etc.)
Click ```Upload```

## Step 5: Access Your Website
Visit the S3 website endpoint URL (found in the "Properties" tab)
Format: ***http://your-bucket-name.s3-website-region.amazonaws.com***
EC2 Static Website Hosting
## Step 1: Launch an EC2 Instance
- Navigate to the EC2 console
- Click ```Launch instance```
- Choose an Amazon Linux 2 AMI
- Select an instance type (t2.micro is eligible for free tier)
- Configure instance details (use default settings for basic setup)
- Add storage (default is sufficient for a static website)
- Add tags (Name: StaticWebServer)
- Configure security group:
- Allow SSH (port 22) from your IP
- Allow HTTP (port 80) from anywhere
- Allow HTTPS (port 443) from anywhere
- Review and launch
- Create or select a key pair for SSH access
## Launch instance

- Step 2: Connect to Your Instance
- ssh -i /path/to/your-key.pem - ec2-user@your-instance-public-dns
Step 3: Install Web Server (Apache)
# Update system packages
sudo yum update -y

# Install Apache
sudo yum install -y httpd

# Start Apache and enable it to start on boot
sudo systemctl start httpd
sudo systemctl enable httpd

# Set permissions
- sudo usermod -a -G apache ec2-user
- sudo chown -R ec2-user:apache /var/www
- sudo chmod 2775 /var/www
- find /var/www -type d -exec sudo chmod 2775 {} \;
- find /var/www -type f -exec sudo chmod 0664 {} \;
Step 4: Upload Website Files
Option 1: Using SCP

scp -i /path/to/your-key.pem -r /path/to/local/website/* ec2-user@your-instance-public-dns:/var/www/html/
Option 2: Using Git

# Install Git
sudo yum install -y git

# Navigate to web directory
cd /var/www/html

# Clone your website repository
sudo git clone https://github.com/yourusername/your-website-repo.git .
Option 3: Create files directly on the server

# Create index.html
sudo nano /var/www/html/index.html
# Paste your HTML content, save and exit (Ctrl+X, Y, Enter)
Step 5: Access Your Website
Visit your EC2 instance's public DNS or IP address in a web browser
Format: http://your-instance-public-dns or http://your-instance-public-ip
Production-Level Configurations
S3 Production Configuration
Custom Domain with Route 53

Register a domain in Route 53 or use an existing domain
Create a hosted zone for your domain
Create an alias record pointing to your S3 website endpoint
CloudFront Distribution

Create a CloudFront distribution with your S3 bucket as the origin
Enable HTTPS with an ACM certificate
Configure caching behaviors
Set up geo-restrictions if needed
Enable logging
S3 Bucket Configuration

Enable versioning
Configure lifecycle rules
Set up server access logging
Implement CORS if needed
Enable default encryption
Security Best Practices

Use bucket policies to restrict access
Implement AWS WAF with CloudFront
Set up CloudTrail for API logging
Use S3 Object Lock for critical files
EC2 Production Configuration
High Availability Setup

Use Auto Scaling Group with multiple instances
Deploy across multiple Availability Zones
Set up an Application Load Balancer
HTTPS Configuration

Obtain an SSL certificate (using AWS Certificate Manager)
Configure Apache/Nginx for HTTPS
Redirect HTTP to HTTPS
Implement proper SSL security headers
Performance Optimization

Configure caching headers
Enable gzip compression
Use CloudFront as a CDN
Optimize instance type based on traffic
Security Hardening

Use security groups and NACLs
Implement AWS WAF
Regular security patches
Use IMDSv2 for instance metadata
Implement least privilege IAM roles
Monitoring and Logging

Set up CloudWatch alarms
Configure detailed monitoring
Enable access logs
Set up log rotation
Implement health checks
Sample Scenarios
Scenario 1: Corporate Marketing Website
Setup: S3 + CloudFront

High-traffic marketing website with global audience
Frequent content updates by marketing team
Need for fast global content delivery
Cost optimization for static content
Scenario 2: E-commerce Product Catalog
Setup: EC2 + CloudFront

Dynamic product search functionality
Static product images and descriptions
Need for server-side processing for search
High availability requirements during sales events
Scenario 3: Software Documentation Portal
Setup: S3 for documentation + EC2 for API reference

Versioned documentation in S3
Interactive API explorer on EC2
Global developer audience
Frequent documentation updates
Scenario 4: Event Microsite
Setup: S3 only

Temporary website for specific event
Simple design with registration form
Cost-effective solution for short-term use
Easy deployment and teardown
Scenario 5: Internal Knowledge Base
Setup: EC2 behind VPC

Company internal documentation
Access restricted to corporate network
Integration with internal authentication
Mix of static content and search functionality
Project: Multi-Region Content Delivery Solution
Business Scenario
TravelExplore, a global travel agency, needs to deliver their content-rich travel guides to users worldwide with minimal latency. The website includes:

High-resolution destination images
Interactive maps
Travel guides in PDF format
User reviews and ratings
Booking information
Requirements
Fast content delivery to users in North America, Europe, and Asia
Cost-effective solution for storing and serving large media files
Ability to update content frequently
High availability and disaster recovery capabilities
Secure access to administrative functions
Solution Architecture
Implement a hybrid approach using both S3 and EC2:

S3 for Static Content

Store images, PDFs, and other static assets
Configure as a static website for direct access
Implement multi-region replication for disaster recovery
EC2 for Dynamic Features

Host the main website application
Implement user authentication
Provide admin interface for content management
Connect to backend services and databases
CloudFront for Global Distribution

Distribute content from both S3 and EC2 origins
Configure geo-based routing
Implement caching strategies
Route 53 for DNS Management

Set up health checks and failover routing
Implement latency-based routing
Implementation Tasks
Set up S3 buckets in multiple regions
Configure EC2 instances with Auto Scaling
Implement CloudFront distribution
Set up Route 53 with appropriate routing policies
Configure monitoring and alerting
Implement backup and disaster recovery procedures
This project demonstrates how to effectively combine S3 and EC2 to deliver a comprehensive solution that leverages the strengths of each service.