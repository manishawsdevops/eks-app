# Pre-requisites Installation Guide

This document contains the installation steps for all required tools for EKS application development.

## Prerequisites

- Ubuntu 24.04 (Noble) on WSL2
- sudo access
- Internet connectivity

## 1. AWS CLI Installation

### Method 1: Install AWS CLI v2 (Recommended)

#### Step 1: Download AWS CLI v2
```bash
# Download the AWS CLI v2 installer
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Install unzip if not already installed
sudo apt update
sudo apt install -y unzip
```

#### Step 2: Extract and Install
```bash
# Extract the installer
unzip awscliv2.zip

# Run the installer
sudo ./aws/install

# Clean up
rm -rf aws awscliv2.zip
```

#### Step 3: Verify Installation
```bash
aws --version
# Expected output: aws-cli/2.x.x Python/3.x.x Linux/x.x.x-microsoft-standard-WSL2 exe/x86_64.ubuntu.xx
```

### Method 2: Install via Package Manager (Alternative)

#### For Ubuntu/Debian:
```bash
# Update package list
sudo apt update

# Install AWS CLI v1 (older version)
sudo apt install -y awscli

# Verify installation
aws --version
```

#### For CentOS/RHEL/Amazon Linux:
```bash
# Install AWS CLI v1
sudo yum install -y awscli

# Or for newer versions
sudo dnf install -y awscli
```

### Method 3: Install via pip (Python Package Manager)

#### Step 1: Install Python and pip
```bash
# Install Python and pip
sudo apt update
sudo apt install -y python3 python3-pip
```

#### Step 2: Install AWS CLI
```bash
# Install AWS CLI via pip
pip3 install awscli --user

# Add to PATH (add to ~/.bashrc for permanent)
echo 'export PATH=$PATH:~/.local/bin' >> ~/.bashrc
source ~/.bashrc
```

#### Step 3: Verify Installation
```bash
aws --version
```

### AWS CLI Configuration

#### Step 1: Configure AWS Credentials
```bash
# Configure AWS credentials
aws configure

# You will be prompted for:
# AWS Access Key ID: [Your Access Key]
# AWS Secret Access Key: [Your Secret Key]
# Default region name: us-east-1
# Default output format: json
```

#### Step 2: Set Environment Variables (Optional)
```bash
# Set AWS region (required for eksctl)
export AWS_DEFAULT_REGION=us-east-1

# Set AWS profile (if using multiple profiles)
export AWS_PROFILE=default

# To make permanent, add to ~/.bashrc
echo 'export AWS_DEFAULT_REGION=us-east-1' >> ~/.bashrc
echo 'export AWS_PROFILE=default' >> ~/.bashrc
source ~/.bashrc
```

#### Step 3: Verify Configuration
```bash
# Check AWS configuration
aws configure list

# Test AWS connection
aws sts get-caller-identity
```

### AWS CLI Troubleshooting

#### Issue: Command not found
```bash
# If aws command is not found, check PATH
which aws
echo $PATH

# Add AWS CLI to PATH if needed
export PATH=$PATH:/usr/local/bin/aws
```

#### Issue: Permission denied
```bash
# If permission denied, check file permissions
ls -la /usr/local/bin/aws

# Fix permissions if needed
sudo chmod +x /usr/local/bin/aws
```

#### Issue: Credentials not found
```bash
# Check AWS credentials
aws configure list

# If no credentials, run aws configure
aws configure
```

### AWS CLI Profiles

#### Create Multiple Profiles
```bash
# Create a new profile
aws configure --profile myprofile

# Use a specific profile
aws s3 ls --profile myprofile

# Set default profile
export AWS_PROFILE=myprofile
```

#### List All Profiles
```bash
# List all configured profiles
aws configure list-profiles
```

### Current Environment Status
```bash
# Check current version (from your environment)
aws --version
# Output: aws-cli/2.31.9 Python/3.13.7 Linux/6.6.87.2-microsoft-standard-WSL2 exe/x86_64.ubuntu.24
```

**Note:** AWS CLI v2.31.9 was already installed in this environment.

## 2. kubectl Installation

