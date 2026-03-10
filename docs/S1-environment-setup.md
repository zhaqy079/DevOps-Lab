# VM set up & GitLab-self-hosted-lab

## Environment
- Host: Windows
- VM: VirtualBox
- OS: Xubuntu 20.04
- RAM: 6GB
- CPU: 4 cores
- Display-Video Memory: 128MB

## Network
VirtualBox NAT
Port forwarding (Host Port → Guest Port): 2222 → 22 

### Network Configuration，Make sure the SSH server is installed and running inside the VM:
At the vm verify:
```
sudo apt install openssh-server 
sudo systemctl status ssh
```

## SSH Connection
```
ssh <username>@127.0.0.1 -p 2222
```

## GitLab Installation
Reference: https://packages.gitlab.com/gitlab/gitlab-ce/install
```
sudo apt update
sudo apt-get upgrade
sudo apt install curl openssh-server ca-certificates tzdata perl

curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash

sudo EXTERNAL_URL="http://localhost" apt install gitlab-ce
```
## GitLab Dependencies Comparison

| Dependency | CentOS (Tutorial) | Ubuntu / Xubuntu (This Lab Setup) | Purpose |
|-----------|------------------|-----------------------------|--------|
| Package manager | yum | apt | System package manager |
| curl | yum install curl | apt install curl | Download GitLab repository |
| SSH server | openssh-server | openssh-server | Enable SSH access |
| perl | perl | perl | Required runtime dependency |
| SELinux tools | policycoreutils-python | —— | SELinux management |
| certificates | —— | ca-certificates | HTTPS verification |
| timezone | —— | tzdata | System timezone configuration |


## Access
```
http://localhost
```

