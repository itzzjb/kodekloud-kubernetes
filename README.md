# What is Kubernetes?

With kubenetes you will be able to workwith thosands of containers.

```sh
# Running 1000 container instances
kubectl run --replicas=1000 my-web-server

# Scaling up and down the containers
kubeclt scale --replicas=2000 my-web-server
# Can also be configured to scale up and down automatically

# Can upgrade all of the containers in a rolling fashion
kubectl rolling-update my-web-server --image=web-server:2

# Can also rollback if any problem occured
kubectl rolling-update my-web-server --rollback
```

With kubernetes you will also be able to define the state of the application using code, 
and kubenetes will ensure that the state that you defined in the code is maintained at all times. 
Even if things go down or something change kubenetes will make sure that your application get backs to the **declaritive state**.

### High Level View

**Nodes**: 
A node is a machine physical or virtual which kubernetes is installed. And nodes is a worker machine and this is where the containers will be launched nby kubernetes. Also if a node fail our application will go down, so we need to have more than 1 node.

**Cluster**:
A cluster is a set of nodes grouped together. This way even if one node fails you have the application available from other nodes. And having multiple nodes helps in sharing load between nodes as well.

**Control Plane (Master Node)**:
The control plane is another node with kubernetes components installed init. The control plane watches over the nodes in the cluster and is responsible for the actual orchestration of the containers of the worker nodes.