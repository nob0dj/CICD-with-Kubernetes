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

jenkins.ideacube.co.kr        192.168.15.40

          sudo apt-get update
          sudo apt-get install fontconfig openjdk-17-jre
          java -version
          sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
            https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
          echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
            https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
            /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt-get install jenkins

harbor.ideacube.co.kr         192.168.15.50

          apt update && sudo apt full-upgrade
          apt install apt-transport-https ca-certificates curl 
          
          # Add Docker's official GPG key:
          sudo apt-get update
          sudo apt-get install ca-certificates curl
          sudo install -m 0755 -d /etc/apt/keyrings
          sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
          sudo chmod a+r /etc/apt/keyrings/docker.asc
          
          # Add the repository to Apt sources:
          echo \
            "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
            $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
            sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
          sudo apt-get update
          
          sudo apt-get install docker-ce docker-ce-cli containerd docker-buildx-plugin docker-compose-plugin
          
          sudo docker run hello-world
          sudo groupadd docker
          sudo usermod -aG docker $USER
          => logout
          newgrp docker
          docker run hello-world

          
