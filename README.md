# rancher-rodeo

# Prerequisites
* Machine with 8GB RAM, SSD – Run 1 rancher server and 3 nodes (change the RAM size)
* Machine with 16GB RAM NVME Disk – Run 1 Rancher Server and 3 VMS
* 4 VMs running in Cloud (2GB each, ensure security group has UDP 500 & 4500, TCP 8080, 2049 & 80 open. Recommend all traffic on UDP and TCP allowed in local network)

**Only run Docker 1.12.6 if you want the K8s demo to work!**

# Vagrant users
* https://www.vagrantup.com/ (For installing Vagrant)
* https://www.mls-software.com/opensshd.html (Windows only, install OpenSSHD on Windows to ensure vagrant can ssh into nodes to do provisioning
* https://github.com/chrisurwin/rancher-rodeo (Github repository containing all the necessary files to run your Vagrant setup)

# Running Rancher
* `sudo docker run -d -p 8080:8080 rancher/server`

# Persistent Storage: Setting up the NFS server
* `sudo docker run -d --name nfs --restart=always --privileged --net=host -v /usr/<user>/nfs:/nfsshare -e SHARED_DIRECTORY=/nfsshare itsthenetwork/nfs-server-alpine:4` **NB: Update the command with the path to your NFS folder**

# NFS driver
* Set the IP to the IP of the server running NFS **NB: Careful not to copy http:// into the ip field**
* Mount point should be set to **/**

# Wordpress persistent storage
* Upgrade DB, add volume path `db:/var/lib/mysql` driver `rancher-nfs`
* Upgrade Wordpress, add path `wp:/var/www/html` driver `rancher-nfs`

# Download kubectl
* Download kubectl version 1.5.4 for your platform (macOS/Linux/Windows): https://kubernetes.io/docs/tasks/tools/install-kubectl/ 
* **If using RancherOS GA on each node run 
* `ros engine switch docker-1.12.6`
* `system-docker restart docker`

# Run the yaml definitions from the rancher-rodeo Github repository
* `cd rancher-rodeo/k8s`
* `kubectl apply -f deployment.yaml` **NB: Update the IP address to your NFS server IP address**
* `kubectl apply -f class.yaml`

# Deploy Wordpress from Helm
* `helm install stable/wordpress --name wordpress --set persistence.enabled=true,persistence.storageClass=managed-nfs-storage,mariadb.persistence.storageClass=managed-nfs-storage`
