<claude>
Here is a summary of the content and suggested task-oriented titles for the "Amazon EKS cluster endpoint access control" documentation page:

Summary:
This page covers how to enable private access to an Amazon EKS cluster's Kubernetes API server endpoint and limit or disable public internet access. By default, the API server endpoint is public. Private access allows all communication between nodes and the API server to stay within the VPC. The page explains the behavior of different endpoint access configurations and provides instructions for modifying access using the AWS Management Console or AWS CLI. It also discusses options for accessing the API server when public access is fully disabled, such as using an AWS transit gateway, EC2 bastion host, or AWS Cloud9 IDE.

The page assumes knowledge of Amazon EKS, Amazon VPC networking concepts, kubectl, and Kubernetes RBAC.

Users may need this information to:
1. Restrict access to their Kubernetes API server for security and compliance 
2. Enable private communication between worker nodes/Fargate and the API server
3. Securely access the API server when public access is disabled

Suggested task-oriented titles:
1. Secure your Amazon EKS cluster's Kubernetes API server endpoint
2. Enable private access to your Amazon EKS API server within your VPC 
3. Lock down public access to your Amazon EKS cluster's API server

Let me know if you would like me to proceed with rewriting the page content using one of these titles and the scenario-based approach. I'm happy to do so.
</claude>

<geoffrey>
Rewrite it using the title "Restrict network access to the Kubernetes API server"
</geoffrey>

<claude>

Here is the technical documentation rewritten to be task-oriented using the title "Restrict network access to the Kubernetes API server":

# Restrict network access to the Kubernetes API server

When you create an Amazon EKS cluster, by default its Kubernetes API server endpoint is accessible from the public internet. You can configure the endpoint to only allow access from within your VPC for improved security. This topic shows you how to:

- Enable private access so nodes and Fargate pods can communicate with the API server using private IP addresses within your VPC 
- Limit which IP addresses can access the public endpoint
- Completely disable public internet access to the API server

Before you restrict access, ensure your VPC is configured properly:
- `enableDnsHostnames` and `enableDnsSupport` must be set to `true`
- VPC DHCP options must include `AmazonProvidedDNS` for its domain name servers 

When you enable private access, Amazon EKS creates and manages a Route 53 private hosted zone associated with your VPC to route API server traffic.

## Choose your network access configuration

The following table shows the supported access configurations and their behavior:

| Public Access | Private Access | Behavior |
| --- | --- | --- |
| Enabled | Disabled | The API server is accessible from the internet. Communication between nodes/Fargate and the server uses public IP addresses. | 
| Enabled with restrictions | Enabled | Requests to the public endpoint are only allowed from the specified IP ranges. Communication between nodes/Fargate and the server uses private IP addresses within the VPC. |
| Disabled | Enabled | The API server is only accessible from within the VPC. All requests must use private IP addresses. |

## Modify access for an existing cluster

Use the AWS Management Console or AWS CLI version 1.27.160 or later to update your cluster's endpoint access.

### Using the AWS Management Console
1. Open the Amazon EKS console and select your cluster.
2. Choose the **Networking** tab, then **Update**.
3. For **Private access**, choose enable or disable. Private access must be enabled to disable public access.
4. For **Public access**, choose enable or disable. 
5. If enabling public access, you can optionally specify CIDR blocks to restrict access under **Advanced settings**. Leave empty to allow access from all IP addresses.
6. Choose **Update**.

### Using the AWS CLI 
Update cluster endpoint access with this command, replacing values as needed:
```
aws eks update-cluster-config \
    --region <region-code> \    
    --name <my-cluster> \
    --resources-vpc-config endpointPublicAccess=<true|false>,publicAccessCidrs="<cidr-ranges>",endpointPrivateAccess=<true|false>
```
Monitor the update status with:
```
aws eks describe-update \
    --region <region-code> \
    --name <my-cluster> \
    --update-id <update-id>
```
The update is complete when status is `Successful`.

## Accessing the API server with public access disabled 

When public endpoint access is disabled, you must connect from within the VPC or a connected network. Options include:

- **AWS transit gateway or VPN** - Access the server from a network connected to the VPC. Ensure the EKS control plane security group allows port 443 ingress from the connected network.

- **EC2 bastion host** - Launch an EC2 instance in a public subnet and SSH to it to run `kubectl`. Ensure the control plane security group allows port 443 ingress from the bastion. 

- **AWS Cloud9 IDE** - Create an IDE environment in the VPC. Ensure the control plane security group allows port 443 ingress from the IDE.

When using AWS credentials to configure `kubectl`, grant permissions to the IAM principal in the cluster's RBAC configuration before removing public access.

</claude>