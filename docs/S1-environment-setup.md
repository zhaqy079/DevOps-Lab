# PartI GitLab-self-hosted-lab

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
# Check status
sudo gitlab-ctl status
sudo gitlab-rake gitlab:env:info
sudo gitlab-ctl tail

# Access at the browser 
http://localhost

# Check root pwd
sudo cat /etc/gitlab/initial_root_password

# More management cmd
sudo gitlab-ctl stop
sudo gitlab-ctl start
sudo gitlab-ctl restart

```

# PartII Docker install GitLab 
- NOTES SEPERATE VM, OR DELETE SELF HOST VERSION 
- Reference: https://docs.docker.com/engine/install/ubuntu/
```
# Delete steps
sudo gitlab-ctl status
sudo gitlab-ctl stop
sudo apt remove gitlab-ce -y
sudo apt purge gitlab-ce -y
# Delete all repo data
sudo rm -rf /etc/gitlab
sudo rm -rf /var/log/gitlab
sudo rm -rf /var/opt/gitlab
# Check 
sudo ss -tulpn | grep -E '80|443|22'
```
## Steps
1 Install Docker
2 Pull GitLab image
3 Run cont

```
# Set Docker environment, create docker key
sudo apt update
sudo apt install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources( run all at the once) 
sudo tee /etc/apt/sources.list.d/docker.sources <<EOF
Types: deb
URIs: https://download.docker.com/linux/ubuntu
Suites: $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}")
Components: stable
Signed-By: /etc/apt/keyrings/docker.asc
EOF

sudo apt update

# Install docker : Docker Engine, Docker CMD, container runtime, build img, multi container
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Test
docker --version
sudo docker run hello-world

# Run Gitlab Docker Container
sudo docker run --detach \
--hostname gitlab.local \
--publish 8080:80 \
--publish 8443:443 \
--publish 2224:22 \
--name gitlab \
--restart always \
--volume /srv/gitlab/config:/etc/gitlab \
--volume /srv/gitlab/logs:/var/log/gitlab \
--volume /srv/gitlab/data:/var/opt/gitlab \
gitlab/gitlab-ce:latest

# Check GitLab Container 
docker ps
# (if able to see, means GitLab container is running)
gitlab/gitlab-ce

# Open GitLab
http://localhost:8080
# if not able to visit
docker logs -f gitlab

# docker container lifecycle
docker run -> container running ->
docker stop -> container stopped ->
docker start -> container running.


# Test
# Check Docker service status
sudo systemctl status docker
# If not active
sudo systemctl start docker
sudo systemctl enable docker
# Check if gitLab is active
sudo docker ps -a
sudo docker logs -f gitlab

# Find the initial pwd
sudo docker exec gitlab cat /etc/gitlab/initial_root_password
```












