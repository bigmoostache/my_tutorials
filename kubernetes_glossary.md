1. **Kubernetes (K8s)**: An open-source container orchestration platform.
  
2. **Pod**: The smallest deployable unit in K8s, representing one or more containers.

3. **Node**: A virtual or physical machine in a Kubernetes cluster. Can be a "control plane" (manages the cluster) or "worker" (runs applications).

4. **Control Plane**: Set of nodes (formerly "master") responsible for managing the overall state of the cluster.

5. **Worker Node**: Nodes that run applications within containers.

6. **Service**: Exposes a set of pods as a network service, either inside or outside the cluster.

7. **Volume**: A storage unit in K8s, allowing data persistence across container restarts.

8. **Deployment**: Declares the desired state for pods and ReplicaSets, enabling updates and rollbacks.

9. **ReplicaSet**: Ensures a specified number of pod replicas are running.

10. **ConfigMap**: A mechanism to externalize configuration from application containers.

11. **Secret**: Stores sensitive information like passwords.

12. **kube-apiserver**: Main management component of the control plane, exposing the Kubernetes API.

13. **etcd**: Consistent and highly-available key-value store for all cluster data.

14. **kube-controller-manager**: Runs controller processes for various core functions.

15. **kube-scheduler**: Assigns work, in the form of pods, to worker nodes.

16. **kubelet**: Agent on each node ensuring containers in a pod are running.

17. **kube-proxy**: Maintains network rules for pod communication.

18. **Container Runtime**: Software for running containers (e.g., Docker, containerd).

19. **Ingress**: Manages external access to services, like HTTP traffic routing.

20. **Namespace**: Allows multiple virtual clusters within a single physical cluster.

21. **DaemonSet**: Ensures specific nodes run a copy of a pod.

22. **StatefulSet**: Manages stateful applications, with unique identities for pods.

23. **Job**: Runs short-lived, one-off tasks.

24. **CronJob**: Schedules jobs based on specified times.

25. **Helm**: A package manager for Kubernetes, bundling resources into charts.

26. **PV (Persistent Volume)**: A piece of storage in the cluster provisioned by an admin.

27. **PVC (Persistent Volume Claim)** : A users request for storage from a PV.

28. **Horizontal Pod Autoscaler**: Scales the number of pods in a deployment based on observed metrics.

29. **ClusterIP**: A type of service that is only accessible within the cluster.

30. **NodePort**: Exposes a service on a static port on each node.

31. **LoadBalancer**: Exposes a service using a cloud provider’s load balancer.


https://kubernetes.io/docs/tutorials/hello-minikube/

https://www.padok.fr/en/blog/minikube-kubeadm-kind-k3s

https://www.padok.fr/en/blog/kubeadm-kubernetes-cluster

https://k21academy.com/docker-kubernetes/container-runtime-is-not-running/


Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:
   ```
  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config
   ```
Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run 
   ```kubectl apply -f [podnetwork].yaml   ```
    with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:
```
kubeadm join 51.254.38.189:6443 --token bhbit3.l68np1t1ujs3ms1i \
        --discovery-token-ca-cert-hash sha256:256243dfd47edc1c55ef07aa97e1a3b534bfd8fccbefd0278021324db2e1dee5
```

**This will solve some shit**

```
rm /etc/containerd/config.toml
systemctl restart containerd
kubeadm init
```












sudo swapoff -a
sudo apt-get install ufw
sudo ufw allow 30000
sudo ufw allow 30001
sudo ufw allow 30002
sudo ufw allow 30003
sudo ufw allow 30004
sudo ufw allow 30005
sudo ufw allow 30006
sudo ufw allow 30007
sudo ufw allow 30008
sudo ufw allow 30009
sudo ufw allow 30010
sudo ufw allow 10250
sudo ufw allow 10257
sudo ufw allow 10259

cat <<EOF | sudo tee /etc/modules-load.d/k8s.conf
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

sudo sysctl --system

