# Minikube configuration files 

This repository is primarily for my own exploration into cyber security.

## Binary authorization 

Binary Authorization provides software supply-chain security for container-based applications by configure a policy that the service enforces when an attempt is made to deploy a container image on one of the supported container-based platforms. The policy will only allow correctly signed images to run on the minkube cluster. In this repo, it is achieved by:

- creating a asymetric key pair with cosign 
- signing a docker image with the private key 
- pushing metadata to the docker registry 
- installing kyverno on the minikube cluster 
- writing and updating the cluster with a cluster policy 
- applying the docker image as a deployment on the cluster 
 


