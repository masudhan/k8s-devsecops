# kubernetes-devops-security

## Fork and Clone this Repo

## Clone to Desktop and VM

## NodeJS Microservice - Docker Image -
`docker run -p 8787:5000 siddharth67/node-service:v1`

`curl localhost:8787/plusone/99`
 
## NodeJS Microservice - Kubernetes Deployment -
`kubectl create deploy node-app --image siddharth67/node-service:v1`

`kubectl expose deploy node-app --name node-service --port 5000 --type ClusterIP`

`curl node-service-ip:5000/plusone/99`


## Trivy
`docker run --rm -v $HOME/Library/Caches:/root/.cache aquasec/trivy image python:3.4-alpine`
`docker run --rm -v $HOME/Library/Caches:/root/.cache aquasec/trivy image --severity CRITICAL chmadhus/numeric-app:18e56bdd6c`
`docker run --rm -v $HOME/Library/Caches:/root/.cache aquasec/trivy image --severity CRITICAL --exit-code 1 chmadhus/numeric-app:18e56bdd6c`
`docker run --rm -v $HOME/Library/Caches:/root/.cache aquasec/trivy image --severity LOW --exit-code 0 chmadhus/numeric-app:18e56bdd6c`