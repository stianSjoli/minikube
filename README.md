# Minikube configuration files ![main:branch](https://github.com/stianSjoli/minikube/actions/workflows/main_branch.yml/badge.svg)

This repository is primarily for my own exploration into cyber security.

## Cluster Architecture

The cluster will host applications in a typical virtual multi-team with trust between teams application and namespaces will be one isolation mechanism  [multi-tenancy](https://kubernetes.io/docs/concepts/security/multi-tenancy/). There will be 2 namespaces reserved for common use by apps (typically for app-teams), test and production, and one for a operations team with more privileges - platform. There will be one argocd-instance in its own namespace - argocd. The namespaces are defined in namespaces.yml. 

## Kubescape
Kubescape is run as a github action on pull requests, and green run is necessary before merge.

## Binary authorization 

Binary Authorization provides software supply-chain security for container-based applications by configure a policy that the service enforces when an attempt is made to deploy a container image on one of the supported container-based platforms. The policy will only allow correctly signed images to run on the minkube cluster. In this repo, it is achieved by:

1. creating a asymetric key pair with cosign 
2. signing a docker image with the private key 
3. pushing metadata to the docker registry 
4. installing kyverno on the minikube cluster 
5. writing and updating the cluster with a cluster policy 
6. applying the docker image as a deployment on the cluster 

1-3:
```
    $ cosign generate-key-pair
    $ cosign sign --key cosign.key docker.io/pasient0/simple-server:signed
      Enter password for private key: ...
```

4-6:
```
    $ minikube start 
    $ kubectl create -f https://github.com/kyverno/kyverno/releases/download/v1.10.0/install.yaml
    $ kubectl apply -f verify_image.yml 
    $ kubectl apply -f deployment.yml
```

Change image in deployment to check that only signed images are allowed to run on the cluster: 

replace:
```
spec:
    containers:
      - name: nginx-server
        image: docker.io/pasient0/simple-server:signed
        ports:
        - containerPort: 80
```

with: 

```
spec:
    containers:
      - name: nginx-server
        image: docker.io/pasient0/simple-server:unsigned
        ports:
        - containerPort: 80
```

## ArgoCD
Install on the cluster: 
```
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
```

