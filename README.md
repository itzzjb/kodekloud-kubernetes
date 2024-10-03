# What is Kubernetes?

With kubernetes you will be able to works with thousands of containers.

```sh
# Running 1000 container instances
kubectl run --replicas=1000 my-web-server
```

```sh
# Scaling up and down the containers
kubectl scale --replicas=2000 my-web-server
# Can also be configured to scale up and down automatically
```

```sh
# Can upgrade all of the containers in a rolling fashion
kubectl rolling-update my-web-server --image=web-server:2
```

```sh
# Can also rollback if any problem occurred
kubectl rolling-update my-web-server --rollback
```

With kubernetes you will also be able to define the state of the application using code,
and kubernetes will ensure that the state that you defined in the code is maintained at all times.
Even if things go down or something change kubernetes will make sure that your application get backs to the **declarative state**.

### High Level View

**Nodes**:

- A node is a machine physical or virtual which kubernetes is installed. And nodes is a worker machine and this is where the containers will be launched nby kubernetes. Also if a node fail our application will go down, so we need to have more than 1 node.

1. **kubelet** - This agent is responsible for making sure that the containers are running on the nodes as expected.
2. **kube-proxy** - This is responsible for maintaining rules on the nodes. Also help to communicate with each other, the worker nodes and the containers. This is more ,.of the networking component.
3. **container-runtime** - This is responsible for running containers. We are running applications in clusters in the form of containers. The runtime in kubernetes is called "container d".

**Cluster**:

- A cluster is a set of nodes grouped together. This way even if one node fails you have the application available from other nodes. And having multiple nodes helps in sharing load between nodes as well.

**Control Plane (Master Node)**:

- The control plane is another node with kubernetes components installed init. The control plane watches over the nodes in the cluster and is responsible for the actual orchestration of the containers of the worker nodes.

1. **kube-apiserver** - Acts as the frontend of kubernetes. The users, management devices, command line interfaces all talk to the API server to interact with the kubernetes cluster.
2. **etcd** - This is a distributed and reliable key value store used by kubernetes to store all data used to manage the cluster. This is where information about the nodes in the cluster and the application running on the cluster and any other
3. **controller-manager** - This is the brain behind the orchestration. They are responsible for noticing and responding when nodes, containers and endpoints go down. The controllers make decisions to bring up new containers in such cases.
4. **kube-scheduler** - This is responsible for distributing work or containers across multiple nodes. It looks for newly created containers and assigns them to nodes.

# Kubectl

Kubectl is the command line utility of kubernetes. This is tool or command that we use to operate the kubernetes cluster.

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
```

```sh
# For more information we can use -o wide
kubectl get nodes -o wide
```

# Pods

Let's assume that the application is already developed and build into docker images and it is available on a docker repository like dockerhub. So, kubernetes can pull it down. And we also assume the kubernetes cluster is already setup and it is working.

In kubernetes, our ultimate aim is to deploy our application in the form of containers in a set of machines that are configures as worker nodes in a cluster.

However kubernetes doesn't deploy containers directly on the worker nodes. The containers are encapsulated into kubernetes objects known as **pods**.

A pod is a single instance of an application. Pod is the smallest object that we can create in kubernetes.

To scale it up what we are going to do is create more pods instead of having multiple applications in the same pod.

However the one-to-one relationship is not a strict rule. It is common practice to have a helper container or a side car container along with the main application. This could be an agent that collects logs and monitors the applications and reports to third party.

```sh
# To create pods from a image that is in the dockerhub
# We give a name to the pod and specify the image of the pod
kubectl run nginx --image=nginx
```

```sh
# To see the list of pods available
kubectl get pods
```

The above way is the imperative way of creating pods and running them. Normally in complex real world scenarios we are using the **declarative way** by using YAML files.

# YAML Files

Kubernetes uses YAML files as inputs for many different cases. A kubernetes definition files contains 4 top level fields. The **_apiVersion, kind, metadata and spec_**.

**apiVersion** :

- This is the version of the kubernetes api we are using to create the object. Depending on what we are trying to create we must use the right API version.

| Kind       | Version |
| ---------- | ------- |
| Pod        | v1      |
| Service    | v1      |
| ReplicaSet | apps/v1 |
| Deployment | apps/v1 |

**kind** :

- Refers to the kind of object that we are creating, like **Pod**. Remember that this is case-sensitive.
- Some other possible values here are **Service, ReplicaSet and Deployment**.

**metadata**:

- Meta data is the data about the object like it's name , labels etc.

**spec**:

- This is the specification section which is written as spec. Depending on the object we are going to create, this is where we are going to provide additional information to kubernetes regarding that object. This will be different from object to object.

### Pod Definition File

```yaml filename="pod-definition.yml"
apiVersion: v1
kind: Pod
metadata:
  name: myapp-pod
  # this labels helps you to identify these objects in later point in time.
  # can be used to filter pods in a way like frontend, backend etc.
  labels:
    app: myapp
    type: front-end
spec:
  containers:
    # this container property is a list because we are able to have multiple containers in a single pod
    - name: nginx-container
      image: nginx
```

Once the file is created we can the following command in order to kubernetes to create the pod.

```sh
# creating a pod using a pod-definition.yml file
# -f stands for file
kubectl create -f pod-definition.yml
```

```sh
# To see all pods in the cluster
kubectl get pods
```

```sh
# For more information we can use -o wide
kubectl get pods -o wide
```

```sh
# in order to get a more detailed information about a specific pod
kubectl describe pod myapp-pod
```

```sh
# delete a existing pod
kubectl delete pod myapp-pod
```

```sh
# if we do some changes to the pod-definition file we can apply those changes using the following command
kubectl apply -f pod-definition.yml
```
