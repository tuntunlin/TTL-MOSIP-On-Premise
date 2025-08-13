# TTL-MOSIP-On-Premise

# MOSIP K8S Cluster Setup using RKE

### 1. Install Kubectl 
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
Follow these steps to install kubeadm on your system.

For Ubuntu/Debian:
Update the package list:

bash
Copy
sudo apt-get update
Install required dependencies:

bash
Copy
sudo apt-get install -y apt-transport-https ca-certificates curl
Add the Kubernetes signing key:

bash
Copy
sudo curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg
Add the Kubernetes APT repository:

bash
Copy
echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
Update the package list again:

bash
Copy
sudo apt-get update
Install kubeadm, kubectl, and kubelet:

bash
Copy
sudo apt-get install -y kubeadm kubelet kubectl
Prevent automatic updates (optional but recommended):

bash
Copy
sudo apt-mark hold kubeadm kubelet kubectl


### 2. Install Helm 
```sh
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh
```
### 3. Install Ansible 
```sh
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```
### 4. Set up [passwordless SSH] 
```sh
ssh-keygen -t rsa
ssh-copy-id -i ~/.ssh/id_rsa.pub wireguard@192.168.0.110
ssh-copy-id -i ~/.ssh/id_rsa.pub node1@192.168.0.111
ssh-copy-id -i ~/.ssh/id_rsa.pub node2@192.168.0.112
ssh-copy-id -i ~/.ssh/id_rsa.pub node3@192.168.0.113
ssh-copy-id -i ~/.ssh/id_rsa.pub node4@192.168.0.114
ssh-copy-id -i ~/.ssh/id_rsa.pub node5@192.168.0.115
ssh-copy-id -i ~/.ssh/id_rsa.pub node6@192.168.0.116
ssh-copy-id -i ~/.ssh/id_rsa.pub nginx@192.168.0.117
```
### 5. Wireguard bastion node 
```sh
sudo apt install ufw
```
```sh
ansible-playbook -i hosts.ini wireguard.yaml
```
### 6. Ports
* Install ufw on each of the nodes
```sh
sudo apt install ufw
```
* Open ports on each of the nodes.
```sh
ansible-playbook -i hosts.ini ports.yaml
```
### 7. Disable swap _(perhaps not needed as swap is already disabled)_.
* Install ufw on each of the nodes
```sh
sudo modprobe br_netfilter
```
```sh
ansible-playbook -i hosts.ini swap.yaml
```
### 8. Environment Check and setup
## NOTE: Not recommended for production or pilot
1. Performs a check to ensure that the localhost mapping is present on all nodes.
2. To remove cache and unused docker images on all the cluster nodes.
```sh
ansible-playbook -i hosts.ini env-check-setup.yaml
```
## Docker
Install docker on all nodes.
```sh
ansible-playbook -i hosts.ini docker.yaml
```
## RKE cluster setup
* Create a cluster config file. 
```sh
rke up --config cluster.yml
```
1. Install rke
To install rke, follow these steps:

Download rke:
Visit the RKE GitHub Releases page to find the latest version of rke.

Download the binary for your operating system. For example, on Linux:

bash
Copy
wget https://github.com/rancher/rke/releases/download/v1.4.8/rke_linux-amd64
Rename the downloaded binary to rke:

bash
Copy
mv rke_linux-amd64 rke
Make the binary executable:

bash
Copy
chmod +x rke
Move the binary to a directory in your PATH (e.g., /usr/local/bin):

bash
Copy
sudo mv rke /usr/local/bin/
Verify the Installation:
Run the following command to verify that rke is installed correctly:

Disable swap permanently:

    Edit the /etc/fstab file and comment out or remove any lines that reference swap partitions.

    Alternatively, you can use the following command to remove all swap entries:
    bash
    Copy

    sudo sed -i '/swap/d' /etc/fstab

    After editing the file, disable swap:
    bash
    Copy

    sudo swapoff -a

Verify that swap is disabled:
bash
Copy

free -h

bash
Copy
rke --version




* HTTP Proxy: 
```
export http_proxy=http://thanwin:thanRup0z@13.228.222.39:34322
```
