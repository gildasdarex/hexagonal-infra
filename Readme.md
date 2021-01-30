
```
 cd infra
 kubectl apply -f hexagonal-namespace.yaml
```

#### I - DEPLOY POSTGRES DATABASE

```
 cd infra/db
```

####### 1 - Create namespace for database
```
 kubectl apply -f 001-namespace.yaml
```

####### 2 - Create kubernetes pods/Deployment/Service for database
Set Postrgres users and password in deployment_values.yaml file

```
 helm repo add bitnami https://charts.bitnami.com/bitnami
 helm install db-postgres -f deployment_values.yaml bitnami/postgresql --namespace db
```


#### II - DEPLOY JENKINS

```
 cd infra/jenkins
```

####### 1 - Create new jenkins image and push to your repo

For registry env variable please , use your own docker registry and login with docker login command
```
 export registry = hexagonal/jenkins
 export tag = v1.0
 docker build -t $registry:$tag .
 docker tag  $registry:$tag
 docker push $registry:$tag
```

####### 1 - Create service account and role for jenkins
```
 kubectl apply -f roles/
```
a service account jenkins-sa will be created


####### 2 - Deploy jenkins kubernetes resources
```
 helm install ci-jenkins -f jenkins_deployment_values.yaml ./jenkins-k8s/helm/jenkins-k8s --set image.repository=$registry --set image.tag=$tag --set replicaCount=1 --namespace hexagonal-test
```


#### III - DEPLOY INGRESS CONTROLLER

```
 cd infra/ingress
```

####### 1 - Deploy Nginx Ingress Controller
```
 helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
 helm repo update
 helm install nginx-hexagonal-controller -f deployment_values.yaml ingress-nginx/ingress-nginx --namespace hexagonal-test
 kubectl apply hexagonal-ingress.yaml
```

####### 2 - Deploy Ingress
```
 kubectl apply hexagonal-ingress.yaml
```