# ubuntu-wsl2-systemd-script
Script to enable systemd support on current Ubuntu WSL2 images from the Windows store. 
Script is unsupported and will no longer be maintained, but will be up here because it is used by quite some people.
I am not responsible for broken installations, fights with your roommates and police ringing your door ;-).

Instructions from [the snapcraft forum](https://forum.snapcraft.io/t/running-snaps-on-wsl2-insiders-only-for-now/13033) turned into a script. Thanks to [Daniel](https://forum.snapcraft.io/u/daniel) on the Snapcraft forum! 

### Install prerequisites
```sh
sudo apt-get install -y \
    apt-transport-https \
    ca-certificates \
    curl \
    software-properties-common
```
### Download and add the official Docker PGP key
```sh
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

### Add the stable channel repository
```sh
sudo add-apt-repository \
   "deb [arch=amd64] https://download.docker.com/linux/ubuntu \
   $(lsb_release -cs) \
   stable"
```

### Update the package list
```sh
sudo apt-get update -y
```

### Install the latest Docker CE
```sh
sudo apt-get install -y docker-ce
```

### Add your user to access the Docker CLI without root user permissions
```sh
sudo usermod -aG docker $USER && newgrp docker
```

### Install systemctl
```sh
git https://github.com/antonchernik/ubuntu-wsl2-systemd-script.git
cd ubuntu-wsl2-systemd-script/
bash ubuntu-wsl2-systemd-script.sh
# Enter your password and wait until the script has finished
```
### Restart the LxssManager in Windows to initialize systemctl with WSL 2.
![Restart the LxssManager](https://github.com/antonchernik/ubuntu-wsl2-systemd-script/blob/master/lxssmanager.png?raw=true)

### Helm install
```sh
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
sudo apt-get install apt-transport-https --yes
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt-get update
sudo apt-get install helm
```

### Install conntrack
```sh
sudo apt install -y conntrack
```

### Install conntrack
```sh
sudo apt install -y conntrack
```

### Install microk8s
```sh
sudo snap install microk8s --classic
sudo microk8s.start
sudo microk8s.enable dns helm3 storage ingress registry dashboard
sudo microk8s.status
sudo microk8s.inspect
```
File "/etc/docker/daemon.json" does not exist.
You should create it and add the following lines:
```json
{
  "insecure-registries" : ["localhost:32000"]
}
```

### Add your user to access the Microk8s without root user permissions
```sh
sudo usermod -a -G microk8s anton
mkdir ~/.kube
sudo chown -f -R anton ~/.kube
newgrp microk8s
```

### Install kubectl
```sh
snap install kubectl --classic
```

### Configure kubectl
```sh
microk8s.kubectl config view --raw > $HOME/.kube/config
echo 'export KUBECONFIG=$HOME/.kube/config' >> ~/.bash_profile
```

### Install Go
```sh
sudo add-apt-repository ppa:longsleep/golang-backports
sudo apt-get update
sudo apt install golang-go
```

### Install k9s
```sh
cd /tmp
git clone https://github.com/derailed/k9s.git
cd k9s && make build
sudo mv ./execs/k9s /usr/local/bin/k9s
```