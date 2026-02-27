# CENTRALIZED METRICS AND LOG MONITORING ACROSS PUBLIC AND PRIVATE INSTANCES
This documentation outlines the deployment of a segmented cloud network using public and private subnets, enabling controlled internet access through NAT routing. It highlights the implementation of centralized monitoring to provide visibility into system performance, resource utilization, and operational health across infrastructure hosted on AWS. By integrating observability using Datadog, the project demonstrates how isolated resources can maintain outbound connectivity while remaining protected from direct internet exposure.

<p align="center"> <img width="900" height="600" alt="Screenshot 2026-02-26 213758" src="https://github.com/user-attachments/assets/d80f5764-fe6d-44cb-9757-1904a6bbf193" />


## Table Of Contents
1. [Introduction](#introduction)
2. [Prerequisites and Services Used](#prerequisites-and-services-used)
3. [AWS VPC Configuration](#aws-vpc-configuration)
4. [Internet Gateway Setup and Routing](#internet-gateway-setup-and-routing)
5. [NAT Gateway SetUp and Routing](#nat-gateway-setup-and-routing)
6. [Configuration and Launching of Public and Private EC2 Instances](#configuration-and-launching-of-public-and-private-ec2-instances)
7. [Jump Host/ Bastion Connection](#jump-host-/-bastion-connection)
8. [Datadog Installation and Configuration](#datadog-installation-and-configuration)
9. [Alerting and Notification Integration](#alerting-and-notification-integration)
10. [Errors and Troubleshooting](#errors-and-troubleshooting)
11. [Conclusion](#conclusion)

## Introduction
This project demonstrates the configuration of a segmented cloud infrastructure with NAT-based internet access and centralized monitoring. By combining resources hosted on Amazon Web Services with Datadog observability and Slack/email alerting, the environment provides secure connectivity, real-time visibility, and coordinated response to infrastructure events.

## Prerequisites and Services Used
* Access to AWS console dashboard with an active subscription
* Designated Availabiltity Zone
* Amazon Virtual Private Cloud
* Internet Gateway
* NAT Gateway
* Public and Private EC2 Instances
* Amazon Linux
* Datadog Monitoring Agent
* Mail
* Slack

## AWS VPC Configuration
Configured a Virtual Private Cloud (VPC) in AWS, including subnet segmentation, route tables, security groups, and NACLs. Full Implementation here: https://github.com/Midas1045/AWS-Vpc/tree/main

<p align="center"> <img width="900" height="600" alt="Screenshot 2026-02-26 223701" src="https://github.com/user-attachments/assets/45f2f1e1-7dad-4e05-beae-f4c1c33a477e" />

## Internet Gateway Setup and Routing
Configured an Internet Gateway to enable inbound and outbound internet connectivity for resources deployed within the public subnet. Route tables were updated to direct external traffic through the Internet Gateway, ensuring controlled public access where required.

<p align="center"> <img width="1642" height="226" alt="Screenshot 2026-02-26 224957" src="https://github.com/user-attachments/assets/90b793a0-82b1-47de-984c-25d6f7afd324" />

## NAT Gateway Setup and Routing
Provisioned an NAT Gateway within the public subnet to provide secure outbound internet access for instances hosted in private subnets. Private route tables were configured to forward external-bound traffic through the NAT Gateway from the the public instance security groups, maintaining workload isolation while allowing updates, package installations, and monitoring communication without exposing private resources to direct inbound internet traffic.

<p align="center"> <img width="1645" height="512" alt="Screenshot 2026-02-26 225036" src="https://github.com/user-attachments/assets/b763c54c-3e5a-423c-ab8a-c0f12b142b0b" />

## Configuration and Launching of Public and Private EC2 Instances
Launched an EC2 instance in the public subnet with security groups having specified inbound and outbound rules for traffic from trusted sources, enabling direct management and public service hosting. A Private EC2 instance was launched in the private subnet with security groups restricting inbound access to the public subnet only , ensuring sensitive workloads remain protected while also connecting to the internet via the NAT Gateway for updates and installations. The same key pair was used for both instances to avoid authentication conflicts. 
* Documentation: https://github.com/Midas1045/AWS-Vpc#creating-and-launching-an-elastic-cloud-compute-on-the-frontend-subnet

<p align="center"> <img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/9fe43550-8fe1-4fd4-a933-aa2b9a7dacbf" />

<p align="center"> <img width="900" height="600" alt="image" src="https://github.com/user-attachments/assets/244f1f3f-f5f9-4153-a29c-87e0669e224b" />


## Jump Host/ Bastion Connection
Configured a Bastion Host within the public subnet to act as a secure access point for managing instances in the private subnet. This established SSH tunneling from a local machine through the Bastion Host to reach private EC2 instances, ensuring secure administrative operations without exposing private resources to the internet.
A. Steps
  * Locate the key pair  downloaded when creating your EC2 instance, which is typically found in the /mnt folder. Since file modifications are not permitted in that location, move the key pair to the home             directory of your local machine.
  * In the home directory, run the command "sudo chmod 400 projectvpc2.pem" to set the appropriate file permissions for your key pair, ensuring it is secure and accessible only by your user.
  * The next step is to securely copy the key pair/identity file (projectvpc2.pem) from the local machine to the home directory of ec2-user on the public EC2 instance. This is done by running the command "scp -i      projectvpc2.pem projectvpc2.pem ec2-user@34.205.45.137:~ ".
  * After completing the file transfer, establish an SSH connection from your local machine to the public EC2 instance using the command " ssh -i "projectvpc2.pem" ec2-user@34.205.45.137 ".
  * Run the "sudo apt update" and "sudo apt upgrade" command to to refresh package lists and upgrade system dependencies to their latest versions.
  * The next step is to establish an SSH connection from the public instance to the private instance and update the system dependencies accordingly.

<p align="center"> <img width="800" height="400" alt="Screenshot 2026-02-27 024002" src="https://github.com/user-attachments/assets/5385c81c-8c17-4c16-8036-1b8e2c69cd38" />

## Datadog Installation and Configuration
Configured monitoring agents from Datadog on both public and private EC2 instances to enable centralized system visibility and performance tracking. API keys and configuration files were applied to ensure secure data transmission, while dashboards and alerting policies were set up to monitor infrastructure health, resource utilization, and network activity across the environment.
* Documentation: 
  https://github.com/Midas1045/SSH-hardening-and-System-monitoring/edit/main/README.md#datadog-installation-and-configuration

<p align="center"> <img width="1000" height="700" alt="image" src="https://github.com/user-attachments/assets/5978be27-8759-4123-823d-55a46da7fc27" />
<p align="center"> <img width="1000" height="700" alt="image" src="https://github.com/user-attachments/assets/33d713b5-87f7-4409-8daa-b664276d6622" />


## Alerting and Notification Integration
Notification alerts were configured to be delivered via email and integrated with Slack to enable real-time communication and rapid incident response. Alerting policies were customized based on predefined thresholds for CPU utilization, memory usage, disk performance, and network activity, ensuring that critical events are promptly escalated to the appropriate channels. This setup enhanced operational visibility, improved response times, and ensured continuous monitoring across both public and private instances.

<p align="center"> <img width="950" height="769" alt="Screenshot 2026-02-27 122950" src="https://github.com/user-attachments/assets/d2912d88-1c62-4986-8d1f-7524f4b49a55" />

## Errors and Troubleshooting


## Conclusion
In conclusion, this project demonstrates the effective design of a secure and scalable cloud infrastructure within Amazon Web Services, combining segmented networking with controlled internet access through NAT-based routing to protect private resources while maintaining required connectivity. Centralized monitoring using Datadog provides continuous visibility into system performance and health, while integrated alerting workflows through Slack and email enable timely response to infrastructure events. Together, these components illustrate how multi-environment workloads can be secured, monitored, and managed efficiently, reinforcing best practices for modern cloud operations and proactive infrastructure management.


