# Production-Style VPC Network on AWS
![AWS VPC Architecture](production-vpc-network
/Production-Style AWS VPC Architecture.png)
## 1. Project Overview

This project demonstrates the design and implementation of a production-style network infrastructure on Amazon Web Services (AWS).

The infrastructure uses a custom Amazon Virtual Private Cloud (VPC) to host a web application across two Availability Zones. An internet-facing Application Load Balancer distributes incoming HTTP traffic between two private EC2 application servers.

The application servers do not have public IPv4 addresses. Administrative access is provided through AWS Systems Manager Session Manager.

The infrastructure was built using both the AWS Management Console and AWS CLI.

### Project Objectives

- Design a custom VPC with a structured IP addressing scheme.
- Create public and private subnets across two Availability Zones.
- Configure Internet Gateway and NAT Gateway connectivity.
- Implement network security using Security Groups and Network ACLs.
- Deploy private EC2 application servers.
- Configure Apache HTTP Server on both instances.
- Deploy an Application Load Balancer.
- Configure target groups and health checks.
- Verify application access and traffic distribution.
- Practise AWS resource management using the Console and CLI.

## 2. AWS Region and Network Design

**AWS Region:** `eu-north-1` (Europe/Stockholm)

| Component | Configuration |
|---|---|
| VPC | Production-VPC |
| VPC CIDR | `10.0.0.0/16` |
| Availability Zones | `eu-north-1a`, `eu-north-1b` |
| Public subnets | 2 |
| Private subnets | 2 |
| Internet Gateway | Production-IGW |
| NAT Gateway | Production-NAT |
| Load Balancer | Production-ALB |
| Target Group | Production-TG |
| Application servers | 2 EC2 instances |

### Subnet Allocation

| Subnet | CIDR | Availability Zone |
|---|---|---|
| Public-A | `10.0.1.0/24` | `eu-north-1a` |
| Public-B | `10.0.2.0/24` | `eu-north-1b` |
| Private-A | `10.0.3.0/24` | `eu-north-1a` |
| Private-B | `10.0.4.0/24` | `eu-north-1b` |

## 3. Architecture Overview

The infrastructure separates internet-facing resources from private application resources.

### Traffic Flow

1. Internet users send HTTP requests to the public Application Load Balancer.
2. The ALB receives traffic through the public subnets.
3. The ALB forwards requests to healthy EC2 instances registered in the target group.
4. The application servers return their webpages through the ALB.
5. The NAT Gateway provides outbound internet connectivity to private resources.

### High-Level Architecture

```text
                         INTERNET
                             |
                      Internet Gateway
                             |
                 +-----------+-----------+
                 |                       |
              Public-A                Public-B
             10.0.1.0/24             10.0.2.0/24
                 |                       |
                 +-----------+-----------+
                             |
                      Production-ALB
                             |
                       Production-TG
                             |
                 +-----------+-----------+
                 |                       |
              Private-A               Private-B
             10.0.3.0/24             10.0.4.0/24
                 |                       |
              AZ-A EC2                AZ-B EC2
              10.0.3.98               10.0.4.197
              Apache                  Apache
```

Administrative access to both EC2 instances is provided through AWS Systems Manager Session Manager without requiring public IP addresses or inbound SSH access.

## 4. Implementation Status

- [x] VPC creation
- [x] Public and private subnet creation
- [x] Internet Gateway and route table configuration
- [x] Security Groups and Network ACL configuration
- [x] NAT Gateway and private routing
- [x] EC2 application server deployment
- [x] Systems Manager Session Manager access
- [x] Apache installation and configuration
- [x] Application Load Balancer creation
- [x] Target group registration and health checks
- [x] Public application access testing
- [x] Traffic delivery to both application servers

## 5. Application Testing

Both application servers host different webpages to make it possible to identify which instance responds to a request.

**AZ-A response:**

```html
<h1>Hello from AZ-A - Private Application Server</h1>
```

**AZ-B response:**

```html
<h1>Hello from AZ-B - Private Application Server</h1>
```

Both targets reported healthy in the target group. Testing through the ALB's public DNS name successfully returned webpages from both AZ-A and AZ-B.

## 6. Tools and AWS Services

- Amazon VPC
- Amazon EC2
- Application Load Balancer
- NAT Gateway
- Internet Gateway
- AWS Systems Manager
- AWS IAM
- Security Groups
- Network ACLs
- AWS Management Console
- AWS CLI
- Apache HTTP Server
- Linux (Amazon Linux)

## 7. Cost Management

The NAT Gateway and associated Elastic IP were created for this practical lab. These resources can incur charges while allocated or running.

They should be reviewed and cleaned up when the project is no longer in use.

---

**Project status:** Core network and load-balancing implementation completed. CLI verification, additional documentation and resource cleanup remain planned.
