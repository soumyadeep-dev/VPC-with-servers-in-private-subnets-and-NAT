# AWS VPC Deployment Guide

## Architectural Overview

Explore the illustration below for an insightful depiction of the resources encapsulated in this exemplary AWS VPC configuration. The VPC intricately integrates public and private subnets, strategically distributed across two Availability Zones. Noteworthy components include NAT gateways, load balancer nodes, and servers governed by an Auto Scaling group. Connectivity is established through NAT gateways to the internet and a dedicated gateway VPC endpoint facilitates seamless access to Amazon S3.

## Routing Configuration

Routing tables are meticulously designed for both public and private subnets. The public subnet routing table orchestrates local and internet gateway routes, while the private subnet table intricately weaves routes to the NAT gateway, egress-only internet gateway, and the gateway VPC endpoint.

**Routing Details:**

*Public Subnet Route Table:*

| Destination            | Target   |
| -----------------------|----------|
| 10.0.0.0/16            | local    |
| 2001:db8:1234:1a00::/56 | local    |
| 0.0.0.0/0              | igw-id   |
| ::/0                   | igw-id   |

*Private Subnet Route Table:*

| Destination            | Target           |
| -----------------------|------------------|
| 10.0.0.0/16            | local            |
| 2001:db8:1234:1a00::/56 | local            |
| 0.0.0.0/0              | nat-gateway-id   |
| ::/0                   | eigw-id          |
| s3-prefix-list-id      | s3-gateway-id    |

## Security Measures

A comprehensive security group blueprint is presented for servers, emphasizing unrestricted communication from the load balancer on the listener port and facilitating health check traffic.

**Inbound Regulations:**

| Source                              | Protocol                | Port Range            | Comments                                           |
| ------------------------------------|-------------------------|-----------------------|----------------------------------------------------|
| ID of the load balancer security group | listener protocol      | listener port         | Allows inbound traffic from the load balancer      |
| ID of the load balancer security group | health check protocol   | health check port      | Allows inbound health check traffic from the load balancer |

## VPC Establishment

Follow these procedural steps to fashion the VPC using the [Amazon VPC console](https://console.aws.amazon.com/vpc/):

1. Initiate the Amazon VPC console.
2. Opt for "Create VPC" on the dashboard.
3. Tailor the VPC with a designated name, IPv4 CIDR block, and optionally an IPv6 CIDR block.
4. Fine-tune subnets, selecting 2 Availability Zones with 2 public and 2 private subnets.
5. Configure NAT gateways, egress-only internet gateways, and VPC endpoints as per requirements.
6. Tailor DNS options to align with your preferences.
7. Conclude by initiating the creation of the VPC.

## Application Deployment

1. Rigorously test your servers within a development or test environment.
2. Leverage Amazon EC2 Auto Scaling to deploy servers, ensuring distribution across multiple Availability Zones.
3. Craft a launch template, encapsulating the configuration specifics for EC2 instances.
4. Establish an Auto Scaling group, incorporating minimum, maximum, and desired sizes.
5. Forge a load balancer, seamlessly tethering it to the Auto Scaling group.

## Configuration Validation

Post-application deployment, validate your configuration through a meticulous testing phase. Should your application exhibit unexpected traffic issues, leverage the Reachability Analyzer for astute troubleshooting insights. For a detailed guide, consult the [Reachability Analyzer Guide](#).
