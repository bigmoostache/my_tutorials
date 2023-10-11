## 1. Requirements and ports

```bash
sudo swapoff -a
sudo systemctl stop firewalld 
sudo systemctl disable firewalld 
sudo apt-get install ufw
sudo ufw allow 30000:32767/tcp
sudo ufw allow 10250
sudo ufw allow 10257
sudo ufw allow 10259
sudo ufw allow 6443
sudo ufw allow 2379
sudo ufw allow 2380
sudo ufw allow 80
sudo ufw allow 443
sudo ufw allow 8443
sudo ufw allow 22
sudo ufw allow 10250/tcp
sudo ufw allow 10257/tcp
sudo ufw allow 10259/tcp
sudo ufw allow 6443/tcp
sudo ufw allow 2379/tcp
sudo ufw allow 2380/tcp
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 8443/tcp
sudo ufw allow 22/tcp
sudo ufw enable
```



## 2. Configure the local IP tables to see the Bridged Traffic

Enable the bridged traffic
```bash
lsmod | grep br_netfilter
sudo modprobe br_netfilter
```

Copy the below contents in this file.. 

```/etc/modules-load.d/k8s.conf
sudo nano /etc/modules-load.d/k8s.conf
br_netfilter
```

Copy the below contents in this file.. 
```/etc/sysctl.d/k8s.conf
sudo nano /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
```

```bash
sudo sysctl --system
```

## 3. Install Docker as a Container RUNTIME

First, remove any old versions of Docker that may be on your system:

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; 
done
```

Refer to the [Docker install dcumentation](https://docs.docker.com/engine/install/) for installation instructions for your OS. As of this writing, the following instructions are valid for Ubuntu and Debian.

### Ubuntu

```bash
for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; done
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```
Then
```bash
sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
```

### Debian

```bash
TODO
```

## 4. Configure Docker Daemon for cgroups management & Start Docker

In the file **/etc/docker/daemon.json** write

```/etc/docker/daemon.json
sudo nano /etc/docker/daemon.json
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2"
}
```

Then 
    
```bash
sudo systemctl daemon-reload
sudo systemctl restart docker
sudo systemctl enable docker
sudo systemctl status docker
```

Also, install the cri-dockerd package, see more details [here](https://github.com/Mirantis/cri-dockerd/releases)

### Ubuntu
```bash
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb

sudo dpkg -i cri-dockerd_0.3.4.3-0.debian-bullseye_amd64.deb
sudo apt-get -f install
```

### Debian
```bash
wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd_0.3.4.3-0.debian-bullseye_amd64.deb

sudo dpkg -i cri-dockerd_0.3.4.3-0.debian-bullseye_amd64.deb
sudo apt-get -f install
```

## 5. Install kubeadm, kubectl, kubelet

```bash
sudo apt-get update
sudo apt-get install -y apt-transport-https ca-certificates curl

curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# OR 

sudo systemctl enable --now kubelet
```

## 6. Configuring a cgroup driver

TODO? I don't think this is needed anymore. 

## 7. Deploy a  kubernetes cluster using kubeadm  - only in master node

```bash
sudo su
sudo rm /etc/kubernetes/kubelet.conf /etc/kubernetes/pki/ca.crt
kubeadm init --pod-network-cidr=10.10.0.0/16 \
--apiserver-advertise-address=162.19.69.33 \
--cri-socket=unix:///var/run/cri-dockerd.sock # execute without crio-socket, if an error pops, copy paste the docker socket path
```

To find the master node IP, run the following command:

```bash
ip addr
```

This should take a few minutes to complete. Once it is done, you should see something like this:

```bash
Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/c...

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.74.10:6443 --token 12633x.b5t19vsm70uas014 \
        --discovery-token-ca-cert-hash sha256:040ba1efe8a1a260db3593c9c7493776cd84ce1a8fd0850933042bd619a197a3 \
        --cri-socket=unix:///var/run/cri-dockerd.sock
```

Therefore, run the following commands:

```bash
exit
-p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config 
```

You should now deploy a pod network to the cluster. I would suggest using Weave Net, but you can use any other CNI network. See [here](https://kubernetes.io/docs/concepts/cluster-administration/addons/) for more details.

For Weave net - as of this writing, the following command works:

```bash
kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml
```

## 8. Reprinting a join command

```bash
kubeadm token create --print-join-command
```


## 9. Join the worker nodes to the master

Basically copy paste the join command from the master node to the worker nodes. you may have to specify the socket just like with the join command on the master node.

```bash
kubeadm join 192.168.74.10:6443 --token l431j0.0tz0bbuu7hj64lw5 \
        --discovery-token-ca-cert-hash sha256:1234567890 
```

You may have to change your host name to avoid conflicts. 

```bash
sudo hostname c2-7-gra11-new
echo "c2-7-gra11-new" | sudo tee /etc/hostname
```


## 10. Accessing the Kubernetes Dashboard

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
```

```bash
kubectl proxy
```

Forward a tunnel to the port specified by the proxy (usually 8001)

```bash
ssh -L 8001:localhost:8001 master_node_ssh_config
```

Then, access the dashboard [here](http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.)

You will be prompted to enter a token. To generate a permanent token, run the following commands.

Apply the following manifests to create a service account for the dashboard:

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
```
Reminder: here is how to apply a manifest:

```bash
kubectl apply -f my_manifest.yaml
```

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
```

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token  
```

Finally, retrieve the token:

```bash
kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d
```

## 11. To kill everything

```bash
kubeadmin reset --cri-socket=unix:///var/run/cri-dockerd.sock
rm /etc/kubernetes/kubelet.conf /etc/kubernetes/pki/ca.crt
docker kill $(docker ps -q)
docker rm $(docker ps -a -q)
```