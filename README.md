# Install the following on your machine

1. [Docker](https://docs.docker.com/get-docker/)
  - Test with `docker version`

2. [kubectl](https://kubernetes.io/docs/tasks/tools/#kubectl)
  - Test with: `kubectl version`
  
3. [kind](https://kubernetes.io/docs/tasks/tools/#kind)
  - Test with: `kind version`

---

## Create kind configuration file and start kind

Create a cofiguration file for kind: `kind-config.yml`

```
apiVersion: kind.x-k8s.io/v1alpha4
kind: Cluster
nodes:
- role: control-plane
  extraPortMappings:
  - containerPort: 31111
    hostPort: 31111
    listenAddress: "0.0.0.0" # Optional, defaults to "0.0.0.0"
    protocol: tcp # Optional, defaults to tcp
- role: worker
```

Now start kind using this config file: `kind create cluster --config=kind-config.yml`

>> Note: This file is required to run local kubernetes Nodeport as [described here](https://kind.sigs.k8s.io/docs/user/quick-start/#mapping-ports-to-the-host-machine)

---

## Kubernetes configuration files

Pick a folder to create the kubernetes declarative files:

A simple app running in a docker continer wrapped in a pod. Let's use nginx demo image. Create a file called: 

hello-pod.yml

```
apiVersion: v1
kind: Pod
metadata:
  name: hello-pod
  labels:
    app: nginx
spec:
  containers:
    - name: web-nginx
      image: nginxdemos/hello:latest
      ports:
        - containerPort: 80
```

---

3. A kubernetes nodeport service, create a file called:

svc-config.yml

```
apiVersion: v1
kind: Service
metadata:
  name: nginx-nodeport
spec:
  type: NodePort
  ports:
  - port: 8000
    targetPort: 80
    nodePort: 31111
    protocol: TCP
  selector:
    app: nginx
```

---

Apply configurations: 

`kubectl apply -f hello-pod.yml`

`kubectl apply -f svc-config.yml`

---

Access your running kubernetes setup application on browser navigate to : 

[http://localhost:31111](http://localhost:31111)


Some useful commands: 

`docker ps` -> show running docker containers used by kind to create the kubernetes control plane and worker.

`kubectl cluster-info` -> display kind cluster info.

`kubectl describe svc $svc_name` -> describes service

`kubectl get pods` -> show pod information

`kubectl get pods --show-labels`

`kubectl version --short`

`kind get clusters` -> display kluster names

``

Delete commands: 

`kubectl delete pod $pod_name`

`kubectl delete svc $service_name`

`kind delete cluster $cluster_name`

This is it, no BS.... it's done! 


