Here is my attempt at rewriting the AWS EKS private cluster documentation to be more task-oriented and follow web writing best practices:

Title: Set up a private Amazon EKS cluster without outbound internet access

Summary:
This page explains how to deploy an Amazon Elastic Kubernetes Service (EKS) cluster on AWS that does not have outbound internet access. It covers the requirements and considerations for configuring networking, pulling container images from a private registry, enabling private API server access, launching self-managed worker nodes, configuring IAM, and using AWS and Kubernetes services in a private cluster setup.

Why a user needs this information: 
A user would need to set up an EKS private cluster if they have strict security and compliance requirements that prevent worker nodes and pods from accessing the internet. The user needs to understand the implications and additional setup required to run an EKS cluster and workloads in an isolated private network environment.

Task-oriented titles:
1. Deploy an isolated Amazon EKS cluster with no internet access 
2. Run Kubernetes workloads in an Amazon EKS cluster with private networking
3. Configure an Amazon EKS private cluster to operate without outbound internet

Selected title: Deploy an isolated Amazon EKS cluster with no internet access

Rewritten content:

# Deploy an isolated Amazon EKS cluster with no internet access

Use this guide to set up an Amazon EKS cluster in a private network configuration with no outbound internet access. Deploying a fully private EKS cluster involves special considerations and setup:

## Pull images from a private container registry
- Create an Amazon ECR registry in your VPC 
- Copy required container images from public registries to your private ECR registry
- Configure nodes to pull images from the private registry

## Enable private access to the EKS API server 
- Turn on private access to the EKS cluster endpoint (public access optional)
- Specify the cluster endpoint and certificate when launching self-managed worker nodes

## Configure worker nodes to join the cluster
- Linux nodes: Pass `--apiserver-endpoint` and `--b64-cluster-ca` bootstrap arguments 
- Windows nodes: Pass `-APIServerEndpoint` and `-Base64ClusterCA` bootstrap arguments
- Use a VPC-internal mechanism to create the EKS `aws-auth` ConfigMap 

## Allow pods to assume IAM roles
- Create an AWS STS VPC endpoint in the cluster VPC
- Configure the AWS SDK to use the regional STS endpoint (`sts.region-code.amazonaws.com`)

## Access other AWS services 
- Create VPC interface endpoints for any AWS services the cluster needs (see table)
- Ensure worker node subnets can access the required VPC endpoints
- Adjust third-party CSI drivers and controllers to work in the private environment

With the proper setup, you can run an EKS cluster and Kubernetes workloads in an isolated VPC with no internet access. This allows you to meet strict security requirements while still leveraging managed Kubernetes on AWS. Refer to the considerations section for additional configuration that may be needed depending on your use case and tooling.