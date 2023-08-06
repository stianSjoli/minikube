# Minikube configuration files 

This repository is primarily for my own exploration into cyber security.

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


