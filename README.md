# K3s High-Availability Cluster on AWS EC2
**Student Name:** Mpho Quinton Swele  
**Student Number:** 231001487  
**Course:** Advanced Diploma in ICT (Communication Networks)

---

# 1. System Requirements & Architecture
This deployment utilizes a **High-Availability (HA)** control plane with embedded **etcd**. Unlike a standard K3s setup with a single server and multiple agents, this architecture uses three server nodes to ensure fault tolerance.

### System Specifications (per node)
- **Instance Type:** t3.large
- **CPU:** 2 vCPUs
- **RAM:** 8 GiB
- **OS:** Ubuntu 22.04 LTS
- **Disk:** 8 GB gp3 SSD

### Architecture Explanation
- **K3s:** A lightweight, fully compliant Kubernetes distribution. It is used here because it packages dependencies (like etcd and Flannel) into a single binary, reducing the overhead typically found in K8s.
- **Control Plane:** Consists of 3 nodes running the API Server, Scheduler, and Controller Manager. 
- **Database (etcd):** Embedded within K3s, using a voting (quorum) system to maintain state.
- **Container Runtime:** K3s uses **containerd**, a lightweight and industry-standard runtime.
- **CNI (Flannel):** Manages the internal Pod network using VXLAN (UDP port 8472).
- **Storage:** Uses the **Local Path Provisioner** for dynamic volume allocation on the host disk.

---

# 2. Installation Steps

### Step 1: Networking & Security Group
Configured an AWS Security Group with the following ingress rules:
- **TCP 22**: SSH Access
- **TCP 6443**: K8s API Server
- **TCP 2379-2380**: etcd Client/Peer (Internal)
- **UDP 8472**: Flannel VXLAN (Internal)
- **TCP 30000-32767**: NodePort Services

# Step 2: Provisioning Master-1
```bash
sudo mkdir -p /etc/rancher/k3s
sudo tee /etc/rancher/k3s/config.yaml <<EOF
cluster-init: true
node-ip: 172.31.18.87
advertise-address: 172.31.18.87
tls-san:
  - 54.236.224.68
disable: [servicelb, traefik]
EOF
curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -

Step 3: Joining Peer Masters (Master 2 & 3)

# Example for Master-2
sudo tee /etc/rancher/k3s/config.yaml <<EOF
server: [https://172.31.18.87:6443](https://172.31.18.87:6443)
token: <SECRET_TOKEN>
node-ip: 172.31.24.255
disable: [servicelb, traefik]
EOF
curl -sfL [https://get.k3s.io](https://get.k3s.io) | sh -s - server

```
# 3. Evidence of Deployment

## 3.1 AWS Console View

<img width="1919" height="525" alt="Screenshot 2026-03-27 152305" src="https://github.com/user-attachments/assets/2cb2b381-c8e8-4c79-862b-2ffb7f7a65a4" />

Verification of three t3.large instances running in the us-east-1 region.

## 3.2 System Pods Health

<img width="957" height="985" alt="Screenshot 2026-03-27 151407" src="https://github.com/user-attachments/assets/009e42de-048d-43ba-ba9e-f9488f1dc970" />

Showing CoreDNS, Metrics-Server, and Local-Path-Provisioner running across the namespace.

## 3.3 Cluster Node Status

<img width="951" height="985" alt="Screenshot 2026-03-27 150147" src="https://github.com/user-attachments/assets/49d3178a-d2b8-4f4b-95ef-3bb146001247" />

Evidence showing all three nodes in 'Ready' status with control-plane roles.


## 3.4 Workload and Service Exposure

<img width="955" height="1030" alt="Screenshot 2026-03-27 151047" src="https://github.com/user-attachments/assets/ef5b48f8-8caf-415f-a0a2-9b49e90df777" />


To validate the cluster's ability to schedule and route traffic, a standard Nginx workload was deployed and exposed via a NodePort service.

- Service Configuration:

- Deployment Name: web-app

- Service Type: NodePort

- Internal Cluster IP: 10.43.117.155

- External Access Port: 30474
