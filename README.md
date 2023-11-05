# russopi-k3s
Manifest files for raspberry pi k3s cluster

### To deploy (in specific directory):
```
kubectl kustomize | kubectl apply -f -
```

### Delete/undeploy:
```
kubectl kustomize | kubectl delete -f -
```

## Preparing the Raspberry Pi nodes:

1. Image the SD card using the Raspberry Pi Imager.
2. Raspberry Pi Imager: https://www.raspberrypi.org/software/
3. Install "Raspberry Pi OS Lite 64bit", enable SSH
4. Insert the SD card into the Raspberry Pi and allow it to boot (3-4 minutes)
5. Remove the SD card and plug it back into your computer
6. Open the SD card location and open the file "cmdline.txt"
    - Add the following to the end of the line of text:
      ```
      cgroup_memory=1 cgroup_enable=memory
      ```
7. Make the PI's have static IP's (i used router settings)
8. Repeat for all nodes!

## Master node setup (installing k3s)

1. SSH to the pi & become root
    ```
    sudo su -
    ```
2. Install K3s (master setup)
    ```
    curl -sfL https://get.k3s.io | K3S_KUBECONFIG_MODE="644" sh -s -
    ```
3. Get the node token from the master
    ```
    sudo cat /var/lib/rancher/k3s/server/node-token
    ```

## Worker node setup

1. Run this command on your other Raspberry Pi nodes
```
curl -sfL https://get.k3s.io | K3S_TOKEN="YOURTOKEN" K3S_URL="https://[your server]:6443" K3S_NODE_NAME="servername" sh
```

## Install Rancher
1. Create a separate Ubuntu Server 20.04.3 LTS VM (I used hyper-v, 2 cpus, 4gig memory)
2. Install docker on it:
    ```
    sudo apt remove -y docker docker-engine docker.io containerd runc
    ```
    ```
    sudo apt install -y docker.io
    ```
    ```
    sudo usermod -aG docker $USER
    ```
    ```
    newgrp docker
    ```
3. Start docker:
    ```
    sudo systemctl start docker
    ```
    ```
    sudo systemctl enable docker
    ```
    ```
    sudo systemctl status docker
    ```
4. Install rancher (you can also try "rancher:latest"):
    ```
    docker run -d --name=rancher-server --restart=unless-stopped -p 80:80 -p 443:443 --privileged rancher/rancher:v2.7.9
    ```
5. Go to localhost and follow instructions for password
6. Import your PI cluster (rancher UI)
7. Edit the cluster API file (agentImageOverride):
    ```
    rancher/rancher-agent:v2.5.8-linux-arm64
    ```



