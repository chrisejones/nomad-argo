# nomad-argo

## Introduction
This repo contains an [Argo Workflow](https://argoproj.github.io/projects/argo/) that demonstrates using Argo to deploy services on [Nomad](https://www.nomadproject.io/).

## Getting Started
I've tested this with Mac and WSL2 Docker Desktop. Before starting enable Kubernetes in Docker Desktop settings.

Set up Argo as specified in the docs, which I've duplicated below.
https://argoproj.github.io/argo/quick-start/

Install Argo:
```
kubectl create ns argo
kubectl apply -n argo -f https://raw.githubusercontent.com/argoproj/argo/stable/manifests/quick-start-postgres.yaml
```

Do this so that the Web UI is accessible on http://localhost:2746/
```
kubectl -n argo port-forward deployment/argo-server 2746:2746
```

You might want to test with a basic workflow at this point. The Argo CLI can be installed from Homebrew on Mac otherwise https://github.com/argoproj/argo/releases.
```
argo submit -n argo --watch https://raw.githubusercontent.com/argoproj/argo/master/examples/hello-world.yaml
```
If that works the carry on to run the nomad workflow.

Run a nomad instance on your host machine. It's available in Homebrew otherwise https://www.nomadproject.io/downloads. On Windows run it within WSL2.
```
nomad agent -dev
```

This workflow does an integration test. Starting a few dummy services with Nomad in a particular order and checking the logs to see if they've started.
```
argo submit -n argo --watch https://raw.githubusercontent.com/chrisejones/nomad-argo/main/nomad-workflow.yaml
```
It's interesting to go and look at the Web UI on http://localhost:2746/ while this is running.

Use this command to see the log output from the workflow.
```
argo logs -n argo @latest
```

Remove the Argo namespace when finished
```
kubectl delete namespaces argo
```
