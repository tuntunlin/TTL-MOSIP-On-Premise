# MOSIP K8S Cluster Setup using RKE

### 1. Install Kubectl
```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl
```
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

### 4. Install RKE
sudo apt-get update && sudo apt-get upgrade -y
wget https://github.com/rancher/rke/releases/download/v1.4.8/rke_linux-amd64
chmod +x rke_linux-amd64
sudo mv rke_linux-amd64 /usr/local/bin/rke
rke --version


### 5.Set up passwordless SSH. for K8S Infra
ssh-keygen -t rsa
ssh-copy-id worker1@172.16.10.117
ssh-copy-id worker2@172.16.10.118

### 6. Create a hosts.ini. Update the IP addresses
node1 ansible_host=172.16.10.117 ansible_user=test1 ansible_sudo_pass=test
node2 ansible_host=172.16.10.118 ansible_user=test2 ansible_sudo_pass=test

### 7.
ansible-playbook -i hosts.ini ports.yaml
ansible-playbook -i hosts.ini swap.yaml


