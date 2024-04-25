<geoffrey>
<document>
{{the .md cluster-endpoint page}}
</document>

Rewrite this page in the AWS Documentation to be oriented around a single procedure/task/scenario. Use the style of the AWS Docs. You are a tech writer experience with software engineering and cloud platforms.
</geoffrey>

<claude>
# Securing Access to the EKS API Server from a Bastion Host

This guide will walk you through the process of setting up secure access to your Amazon EKS cluster's Kubernetes API server using a bastion host in an Amazon EC2 instance. By following these steps, you will disable public access to the API server and enable private access from within your VPC.

## Prerequisites

Before you begin, ensure you have the following prerequisites:

- An existing Amazon EKS cluster
- `kubectl` installed on your local machine and configured to communicate with your cluster
- AWS CLI installed and configured with appropriate permissions

## Step 1: Launch an EC2 Instance as a Bastion Host

1. Open the Amazon EC2 console and click **Launch Instance**.
2. Choose an Amazon Machine Image (AMI) that suits your needs, such as an Amazon Linux 2 AMI.
3. Select an instance type and click **Next: Configure Instance Details**.
4. In the **Network** field, choose the VPC associated with your EKS cluster.
5. Choose a public subnet for your instance and enable **Auto-assign Public IP**.
6. Click **Review and Launch**, then **Launch** to start the instance.

## Step 2: Configure Security Group Rules

1. In the EC2 console, click on **Security Groups** in the left menu.
2. Find the security group associated with your EKS cluster's control plane and click **Edit inbound rules**.
3. Add a new rule to allow inbound traffic on port 443 from the security group of your bastion host instance.
4. Save the changes.

## Step 3: Connect to the Bastion Host and Configure `kubectl`

1. SSH into your bastion host instance.
2. Install `kubectl` on the instance.
3. Configure `kubectl` to communicate with your EKS cluster using the appropriate kubeconfig file.
   - Ensure that the AWS credentials used have the necessary IAM permissions and are mapped to your cluster's RBAC configuration.

## Step 4: Modify EKS Cluster Endpoint Access

1. Open the Amazon EKS console and select your cluster.
2. Go to the **Networking** tab and click **Update**.
3. For **Private access**, select **Enabled**.
4. For **Public access**, select **Disabled**.
5. Click **Update** to apply the changes.

Your EKS cluster's Kubernetes API server is now only accessible from within the VPC, and your bastion host can securely communicate with it.

## Step 5: Access the EKS Cluster from the Bastion Host

1. SSH into your bastion host instance.
2. Use `kubectl` commands to interact with your EKS cluster securely from within the VPC.

By following this guide, you have successfully set up a bastion host to securely access your Amazon EKS cluster's API server, while disabling public access to enhance the security of your cluster.
</claude>

<geoffrey>
can you give me an alternate example keeping closer to the content of the original page? include a bit more detail targeting sophisticated Kubernetes and AWS users.
</geoffrey>

<claude>
# Securing Access to the Amazon EKS API Server

This guide demonstrates how to enhance the security of your Amazon EKS cluster by configuring private access to the Kubernetes API server and restricting or disabling public access. You'll learn how to modify the cluster endpoint access settings and explore various methods to securely connect to the API server from within your VPC or connected networks.

## Prerequisites

- An existing Amazon EKS cluster
- AWS CLI version 1.27.160 or later
- `kubectl` configured to interact with your EKS cluster
- IAM user or role with necessary permissions to modify EKS cluster configuration

## Step 1: Update Cluster Endpoint Access Configuration

You can update the endpoint access configuration using the AWS Management Console or the AWS CLI.

### Using the AWS Management Console

1. Open the Amazon EKS console and select your cluster.
2. Go to the **Networking** tab and click **Update**.
3. For **Private access**, choose **Enabled** to allow communication between nodes and the API server within your VPC.
4. For **Public access**, choose **Enabled** or **Disabled** based on your requirements.
   - If enabled, you can optionally specify CIDR blocks to restrict access to the public endpoint.
   - If disabled, the API server will only accept requests from within the cluster VPC.
5. Click **Update** to apply the changes.

### Using the AWS CLI

1. Update the cluster endpoint access using the following command:

   ```
   aws eks update-cluster-config \
       --region <region-code> \
       --name <cluster-name> \
       --resources-vpc-config endpointPublicAccess=<true|false>,publicAccessCidrs="<cidr-blocks>",endpointPrivateAccess=<true|false>
   ```

   - Set `endpointPublicAccess` and `endpointPrivateAccess` to `true` or `false` based on your requirements.
   - If enabling public access, you can optionally specify a comma-separated list of CIDR blocks for `publicAccessCidrs` to restrict access to the public endpoint.

2. Monitor the status of the endpoint access update using the following command:

   ```
   aws eks describe-update \
       --region <region-code> \
       --name <cluster-name> \
       --update-id <update-id>
   ```

   The update is complete when the status shows as `Successful`.

## Step 2: Accessing the API Server Privately

If you have disabled public access to the API server, you can use one of the following methods to securely connect from within your VPC or a connected network:

### AWS PrivateLink

Establish a VPC endpoints for Amazon EKS using AWS PrivateLink. This allows you to privately access the EKS API server from your VPC without traversing the public internet.

### AWS Direct Connect or VPN

Use AWS Direct Connect or a VPN connection to extend your on-premises network to your VPC. This enables secure communication with the EKS API server from your local environment.

### Bastion Host

Launch an EC2 instance in a public subnet of your VPC to act as a bastion host. Configure the EKS control plane security group to allow inbound traffic on port 443 from the bastion host. Install `kubectl` on the bastion host and use it to interact with your cluster.

### AWS Cloud9

Create an AWS Cloud9 IDE within your VPC and use it to communicate with the EKS API server. Ensure the EKS control plane security group allows inbound traffic from the IDE.

## Conclusion

By configuring private access to the Amazon EKS API server and restricting public access, you can enhance the security posture of your cluster. Choose the appropriate method to securely connect to the API server based on your specific requirements and network architecture. Remember to manage IAM permissions and RBAC configurations carefully to ensure authorized access to your cluster.
</claude>