lsmod | grep br_netfilter
lsmod | grep overlay

for pkg in docker.io docker-doc docker-compose podman-docker containerd runc; do sudo apt-get remove $pkg; 
done

# Add Docker's official GPG key: https://docs.docker.com/engine/install/ubuntu/ (see here if not on debian)
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/debian/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add the repository to Apt sources:
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/debian \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update


## DEBIAN
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

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
sudo docker run hello-world
## END DEBIAN

sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo docker run hello-world

# Same here : if not on debian, see https://docs.docker.com/engine/install/linux-postinstall/

wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd_0.3.4.3-0.debian-bullseye_amd64.deb

OR

wget https://github.com/Mirantis/cri-dockerd/releases/download/v0.3.4/cri-dockerd_0.3.4.3-0.ubuntu-jammy_amd64.deb

sudo dpkg -i cri-dockerd_0.3.4.3-0.debian-bullseye_amd64.deb
sudo apt-get -f install


sudo apt-get update
# apt-transport-https may be a dummy package; if so, you can skip that package
sudo apt-get install -y apt-transport-https ca-certificates curl


curl -fsSL https://pkgs.k8s.io/core:/stable:/v1.28/deb/Release.key | sudo gpg --dearmor -o /etc/apt/keyrings/kubernetes-apt-keyring.gpg

echo 'deb [signed-by=/etc/apt/keyrings/kubernetes-apt-keyring.gpg] https://pkgs.k8s.io/core:/stable:/v1.28/deb/ /' | sudo tee /etc/apt/sources.list.d/kubernetes.list

sudo apt-get update
sudo apt-get install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# This is the command to run on the master node(s) ONLY

sudo kubeadm init --control-plane-endpoint=kube.blends.fr --cri-socket=unix:///var/run/cri-dockerd.sock

kubectl apply -f https://github.com/weaveworks/weave/releases/download/v2.8.1/weave-daemonset-k8s.yaml


Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

Alternatively, if you are the root user, you can run:

  export KUBECONFIG=/etc/kubernetes/admin.conf

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

You can now join any number of control-plane nodes by copying certificate authorities
and service account keys on each node and then running the following as root:

  kubeadm join kube.blends.fr:6443 --token sbse9h.y0tusosmg7ga2lku \
        --discovery-token-ca-cert-hash sha256:172c07965448147db046a1255aeebefa6eaf8bbcb0078f1b78992fde0bbc03a8 \
        --control-plane

Then you can join any number of worker nodes by running the following on each as root:


sudo kubeadm reset
sudo systemctl daemon-reload
sudo systemctl restart kubelet
sudo apt-mark hold kubelet kubeadm kubectl
sudo rm /etc/kubernetes/kubelet.conf /etc/kubernetes/pki/ca.crt
sudo kubeadm join kube.blends.fr:6443 --token sbse9h.y0tusosmg7ga2lku \
        --discovery-token-ca-cert-hash sha256:172c07965448147db046a1255aeebefa6eaf8bbcb0078f1b78992fde0bbc03a8 \
        --cri-socket=unix:///var/run/cri-dockerd.sock


# dashboard

kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml
kubectl proxy



# to creat ea new join token
kubeadm token create --print-join-command






# Cleaning an instance from all docker (/!\ will remove all docker images and containers)

sudo docker stop $(docker ps -aq)
sudo docker rm $(docker ps -aq)
sudo docker rmi $(docker images -q)
sudo docker volume rm $(docker volume ls -q)
sudo docker network prune
sudo systemctl stop docker
sudo apt-get purge -y docker-engine docker docker.io docker-ce docker-ce-cli
sudo rm -rf /var/lib/docker /etc/docker
sudo rm /etc/systemd/system/docker.service.d
sudo groupdel docker
sudo rm -rf /usr/bin/docker /usr/bin/dockerd
sudo systemctl daemon-reload
sudo reboot

# To check status of node
kubectl describe node blends-main













