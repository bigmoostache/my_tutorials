kubectl drain c2-7-gra11  --ignore-daemonsets --delete-local-data
kubectl delete node <node-name>


then, on the node:
sudo kubeadm reset --cri-socket=unix:///var/run/cri-dockerd.sock
add the cri part if needed