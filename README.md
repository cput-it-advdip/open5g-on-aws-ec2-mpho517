\# K3s Deployment on AWS - Graduate Trainee Assignment 1

\*\*Student Name:\*\* \[Mpho Quinton Swele]

\*\*Student Number:\*\* \[231001487]



---



\## 1. Architecture Explanation

K3s is a lightweight Kubernetes distribution. It is ideal for this deployment because it replaces the heavy etcd datastore with \*\*SQLite\*\*, reducing the memory overhead on our AWS instances.

\* \*\*Control Plane:\*\* Hosted on t3.medium.

\* \*\*Worker Nodes:\*\* Hosted on t3.micro.

\* \*\*Networking (CNI):\*\* Uses \*\*Flannel\*\* (VXLAN) for pod communication.

\* \*\*Ingress:\*\* Default \*\*Traefik\*\* controller for traffic management.



\## 2. Infrastructure Setup

I provisioned two Ubuntu 22.04 EC2 instances. The Security Group was configured with:

\* \*\*TCP 6443\*\*: K3s API Server

\* \*\*UDP 8472\*\*: Flannel VXLAN (Required for node-to-node pod traffic)

\* \*\*TCP 22\*\*: SSH Management



\## 3. Evidence of Deployment

!\[Nodes Status](./img/nodes\_status.png)

!\[Pods Status](./img/pods\_status.png)

!\[AWS Console](./img/aws\_console.png)



\## 4. Technical Reflection

\### What I Learned

During this Graduate Trainee assignment, I learned that setting up Kubernetes isn't just about the software, but the underlying networking. I initially struggled with the worker node joining the cluster until I realized the Flannel CNI requires specific UDP ports (8472) to be open in the AWS Security Group.



\### 5G and Scalability

K3s is highly relevant to \*\*5G Cloud-Native\*\* concepts. In 5G Edge computing, hardware is often resource-constrained. K3s provides a "light" orchestration layer to manage containerized network functions (CNFs) efficiently. Virtualization provides the hardware isolation, while K3s provides the scalability needed for high-demand 5G services.


## Deployment Evidence

### Multi-Node Cluster Status
![Nodes](img/nodes_status.png)

### System Pods Status
![Pods](img/pods_status.png)