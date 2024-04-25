Here is my attempt at rewriting the technical documentation page to be more task-oriented and scenario-based:

Title: Control access to your Amazon EKS cluster's Kubernetes API server endpoint

Summary:
This page provides information on controlling access to the Kubernetes API server endpoint for an Amazon Elastic Kubernetes Service (EKS) cluster. By default, this endpoint is publicly accessible over the internet. The page describes how to:

- Enable private access so all communication between nodes and the API server stays within the cluster's VPC
- Limit the IP addresses that can access the public API server endpoint 
- Completely disable public internet access to the API server
- Access the API server when it is configured for private access only

The content assumes familiarity with Amazon EKS, Amazon VPC, Kubernetes, and kubectl. It also references AWS Identity and Access Management (IAM), Amazon Route 53, and AWS Cloud9.

Why a user might need this information:

A user would consult this page if they want to:

- Improve the security of their EKS cluster by restricting API server access
- Access the Kubernetes API privately from within the cluster VPC or connected networks
- Troubleshoot issues accessing the API server after changing endpoint access settings

Three potential task-oriented titles:

1. Secure your Amazon EKS cluster by restricting Kubernetes API server access

2. Access your cluster's private Kubernetes API server endpoint 

3. Configure public and private access to your EKS cluster's Kubernetes API server

Selected title: Configure public and private access to your EKS cluster's Kubernetes API server

Rewritten content:

# Configure public and private access to your EKS cluster's Kubernetes API server

When you create an Amazon EKS cluster, a public endpoint is provisioned by default to access the Kubernetes API server. You can communicate with your cluster through this endpoint using tools like `kubectl`. 

To improve cluster security, you may want to restrict access to the API server endpoint. With Amazon EKS, you can:

- Enable private access - All traffic between cluster nodes and the API server stays within your cluster's VPC. The API server is assigned a private IP address.

- Restrict public access - Specify a allowlist of IP address ranges in CIDR block format that can access the public API server endpoint. All other IP addresses are denied.

- Disable public access - Completely turn off public access to the API server. It can then only be accessed privately from within the cluster VPC. 

You can define these access settings when creating a new EKS cluster or update them for an existing cluster at any time.

## Enable private access to your cluster's API server

Follow these steps to enable private access to your cluster's API server endpoint:

1. Open the Amazon EKS console and select your cluster. 

2. Go to the Networking tab and click Update.

3. Under Private Access, select Enable. This provisions a private VPC endpoint the API server in your cluster VPC. 

4. Click Update to apply the changes.

After enabling private access, `kubectl` commands and Kubernetes API requests from within the cluster VPC will use the private endpoint. 

Note that Amazon EKS automatically creates and manages a Route 53 private hosted zone for the private endpoint. Ensure your VPC has `enableDnsHostnames` and `enableDnsSupport` enabled for private endpoint DNS resolution.

## Restrict public access to your cluster's API server 

To improve security, you can restrict what IP addresses are allowed to access your cluster's public API server endpoint:

1. Open the Amazon EKS console and select your cluster.

2. Go to the Networking tab and click Update. 

3. Ensure Public access is Enabled.

4. Expand Advanced settings. Enter the allowlisted CIDR blocks for public endpoint access. Use Add source to specify multiple ranges.

5. Click Update to apply the changes.

After updating, only the specified IP ranges will be able to access the public API server endpoint. Requests from all other IP addresses will be denied.

It's recommended to also enable private access when restricting public endpoint access. This ensures cluster nodes and Fargate pods can still communicate with the API server.

## Disable public access to your cluster's API server

For maximum security, you can completely disable public access to the API server. Kubernetes workloads and `kubectl` commands can then only reach the API server from within the cluster VPC:  

1. Open the Amazon EKS console and select your cluster.

2. Go to the Networking tab and click Update.

3. Under Public access, select Disabled. 

4. Ensure Private access is enabled. Disabling public access requires private access to be turned on.

5. Click Update to apply the changes.

After disabling public access, the Kubernetes API server can only be reached through the private VPC endpoint. 

## Access your cluster's private API server endpoint

With public access disabled, you can use the following methods to privately connect to the API server:

- AWS VPN or Direct Connect - Access the private endpoint through a VPN or Direct Connect connection between your network and the cluster VPC.

- Bastion host - Deploy a bastion EC2 instance in the cluster VPC and SSH to it in order to run `kubectl` commands. 

- AWS Cloud9 IDE - Create an AWS Cloud9 environment in the cluster VPC to use as a dashboard for managing the private cluster.

Ensure the security groups for the above resources allow inbound port 443 access from the EKS control plane. Also configure `kubectl` on the instances to use IAM principals mapped to your cluster RBAC configuration.

By enabling private access, restricting public access, or disabling public access altogether, you can secure the Kubernetes API server and ensure it is only reachable from authorized networks and instances. This helps to protect your Amazon EKS cluster from unauthorized access or potential attacks.