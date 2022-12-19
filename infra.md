# Installing `minikube`

Please follow [these instructions](https://minikube.sigs.k8s.io/docs/start/) according to your platform.

Hint: use a package manager (e.g. `brew` (on macOS), `choco` (on Windows))

# Start `minikube`

As in `minikube start`. This will take a while because it will pull a bunch of images from the network.

# Use `kubectl`

By default, `minikube` will set up `kubectl` to use itself as a cluster.

Try running `kubectl get nodes`: you should see something like this:
```
NAME       STATUS   ROLES           AGE    VERSION
minikube   Ready    control-plane   117s   v1.25.3
```

# Enabling the ingress addon

To use ingresses, we need some extra components on our k8s cluster.
In `minikube` we can get these components by enabling the following addon:

```
minikube addons enable ingress
```

You can check that the extra components are installed via:

```
kubectl get pods -n ingress-nginx
```
You should see something along these lines:
```
NAME                                        READY   STATUS      RESTARTS      AGE
ingress-nginx-admission-create-nrtcn        0/1     Completed   0             14h
ingress-nginx-admission-patch-6kctc         0/1     Completed   0             14h
ingress-nginx-controller-5959f988fd-kwjnw   1/1     Running     1 (13h ago)   14h

```
# Accessing the cluster from your local machine

By default, `minikube` runs itself via `docker` and therefore needs to be made 
accessible from your local host. You can do this by running the following 
command in a separate terminal (it might require `sudo` privileges):
```
minikube tunnel
```

# Scale your cluster up

By default, `minikube` is really mini: you only get 1 single master node.

To spice things up, we'll try to create a more realistic setup by adding in 2 extra workers.

```
minikube node add --worker  # will take a while
minikube node add --worker  # will take a while
```


Coolio! If everything worked so far, running `kubectl get nodes` should now print something like this:
```
NAME           STATUS   ROLES           AGE   VERSION
minikube       Ready    control-plane   12m   v1.25.3
minikube-m02   Ready    <none>          92s   v1.25.3
minikube-m03   Ready    <none>          27s   v1.25.3
```

As you can see, we've got 3 nodes in total, 1 master (acting as control plane) and 2 workers.
