Here are the installation instructions specifically for Ubuntu 20.04:

## Install kubectl on Ubuntu 20.04

```bash
# Update package index
sudo apt-get update

# Install required packages
sudo apt-get install -y apt-transport-https ca-certificates curl

# Download Google Cloud public signing key
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg

# Add Kubernetes apt repository
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list

# Update apt package index with new repository
sudo apt-get update

# Install kubectl
sudo apt-get install -y kubectl

# Verify installation
kubectl version --client
```

## Install helm on Ubuntu 20.04

```bash
# Download and install Helm
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /usr/share/keyrings/helm.gpg > /dev/null

# Add Helm repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm.list

# Update package index and install Helm
sudo apt-get update
sudo apt-get install helm

# Verify installation
helm version
```

## Install istioctl (version 1.15.0) on Ubuntu 20.04

```bash
# Download istioctl 1.15.0 for Linux
curl -L https://github.com/istio/istio/releases/download/1.15.0/istioctl-1.15.0-linux-amd64.tar.gz -o istioctl.tar.gz

# Extract the tar.gz file
tar -xzf istioctl.tar.gz

# Move istioctl to /usr/local/bin
sudo mv istioctl /usr/local/bin/

# Clean up
rm istioctl.tar.gz

# Verify installation
istioctl version
```

## Alternative: All-in-one script for Ubuntu 20.04

```bash
#!/bin/bash

# Update system
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

# Install kubectl
curl -fsSL https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-archive-keyring.gpg
echo "deb [signed-by=/etc/apt/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt-get update
sudo apt-get install -y kubectl

# Install Helm
curl https://baltocdn.com/helm/signing.asc | gpg --dearmor | sudo tee /etc/apt/keyrings/helm.gpg > /dev/null
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/helm.gpg] https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm.list
sudo apt-get update
sudo apt-get install -y helm

# Install istioctl 1.15.0
curl -L https://github.com/istio/istio/releases/download/1.15.0/istioctl-1.15.0-linux-amd64.tar.gz | tar xz
sudo mv istioctl /usr/local/bin/
chmod +x /usr/local/bin/istioctl

# Verify installations
echo "=== kubectl version ==="
kubectl version --client --short
echo "=== helm version ==="
helm version --short
echo "=== istioctl version ==="
istioctl version --short
```

## Set executable permission and run the script:
```bash
# Make the script executable
chmod +x install-tools.sh

# Run the script
./install-tools.sh
```

## Verify all installations:
```bash
# Check kubectl
kubectl version --client

# Check helm
helm version

# Check istioctl (specific version)
istioctl version

# Also check if they're in PATH
which kubectl
which helm
which istioctl
```

The tools will now be installed and available in your terminal. The installations use the standard package management system (apt) for kubectl and helm, while istioctl is downloaded directly from the Istio GitHub releases to ensure you get the specific version 1.15.0.
