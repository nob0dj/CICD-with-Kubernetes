## CICD-with-Kubernetes
KUT CI/CD with Kubernetes

### Labs Server List
| Server Name        | Server Hostname               | Specs                           | IP Address     | Port Forwarding(ssh) | Port Forwarding(http) |
| ------------------ | ----------------------------- | ------------------------------- | -------------- | -------------------- | --------------------- |
| windev             | windev                        | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.10  |  -                   |  -                    |  
| gitLab             | gitLab.ideacube.co.kr         | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.30  |  22                  |  8080                 |
| jenkins            | jeninks.ideacube.co.kr        | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.40  |  23                  |  80                   |
| harbor             | harbor.ideacube.co.kr         | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.40  |  24                  |  9090                 |
| k8s-control        | k8s-control.ideacube.co.kr    | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.101 |  25                  |  -                    |
| worker-node-01     | worker-node-01.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |  26                  |  -                    |
| worker-node-02     | worker-node-02.ideacube.co.kr | 2 vCPU, 4 GB RAM, 100GB Disk    | 192.168.15.102 |  27                  |  -                    |

### Visual Studio Code & VirtualBox

          VirtualBox
          Visual Studio Code
          
          VBoxManage setextradata global GUI/Input/HostKeyCombination 162,164
          VBoxManage natnetwork add --netname NatNetwork --network "192.168.15.0/24" --enable --dhcp off --port-forward-4 "ssh:tcp:[]:22:[192.168.15.101]:22"
          VBoxManage natnetwork modify --netname NatNetwork --port-forward-4 "ssh:tcp:[]:23:[192.168.15.102]:22"
              
          code --install-extension MS-CEINTL.vscode-language-pack-ko
          code --install-extension ms-vscode-remote.remote-ssh
          code --install-extension ms-azuretools.vscode-docker
          code --install-extension vscjava.vscode-java-pack
          code --install-extension vscjava.vscode-gradle
          code --install-extension vmware.vscode-boot-dev-pack

### Ubuntu 64bit Server 22.04.x(Minimized)
- After installing ubuntu 64 server minimum specifications
- Create User => user1/1234
          
          sudo su
          apt-get install net-tools iputils-ping
          printf "Server Name(Each Server)" > /etc/hostname
          printf "\n192.168.15.30 gitlab.ideacube.co.kr\n192.168.15.40 jenkins.ideacube.co.kr\n192.168.15.50 harbor.ideacube.co.kr\n192.168.15.101 k8s-control.ideacube.co.kr\n192.168.15.102 worker-node-01.ideacube.co.kr\n192.168.15.103 worker-node-02.ideacube.co.kr\n\n" >> /etc/hosts
          
          cat /etc/hosts
          cat /etc/hostname
          cat /etc/netplan/00-installer-config.yaml
          => check ip or change ip
          netplan apply

### Settings for each server
- gitlab.ideacube.co.kr         192.168.15.30

          sudo apt-get update
          sudo apt-get install -y curl openssh-server ca-certificates tzdata perl
          curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ce/script.deb.sh | sudo bash
          sudo EXTERNAL_URL="http://gitlab.ideacube.co.kr" apt-get install gitlab-ce
          # List available versions: apt-cache madison gitlab-ce
          # Specifiy version: sudo EXTERNAL_URL="https://gitlab.example.com" apt-get install gitlab-ce
          # Pin the version to limit auto-updates: sudo apt-mark hold gitlab-ce
          # Show what packages are held back: sudo apt-mark showhold

- jenkins.ideacube.co.kr        192.168.15.40

          sudo apt-get update
          sudo apt-get install fontconfig openjdk-17-jre
          java -version
          sudo wget -O /usr/share/keyrings/jenkins-keyring.asc \
            https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key
          echo "deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc]" \
            https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
            /etc/apt/sources.list.d/jenkins.list > /dev/null
          sudo apt-get install jenkins

- harbor.ideacube.co.kr         192.168.15.50

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
          
          sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
          
          sudo docker run hello-world
          sudo groupadd docker
          sudo usermod -aG docker $USER
          => logout
          newgrp docker
          docker run hello-world
          
          cd /tmp
          curl -s https://api.github.com/repos/goharbor/harbor/releases/latest | grep browser_download_url | cut -d '"' -f 4 | grep '\.tgz$' | wget -i -
          
          tar -xzvf harbor-offline-installer-v2.10.2.
          sudo mv harbor /opt/
          
          cp harbor.yml.tmpl harbor.yml
          sudo vi harbor.yml
          hostname : harbor.ideacube.co.kr
          #https <- comment
          harbor_admin_password : 
          database :
          
          sudo ./prepare.sh
          sudo ./install.sh


- k8s-control.ideacube.co.kr    192.168.15.101 
- worker-node-01.ideacube.co.kr 192.168.15.102 
- worker-node-02.ideacube.co.kr 192.168.15.103

### ALL
          sudo su
          vi /etc/fstab
          #/swap
          swapoff -a
          free -m
          
          cat <<EOF | tee /etc/modules-load.d/k8s.conf
          overlay
          br_netfilter
          EOF
          
          sudo modprobe overlay
          sudo modprobe br_netfilter
          
          cat <<EOF | sudo tee /etc/sysctl.d/k8s.conf
          net.bridge.bridge-nf-call-iptables  = 1
          net.bridge.bridge-nf-call-ip6tables = 1
          net.ipv4.ip_forward                 = 1
          EOF
          
          sysctl --system
          
          apt update
          apt upgrade -y
          apt install apt-transport-https ca-certificates curl jq gpg -y
          curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.29/deb/Release.key | gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg
          echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.29/deb/ /' | tee /etc/apt/sources.list.d/kubernetes.list
          
          cat /etc/apt/sources.list.d/kubernetes.list
          
          apt install containerd kubelet kubeadm kubectl
          
          mkdir -p /etc/containerd
          containerd config default > /etc/containerd/config.toml
          vi /etc/containerd/config.toml
          SystemdCgroup = true
          systemctl restart containerd
          free -h
          sysctl --system
          
          /etc/containerd/config.toml
          sandbox_image = "registry.k8s.io/pause:3.8" -> "registry.k8s.io/pause:3.9"

### Control Panel

          kubeadm init --pod-network-cidr 10.10.0.0/16 --node-name k8s-control
          kubeadm token create --print-join-command
          
          curl -LO https://github.com/cilium/cilium-cli/releases/latest/download/cilium-linux-amd64.tar.gz     
          tar xzvfC cilium-linux-amd64.tar.gz /usr/local/bin
          rm cilium-linux-amd64.tar.gz
          cilium install --set ipam.operator.clusterPoolIPv4PodCIDRList=10.10.0.0/16
          cilium status

          #################################
          # kubeadm 초기화
          $ sudo kubeadm reset
          $ sudo systemctl restart kubelet
          $ sudo reboot
          ##################################

### Etc
- Docker install Ubuntu
 
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
          
          sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
          
          sudo docker run hello-world
          sudo groupadd docker
          sudo usermod -aG docker $USER
          => logout
          newgrp docker
          docker run hello-world
  
          

          