There are several points to remember when setting up the cluster with "kubeadm init" and it is clearly documented on the Kubernetes site kubeadm cluster create:

"kubeadm reset" if you have already created a previous cluster
Remove the ".kube" folder from the home or root directory
(Also stopping the kubelet with systemctl will allow for a smooth setup)
Disable swap permanently on the machine, especially if you are rebooting your linux system
And not to forget, install a pod network add-on according to the instructions provided on the add on site (not Kubernetes site)
Follow the post initialization steps given on the command window by kubeadm.
If all these steps are followed correctly then your cluster will run properly.

And don't forget to do the following command to enable scheduling on the created cluster:

kubectl taint nodes --all node-role.kubernetes.io/master-
About how to install from behind proxy you may find this useful:



kubectl port-forward -n kubernetes-dashboard service/kubernetes-dashboard 8443:443


The YAML files described in the steps are Kubernetes resource definitions. They are used to create or modify resources in a Kubernetes cluster.

Here's how you use them:

1. **Save the ServiceAccount YAML**:
   First, save the `ServiceAccount` definition to a file. You can name it `admin-user-serviceaccount.yaml` or any other descriptive name:

   ```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
   ```

2. **Apply the ServiceAccount YAML**:
   Using `kubectl`, apply the YAML file to create the `ServiceAccount`:

   ```bash
   kubectl apply -f admin-user-serviceaccount.yaml
   ```

3. **Save the ClusterRoleBinding YAML**:
   Next, save the `ClusterRoleBinding` definition to a file. You can name it `admin-user-clusterrolebinding.yaml`:

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

4. **Apply the ClusterRoleBinding YAML**:
   Using `kubectl`, apply the YAML file to create the `ClusterRoleBinding`:

   ```bash
   kubectl apply -f admin-user-clusterrolebinding.yaml
   ```

5. **Get the Bearer Token**:
   As mentioned, you can retrieve the token associated with the `ServiceAccount` you created:

   ```bash
   kubectl -n kubernetes-dashboard describe secret $(kubectl -n kubernetes-dashboard get secret | grep admin-user | awk '{print $1}')
   ```

   This command looks complex, but here's a breakdown:
   - The inner `kubectl` command lists secrets in the `kubernetes-dashboard` namespace.
   - It then pipes (`|`) that list to `grep` to find the line corresponding to the `admin-user` ServiceAccount.
   - Then, it uses `awk` to extract the name of the secret.
   - The outer `kubectl describe` command then describes this secret, where you can find the token in its output.

6. **Using the Token**:
   With the token, you can log into the Kubernetes Dashboard or use it for other authentication purposes. Just ensure that you keep this token secure since it grants admin privileges.

Remember, this setup gives the service account cluster-wide administrative privileges, which may not be appropriate for all environments. Always follow best security practices and grant only the required permissions for your use case.




Getting a long-lived Bearer Token for ServiceAccount
We can also create a token with the secret which bound the service account and the token will be saved in the Secret:

apiVersion: v1
kind: Secret
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
  annotations:
    kubernetes.io/service-account.name: "admin-user"   
type: kubernetes.io/service-account-token  
After Secret is created, we can execute the following command to get the token which saved in the Secret:

kubectl get secret admin-user -n kubernetes-dashboard -o jsonpath={".data.token"} | base64 -d


