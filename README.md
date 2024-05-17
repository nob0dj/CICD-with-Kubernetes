# CICD-with-Kubernetes
KUT CI/CD with Kubernetes

## Labs Server List
| Server Name        | Server Hostname               | Specs                           | IP Address     | Port Forwarding |
| ------------------ | ----------------------------- | ------------------------------- | -------------- | --------------- |
| windev             | windev                        | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.10  |  -               |
| gitLab             | gitLab.ideacube.co.kr         | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.30  |  80             |
| jenkins            | jeninks.ideacube.co.kr        | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.40  |  8080           |
| harbor             | harbor.ideacube.co.kr         | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.40  |  9090           |
| k8s-control        | k8s-control.ideacube.co.kr    | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.101 |                 |
| worker-node-01     | worker-node-01.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |                 |
| worker-node-02     | worker-node-02.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |                 |

## Ubuntu 64bit Server 22.04.x(Minimized)
=> After installing ubuntu 64 server minimum specifications
=> Create User => user1/1234
sudo su
apt-get install net-tools iputils-ping
printf "Server Name(Each Server)" > /etc/hostname
printf "\n192.168.15.30 gitlab.ideacube.co.kr\n192.168.15.40 jenkins.ideacube.co.kr\n192.168.15.50 harbor.ideacube.co.kr\n192.168.15.101 k8s-control.ideacube.co.kr\n192.168.15.102 worker-node-01.ideacube.co.kr\n192.168.15.103 worker-node-02.ideacube.co.kr\n\n" >> /etc/hosts

cat /etc/hosts
cat /etc/hostname
cat /etc/netplan/00-installer-config.yaml
=> check ip or change ip
netplan apply

## Settings for each server
gitlab.ideacube.co.kr         192.168.15.30

          sudo apt-get update
          sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
          curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
          sudo EXTERNAL_URL="http://gitlab.ideacube.co.kr" apt-get install gitlab-ce
          # List available versions: apt-cache madison gitlab-ce
          # Specifiy version: sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ce
          # Pin the version to limit auto-updates: sudo apt-mark hold gitlab-ce
          # Show what packages are held back: sudo apt-mark showhold