### Step 1: Install Required Packages
```bash
sudo apt update
sudo apt install -y apt-transport-https ca-certificates curl
```

### Step 2: Add Kubernetes Repository
```bash
# Download and add the Kubernetes signing key
sudo curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.30/deb/Release.key | sudo gpg --dearmor -o /etc/apt/trusted.gpg.d/kubernetes-apt-keyring.gpg

# Add the Kubernetes repository
echo "deb [signed-by=/etc/apt/trusted.gpg.d/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.30/deb/ /" | sudo tee /etc/apt/sources.list.d/kubernetes.list
```

### Step 3: Update Package List and Install kubectl
```bash
sudo apt update
sudo apt install -y kubectl
```

### Step 4: Verify Installation
```bash
kubectl version --client
# Output: Client Version: v1.30.14
#         Kustomize Version: v5.0.4-0.20230601165947-6ce0bf390ce3
```

## 3. Helm Installation

### Method 1: Direct Download (Recommended)
```bash
# Download Helm binary
curl -fsSL https://get.helm.sh/helm-v3.15.2-linux-amd64.tar.gz -o helm.tar.gz

# Extract the archive
tar -zxvf helm.tar.gz

# Move helm binary to system PATH
sudo mv linux-amd64/helm /usr/local/bin/helm

# Clean up
rm -rf linux-amd64 helm.tar.gz
```

### Method 2: Package Manager (Alternative)
```bash
# Add Helm repository (if network allows)
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list

# Update and install
sudo apt update
sudo apt install -y helm
```

### Verify Helm Installation
```bash
helm version
# Output: version.BuildInfo{Version:"v3.15.2", GitCommit:"1a500d5625419a524fdae4b33de351cc4f58ec35", GitTreeState:"clean", GoVersion:"go1.22.4"}
```

## 4. eksctl Installation

### Download and Install eksctl
```bash
# Download the latest eksctl binary
curl -sLO "https://github.com/eksctl-io/eksctl/releases/latest/download/eksctl_Linux_amd64.tar.gz"

# Extract the archive
tar -xzf eksctl_Linux_amd64.tar.gz

# Move eksctl to system PATH
sudo mv eksctl /usr/local/bin/

# Clean up
rm eksctl_Linux_amd64.tar.gz
```

### Verify eksctl Installation
```bash
eksctl version
# Output: 0.215.0
```

## 5. Environment Setup

### Set AWS Region
```bash
# Set AWS region for eksctl
export AWS_DEFAULT_REGION=us-east-1

# To make it permanent, add to ~/.bashrc or ~/.profile
echo 'export AWS_DEFAULT_REGION=us-east-1' >> ~/.bashrc
source ~/.bashrc
```

### Verify All Tools
```bash
# Check all installed tools
aws --version
kubectl version --client
helm version
eksctl version
```

## 6. Common Issues and Solutions

### Issue: kubectl connection refused
```bash
kubectl version
# Error: The connection to the server localhost:8080 was refused
```
**Solution:** This is normal when no Kubernetes cluster is configured. Configure your kubeconfig after creating an EKS cluster.

### Issue: eksctl AWS Region error
```bash
eksctl get cluster
# Error: AWS Region must be set
```
**Solution:** Set the AWS region as shown in step 5.

### Issue: Helm repository connection issues
If the Helm repository is not accessible, use the direct download method (Method 1) as shown above.

## 7. Next Steps

After completing these installations:

1. Configure AWS credentials (`aws configure`)
2. Create an EKS cluster using eksctl
3. Update kubeconfig to connect to your cluster
4. Deploy applications using kubectl and Helm

## 8. Tool Versions Installed

- **AWS CLI:** 2.31.9
- **kubectl:** v1.30.14
- **Helm:** v3.15.2
- **eksctl:** 0.215.0

## 9. Useful Commands

```bash
# Check kubectl configuration
kubectl config view

# List EKS clusters
eksctl get cluster

# List Helm repositories
helm repo list

# Check AWS configuration
aws configure list
```

---

**Installation Date:** October 8, 2025  
**Environment:** Ubuntu 24.04 (Noble) on WSL2  
**Documentation Created By:** Satish