eyJhbGciOiJSUzI1NiIsImtpZCI6IjI0d2VxcHViNFRURXhyMjFNOUs5andxOVEtMi0tN2pYNkhSLVdjeFdjWWsifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlcm5ldGVzLWRhc2hib2FyZCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJhZG1pbi11c2VyIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQubmFtZSI6ImFkbWluLXVzZXIiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC51aWQiOiIyNTQwNjE0Zi0wODgyLTRkOWYtOGYwYi1mNjEwY2YwZTc5NzEiLCJzdWIiOiJzeXN0ZW06c2VydmljZWFjY291bnQ6a3ViZXJuZXRlcy1kYXNoYm9hcmQ6YWRtaW4tdXNlciJ9.BkLy6YmCl3AtlQwJ6NF1tbKUXSjyXZxiWyFb6vTdQhvgUomXhkNkCUDuGGfg2Q8UCpGp8XvBduKLrZZg8rgloAtMYKvQPaCHaK079qIr2xVQXw0Cexvi1bcb-4ornBh9mpen8eCMAInNb1l7dmoTcVfrm3YzA-0h1xyWkTBxyqaxC2z_fqzpEaYLZG9Z6U9-WsxUdjGMS4kkw6LBrtxCFo4xAJzWCcp_Qy0I4Ux_1fP-KOPkXBLiPHM2LQEIHdKLp8ws6VamTamYmeMqS3j4m0Sko4nt9TBVGDYuYFcN-LatRbuh2wcAAEg4WrUQPBy80EcYZA3CXAHlaAaD4enUlA





When setting up a Kubernetes worker node, the storage on the node itself (the instance's default storage) is typically used for the OS, Kubernetes components, container runtime, and other system-related files. To utilize the additional attached storage (in your case, the 2TB volume), you can create a Persistent Volume (PV) and Persistent Volume Claim (PVC) that map to this attached storage. However, be aware that a single large PV might not be the most flexible approach if you need to share storage among many pods.

Here's a high-level overview of how to set this up:

1. **Prepare the Attached Volume**:

    Before Kubernetes can use the attached volume, the volume has to be formatted and mounted to a known location on the worker node. Assuming the attached volume is recognized as `/dev/sdb`:

    ```bash
    # Create a mount point
    mkdir /mnt/data

    # Format the volume (if it's not formatted yet)
    mkfs.ext4 /dev/sdb

    # Mount the volume
    mount /dev/sdb /mnt/data

    # To ensure the volume is mounted after reboots, add an entry in /etc/fstab
    echo '/dev/sdb /mnt/data ext4 defaults 0 0' >> /etc/fstab
    ```

2. **Create a Persistent Volume (PV)**:

    Create a YAML file for the PV:

    ```yaml
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: my-2tb-volume
    spec:
      capacity:
        storage: 2Ti
      accessModes:
        - ReadWriteOnce
      persistentVolumeReclaimPolicy: Retain
      storageClassName: local-storage
      local:
        path: /mnt/data
      nodeAffinity:
        required:
          nodeSelectorTerms:
          - matchExpressions:
            - key: kubernetes.io/hostname
              operator: In
              values:
              - your-worker-node-name
    ```

    Apply it:

    ```bash
    kubectl apply -f your-pv.yaml
    ```

3. **Use a Persistent Volume Claim (PVC) to Use the Storage**:

    When deploying applications, you can now claim storage from this PV using a PVC:

    ```yaml
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: my-pvc
    spec:
      storageClassName: local-storage
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 1Ti  # or however much storage you want to claim
    ```

4. **Mount the PVC in a Pod**:

    When defining a Pod, you can mount this PVC:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: my-pod
    spec:
      containers:
      - name: my-container
        image: my-image
        volumeMounts:
        - mountPath: /path/in/container
          name: my-volume
      volumes:
      - name: my-volume
        persistentVolumeClaim:
          claimName: my-pvc
    ```

Note: Using local volumes like this ties your storage closely to the specific node. If the node fails, you might have to manually intervene to get the PV running on another node. For more fault-tolerant storage solutions in a Kubernetes environment, consider distributed storage systems like Ceph, Rook, GlusterFS, or cloud provider's storage solutions like AWS EBS, Google Persistent Disk, etc.







swapoff -a    # will turn off the swap 
sudo kubeadm reset
sudo systemctl daemon-reload
sudo systemctl restart kubelet
iptables -F && sudo iptables -t nat -F && sudo iptables -t mangle -F && sudo iptables -X  # will reset iptables