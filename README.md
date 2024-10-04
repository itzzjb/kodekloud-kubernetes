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

### YAML Files

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
# this can also be used as a alternative to create as well
kubectl apply -f pod-definition.yml
```

```sh
# we can get the pod-definition file using the --dry-run option
# the following command will output the pod-definition file that we would need to create the pod-definition.yml file
kubectl run redis --image=nginx --dry-run=client -o yaml
```

```sh
# we can redirect the about standard output as a standard input to a file
kubectl run redis --image=nginx --dry-run=client -o yaml > pod-definition.yml
```

# Replica Sets

If we only have a single pod running our application, for some reason our pod fails our application will be down. Users will no longer able to access out application.

To prevent users from loosing access to our application we would like to have more than one instance or pod running at the same time. And that way even if one fails we still have our application running.

And replica set get the failed one back, ensuring **predefined number of replicas are always running at all times**. Helps us running multiple instances of a single pod in the kubernetes cluster providing **high availability**.

Even if you have a single pod, the replica set can help by automatically bringing up a new pod when an existing one fails.

We can also use the replica sets to create multiple pods and share the load across them. We deploy these pods across other nodes in the cluster. So, it helps us balance the load across multiple pods on different nodes as well as scale our application when the demand increases.

A pod has **one-to-one relationship with a node**. A pod can only run on one node at a time. You cannot move a running pod from one node to the other. You will have to kill it and recreate it on another node.

Technically the schedular decides which node a pod gets assigned to. And there are ways to control that.

### Replicaset Definition file

```yaml filename="replicaset-definition.yml
# replica set support apiVersion apps/v1
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-replicaset
  labels:
    app: myapp
    type: front-end
spec:
  # spec defines whats inside this object
  # here the replica set creates multiple instances of a pod
  # we create a template section under spec to provide a pod template
  # ... to be used by the replica set to create replicas
  template:
    # here what we are going to do is to get the metadata and spec sections of the pod-definition file
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  # inputting the number of  replicas that we need to create from the pod template.
  replicas: 3
  # replicaSet requires a selector sections
  # helps to identify what pods fall under the replica set
  # this is because replica sets can also manage pods that are not created as a part of the replica set creation process
  # for example there might be pods that are created before that replica set creation.
  selector:
    # matchLabels is used to match the labels of the pods
    # all the pods that have the same labels will be managed by the replica set
    # the pods that should be managed by a specific replica set must have the same label (so we can filter)
    matchLabels:
      type: front-end
```

Once the file is created we can the following command in order to kubernetes to create the replicas.

```sh
# creates the replica set using the replicaset-definition.yml file.
kubectl create -f replicaset-definition.yml
```

```sh
# to get list of the replica sets available
kubectl get replicaset
# or
kubectl get rs # rs - can be used to anywhere we have replicaset
```

```sh
# to see all the pods including the pods created using the replica set
kubectl get pods
```

```sh
# to get more information on the replica set list
kubectl get replicaset -o wide
```

```sh
# or to find more details about a specific replica set we can use the following command
kubectl describe replicaset myapp-replicaset
```

If we want to change the number of replicas (scale up and down) we can change the number of replicas we can change the number of replicas in the replicaset-definition.yml file. Then run the following command.

```sh
# this will update the replica set
kubectl replace -f replicaset-definition.yml
```

Or, we can use the following way instead.

```sh
# we can either input the definition file
kubectl scale --replicas=6 -f replicaset-definition.yml
```

```sh
# or the replicaset name
kubectl scale --replicas=6 replicaset myapp-replicaset
```

```sh
# to delete a replica set
kubectl delete replicaset myapp-replicaset
```

```sh
# if we have already created a replica set and need to find the replicaset-definition file and edit it we can use the following command
# this will open this file using vim
# i -> insert mode (start typing)
# :w -> write(save)
# :q -> quit if the file is saved
# :wq or :x -> write(save) and quit
# :q! -> quit without save
kubectl edit replicaset myapp-replicaset
# once updated we need to delete all the existing pods of that replica set
# then the replica set will automatically spin of new pods with the new configurations we have updated
```

However using the second way will not result in number of replicas being updated automatically in the file. Even though we scale the number of replicas tp be 6 the number of replicas stated in the file would be 3.

So, the recommended approach is to always create yml files and edit the yml files. That way there won't be any difference between the actual state of the environment and what's defined in the yml files.

There are also options to automatically scale based on the load.

# Development

Even though we can deploy an application using pods and replicas it won't cut it when deploying applications for the production use cases.

When newer versions of application released you would like to upgrade your application instances seamlessly. You may want to upgrade those instances one after the other. These kinds of updates are known as **rolling updates**. Also if we want to undo the recent update you would be able to **roll back** the changes that are recently carried out.

And finally, if you want to make multiple changes to the environment like upgrading the underline web server versions, scaling your environment and modifying the resource allocation etc.

You don't want each change to be applied immediately after the change is run. Instead you would like to apply a **pause** to your environment, make the changes and then **resume** so all changes are rolled out together. All of these capabilities are available with Kubernetes deployment.\

### Deployment definition file

The deployment-definition.yml file is same as the replicaset-definition.yml file. The only change that need to make is to the kind section.

```yml
apiVersion: apps/v1 # The apiVersion is apps/v1 here
kind: Deployment # This is where it changes from replica set
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        labels:
          app: myapp
          type: front-end
    spec:
      containers:
        - name: nginx-container
          image: nginx
  replicas: 3
  selector:
    matchLabels:
      type: front-end
