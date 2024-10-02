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


Nodes: 