# A Kubernetes cheatsheet

## Setting up local environment on MacOS

Install an hypervisor (Such as virtualbox)
Install kubectl (kubernetes-cli)

```bash
# Install kubectl
brew install kubernetes-cli

# Verify
kubectl version
```

Install minikube

```bash
# Install minikube
brew cask install minikube
```

## General information

```bash
# Get cluster info
kubectl cluster-info

# Get nodes info
kubectl get nodes

# Get pod info
kubectl get pods

# Get services
kubectl get services

# Get deployments
kubectl get deployments

# Detailed info
kubectl describe nodes

# Print pod logs
kubectl logs $POD_NAME

# Run a command on a container inside a pod
kubectl exec $POD_NAME echo 'hello from inside the pod!'

# Open a bash inside the pod
kubectl exec -ti $POD_NAME bash

# Append -help to get more info on the command
kubectl get nodes --help

```

## Starting a cluster

```bash
# Start minikube
minikube start
```

## Deploy to a cluster

```bash
# Check that the cluster is available
# The output should return both a master and a client
kubectl version

# Create a new deployment
# Notes
# a)Images hosted outside of dockerhub should be specified
# with full url
kubectl run deploy-name  
  --image=gcr.io/google-samples/kubernetes-bootcamp:v1
  --port=8080

# In a separate terminal, create a proxy to enable
# communication with the clusters
kubectl proxy
```

## Services commands

```bash
# Expose the port on a deployment to external traffic and
# create a service
# Check the Using source IP link for more info on types
kubectl expose deployment/kubernetes-bootcamp
  --type="NodePort"
  --port 8080

# Removes a service
kubectl delete service -l run=kubernetes-bootcamp
```

## Label your pods

```bash
# Label a pod with APP = v1
kubectl label pod $POD_NAME app=v1

# Fetch pods by label
kubectl get pods -l app=v1
```

## Scale

Default deployment name is kubernetes-bootcamp

```bash
# Check deployment state
# DESIRED: Desired number of replicas
# CURRENT: Current number of replicas
# UP-TO-DATE: Replicas matching the current desired state
# AVAILABLE: Replicas available to users
kubectl get deployments

# Set the desired replica states for a deployment
# Scaling up
kubectl scale deployments/kubernetes-bootcamp --replicas=4

# Scaling down
kubectl scale deployments/kubernetes-bootcamp --replicas=2

# Verify deployments again to check the current state
kubectl get deployments

# Check number of pods that changed
kubectl get pods -o wide

# Check changes made to pods
kubectl describe deployments/kubernetes-bootcamp
```

## Cluster API commands

locahost:8001 is the url of the cluster api

```bash
# Export the pod name to a environment variable
# go-template is how the output will be formated with Go
export POD_NAME=$(kubectl get pods -o go-template --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

# Make a request to the api
curl http://localhost:8001/api/v1/namespaces/default/pods/$POD_NAME/proxy/

# Test exposed app connection
curl $(minikube ip):$NODE_PORT
```

## Resources and quick links

* [Learn Kubernetes Basics](https://kubernetes.io/docs/tutorials/kubernetes-basics/)
* [Understading Kubernetes Architecture](https://kubernetes.io/docs/tutorials/kubernetes-basics/explore/explore-intro/)
* [Services Source IP Types](https://kubernetes.io/docs/tutorials/services/source-ip/)
* [Labels and selectors](https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/)
