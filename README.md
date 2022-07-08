## RKE2-installation-steps

#### Step:1 Create a directory as below.
```
mkdir /root/rke2-artifacts && cd /root/rke2-artifacts/
```
#### Step 2: Download below calico and rke2 core images.
```
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images-calico.linux-amd64.tar.gz
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images-core.linux-amd64.tar.gz
```
#### Step 3: Create dirs and Copy the downloaded tar files to it.
```
mkdir -p /var/lib/rancher/rke2/agent/images/
cp rke2-images-calico.linux-amd64.tar.gz /var/lib/rancher/rke2/agent/images/
cp rke2-images-core.linux-amd64.tar.gz /var/lib/rancher/rke2/agent/images/
```
#### Step 4: Download remaining rke2 images wrt to specific version as below.
```
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images.linux-amd64.tar.zst
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2.linux-amd64.tar.gz
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/sha256sum-amd64.txt
curl -sfL https://get.rke2.io --output install.sh
```
#### Step 5: Run the below command to initiate RKE2 server cluster.
```
INSTALL_RKE2_ARTIFACT_PATH=/root/rke2-artifacts sh install.sh

or

INSTALL_RKE2_ARTIFACT_PATH=/root/rke2-artifacts sh /root/rke2-artifacts/install.sh
```
#### Step 6: Create a dir as below and add content to it.
```
mkdir -p /etc/rancher/rke2

write-kubeconfig-mode: "0644"
tls-san:
  - "master-node-hostname"
node-label:
  - "nodetype=master"
node-ip: "master node ipv4, master node ipv6"
cluster-cidr: "10.42.0.0/16,2001:cafe:42:0::/56"
service-cidr: "10.43.0.0/16,2001:cafe:42:1::/112"
cluster-dns: "10.43.0.10"
cluster-domain: "master-node-hostname"
cni:
  - calico
disable:
  - rke2-canal
  - rke2-kube-proxy

:wq
```
#### Important!! - Make sure - disable firewalld & iptables if active
#### enable and start "rke2-server.service" service

```
systemctl enable rke2-server.service
systemctl start rke2-server.service
```
#### Step 7: Add the below env variables in .bashrc file and source it.
```
export PATH=$PATH:/var/lib/rancher/rke2/bin 
export KUBECONFIG=/etc/rancher/rke2/rke2.yaml

source .bashrc
```
#### Step 8: You’ll need this token to add the other nodes to the cluster.
```
cat /var/lib/rancher/rke2/server/node-token
```

================================================================================
## Steps for worker or agent nodes

#### Step:1 Create a directory as below.
```
mkdir /root/rke2-artifacts && cd /root/rke2-artifacts/
```
#### Step 2: Download below calico and rke2 core images.
```
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images-calico.linux-amd64.tar.gz
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images-core.linux-amd64.tar.gz
```
#### Step 3: Create dirs and Copy the downloaded tar files to it.
```
mkdir -p /var/lib/rancher/rke2/agent/images/
cp rke2-images-calico.linux-amd64.tar.gz /var/lib/rancher/rke2/agent/images/
cp rke2-images-core.linux-amd64.tar.gz /var/lib/rancher/rke2/agent/images/
```
#### Step 4: Download remaining rke2 images wrt to specific version as below.
```
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2-images.linux-amd64.tar.zst
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/rke2.linux-amd64.tar.gz
curl -OLs https://github.com/rancher/rke2/releases/download/v1.23.7%2Brke2r2/sha256sum-amd64.txt
curl -sfL https://get.rke2.io --output install.sh
```
#### Step 5: Run the below command to add RKE2 worker or agent nodes.
```
INSTALL_RKE2_ARTIFACT_PATH=/root/rke2-artifacts INSTALL_RKE2_TYPE='agent' sh /root/rke2-artifacts/install.sh
```
#### Step 6: Create a dir and a file and add the content to it.
```
mkdir -p /etc/rancher/rke2/
vim /etc/rancher/rke2/config.yaml

Content for config.yaml:

server: https://master-node-IP:9345
token: <token from server node>
node-ip: "worker ipv4 or worker ipv6"
```
#### Important!! - Make sure - disable firewalld & iptables if active
#### enable and start "rke2-agent.service" service
```
systemctl enable rke2-agent.service
systemctl start rke2-agent.service
```

