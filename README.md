# What is Kubernetes?

With kubenetes you will be able to workwith thosands of containers.

```sh
# Running 1000 container instances
kubectl run --replicas=1000 my-web-server
```
```sh
# Scaling up and down the containers
kubeclt scale --replicas=2000 my-web-server
# Can also be configured to scale up and down automatically
```
```sh
# Can upgrade all of the containers in a rolling fashion
kubectl rolling-update my-web-server --image=web-server:2
```
```sh
# Can also rollback if any problem occured
kubectl rolling-update my-web-server --rollback
```

With kubernetes you will also be able to define the state of the application using code, 
and kubenetes will ensure that the state that you defined in the code is maintained at all times. 
Even if things go down or something change kubenetes will make sure that your application get backs to the **declaritive state**.

### High Level View

**Nodes**: 
- A node is a machine physical or virtual which kubernetes is installed. And nodes is a worker machine and this is where the containers will be launched nby kubernetes. Also if a node fail our application will go down, so we need to have more than 1 node.
1. **kubelet** -  This agent is responsible for making sure that the containers are running on the nodes as expected.
2. **kube-proxy** -  This is responsible for maintaining rules on the nodes. Also help to communicate with each other, the worker nodes and the containers. This is more ,.of the networking component.
3. **container-runtime** - This is responsible for running containers. We are running applications in clusters in the form of containers. The runtime in kubernetes is called "container d".

**Cluster**:
- A cluster is a set of nodes grouped together. This way even if one node fails you have the application available from other nodes. And having multiple nodes helps in sharing load between nodes as well.

**Control Plane (Master Node)**:
- The control plane is another node with kubernetes components installed init. The control plane watches over the nodes in the cluster and is responsible for the actual orchestration of the containers of the worker nodes.
1. **kube-apiserver** - Acts as the frontend of kubernetes. The users, management devices, command line interfaces all talk to the API server to interact with the kubernetes cluster.
2. **etcd** - This is a destributed and relaiable key value store used by kubernetes to store all data used to manage the cluster. This is where information about the nodes in the cluster and the application running on the cluster and any other
3. **contoller-manager** - This is the brain behind the orchestration. They are responsible for noticing and responding when nodes, containers and endpoints go down. The controllers make decisions to bring up new containers in such cases.  
4. **kube-scheduler** - This is responsible for distributing work or containers across multiple nodes. It looks for newly created containers and assigns them to nodes.

# Kubectl 

Kubeclt is the command line utility of kubernetes. This is tool or command that we use to operate the kubernetes cluster.

```sh
# To identify the version of kubectl client and the kubernetes server along with any other tool related
kubectl version
```
```sh
# Lists basic help information such as basic commands
kubectl --help
```
```sh
# To see a list of nodes in the cluster
kubectl get nodes
# For more information we can use -o wide 
kubectl get nodes -o wide
```

