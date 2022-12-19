# Intro and goal

We want to deploy [mattmezza/k8s-seminar-app](https://github.com/mattmezza/k8s-seminar-app) on a k8s cluster.

In order to obtain our goal, we need the following components:
- a deployment, a replica set and a pod for the APIs ([see api.yaml](api.yaml))
- a deployment, a replica set and a pod for the UI ([see ui.yaml](ui.yaml))
- a service for the APIs ([see api.svc.yaml](api.svc.yaml))
- a service for the APIs ([see ui.svc.yaml](ui.svc.yaml))
- an ingress for the web app ([see ingress.yaml](ingress.yaml))

We want to use a `minikube` cluster on our local machine and, after deployment, 
we want to have the following:
- UI hosted on `k8s-seminar.local`
- API hosted on `api.k8s-seminar.local`

# Setting up the cluster

Please follow instructions in [infra.md](../infra.md) to set up the cluster.

# Creating a namespace

We will create all the components for the app in a separate namespace. 
Namespaces are nice to keep things tidy. You can create one like this:
```
kubectl create namespace k8s-seminar
```

# Deploying the components

The pods will not use a local container registry shipped with `minikube`. 
Because we are building images with our local docker (and not the docker 
shipped within `minikube`) we need to make sure the images are loaded 
in `minikube`. We can do that by running the following:
```
minikube image load k8s-seminar-ui:1.0.0
minikube image load k8s-seminar-api:1.0.0
```
Obviously, this is only because we are using a `minikube` cluster.

To deploy all the components, from the root:
```
kubectl apply -f deployment
```

This will read all the yaml manifest in the specified directory and it will create 
the k8s components on the minikube cluster.

You can make sure the components are created by running the following:

```
kubectl get pods -n k8s-seminar
```
You should see something like this:
```
NAME                  READY   STATUS    RESTARTS   AGE
api-f7945fb76-mwpqx   1/1     Running   0          61m
ui-659f67849d-q92z7   1/1     Running   0          52m
```

# Resolving local host names

We want to resolve the following host names locally:
- `k8s-seminar.local`: for the UI
- `api.k8s-seminar.local`: for the API

In order to resolve these names correctly, we need to make a small edit to the `/etc/hosts` file on our local machine.
It is sufficient to append the following line to the file:
```
127.0.0.1 api.k8s-seminar.local k8s-seminar.local
```

# Don't forget to tunnel

As you have probably read in the [infra.md](../infra.md) file, because our `minikube` 
cluster runs via docker itself, we need to have a tunnel to reach it.
```
minikube tunnel
```


# Done

If everything worked fine, you should now be able to access the app by visiting 
[http://k8s-seminar.local](http://k8s-seminar.local).


### Some useful utilities

- `kubens`: to switch and pin the k8s namespaces (so that you don't have to always use the `-n` argument in `kubectl`
- `kubetail`: to tail logs for multiple pods at once (useful when you have replicas)