```

After creating the deployment-definition.yml file we can run the following commands

```sh
# this will create the deployment
kubectl create -f deployment-definition.yml
```

```sh
# we can see the list of all the deployments
kubectl get deployments
```

The deployment automatically creates a new replicaset.

```sh
# we can all the replica sets with the ones that are created by the deployments
kubectl get replicaset
```

```sh
# to the all the pods including the pods that are created by the replica set of the deployment
kubectl get pods
```

```sh
# we are able to see all the objects (pods, replicaset, deployments ...) in a single command
kubectl get all
```

We might need to update or upgrade the pods of the deployment to a newer version

```sh
# we need to update the deployment-definition file first (update the image name with the newer version of the app)
# this is the declarative approach
kubectl apply -f deployment-definition.yml
```

```sh
# there is a imperative approach as well
# here we are changing the image of the container
kubectl set image deployment/myapp-deployment nginx-container=nginx:1.9.1
```

In a scenario where we have the name, image, and the number of replicas there is also an imperative way to create the deployments.

```sh
# imperative way of creating a deployment
kubectl create deployment myapp-deployment --image=nginx --replicas=3
```

# Services

We can many pods in the cluster. Those pods may contain containers of web servers, databases etc. Each pod will get assigned a IP address. All the pods have internal ip addresses which they can used to communicate with each other.

But these IP addresses are specific for these pods, and when a pod goes down the ip address goes down as well. So, we can't use these IP addresses in code. This is where services comes in. Service enables communication between application within a kubernetes cluster.

Services acts as a load balancer or a proxy, it provides an endpoint to other services to connect to.

Similarly, to expose the service to the outside to the external users, we need to create another service.

So, a service **enables connectivity between applications within the cluster as well as to expose applications outside the cluster to end users**.

1. **Cluster IP Service**:
   - This is a service **_within the cluster_**, that i not exposed externally and helps different services communicate with each other.
2. **Node Port Service**
   - The service exposes the application on a **_port on the Node_** to be made **_accessible to the external users_**.
3. **Load Balancer Service**
   - This provisions a load balancer for our service and supported providers. Good example is to distribute load across different web servers on these cloud environments.

### Cluster IP Service

Pods are usually deployed in replicas. So there could be multiple instances of the same pod and also there could be hundreds of other pods too. So, to identify the specific pods that we need to route traffic to, the labels and selectors are used. S

We can add the label of the specific pods as a selector in the service.

### Cluster IP Service Definition File

```yml
apiVersion: v1 # the apiVersion should be v1
kind: Service
metadata:
  name: myapp-service # name of the service
spec:
  type: ClusterIP # this is the default type
  ports:
    # ports are defined in the perspective of the service.
    - targetPort: 8080 # this is the port that the application that runs in the pod is listening on
      port: 8080 # this is the port that the service is exposed
  # selector is used to link the service to a set of pods
  selector:
    # we can refer to the pod-definition.yml file and copy the labels section here
    app: myapp
    type: front-end
```

After creating the service-definition.yml file we can use the following commands.

```sh
# creating the service
kubectl create -f service-definition.yml
```

```sh
# to check get the list of all the services with newly created service
kubectl get services
```

The service can be accessed by other pods using the Cluster IP or the name of the service.

### Node Port Service

This is a type of service where a normal service is created first, and then exposed to external users through a **port on the Node**.

Node port can only be in a valid range from **30000 - 32767**.

### Node Port Definition File

This is very similar to the cluster-ip-service-definition.yml but there are a little bit of changes.

```yml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
spec:
  type: NodePort # changed the type of the service to NodePort
  # you can have multiple port configurations because this is a list
  ports:
    - targetPort: 8080 # if we don't provide the target port it is assumed to be same as the port
      port: 8080 # this is mandatory to provide
      nodePort: 30008 # this is the port in the Node that we are going to use the expose the application to the outside.
      # if we don't provide the nodePort a free port from the range 30000 - 32767 will be allocated automatically.
  selector:
    # copied from the pod-definition.yml file
    app: myapp
    type: front-end
```

After creating the node-port-service-definition.yml file we can use the following commands.

```sh
# to create the service
kubectl create -f node-port-service-definition.yml
```

```sh
# to get the list of services with the newly created services
kubectl get services
```

When there are **_multiple pods_** in **_same or different Nodes_** the service will automatically distribute the load between them. The service acts as a load balancer and the algorithm that is used is random. To change the algorithm or to do more configurations about the load is balanced, it's where the service meshes comes in.

When the pods are distributed across multiple nodes, when a service is created without us having to do any additional configuration kubernetes creates a service that spans across all nodes in the cluster. And will **_match the target port of the pods to the same node port of all the nodes in the cluster_**.

This way you can access the application with the **_IP of any node in the cluster and the same nodePort number_**.

This will also be available on nodes, where even if the pods are not scheduled.
