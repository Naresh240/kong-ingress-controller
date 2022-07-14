# kong-ingress-controller

## Pre-Requisites

```bash
1. Install Helm
2. Create Hosted zone under Route53 with our domain name
3. SetUP Kong
4. SetUP External DNS
```

## Create Hosted zone under Route53 with our domain name

1. Created Hosted zone as shown in belo Image

![image](https://user-images.githubusercontent.com/58024415/178981125-b93531f8-0089-4554-92e7-82aba2d94e01.png)

2. COPY Name Servers Where we have purchased our Domain

![image](https://user-images.githubusercontent.com/58024415/178981591-167f0301-fd4b-4f90-8bdc-80dce265a6f4.png)

## SetUP Kong on EKS Cluster

```bash
## Kong Gateway (OSS) on Kubernetes native
kubectl apply -f https://bit.ly/kong-ingress-dbless

## Check the install status
kubectl get pods -n kong
```

## SetUP External DNS on EKS Cluster

```bash
# Create ```aws-creds.conf``` file with below content
[default]
aws_access_key_id = XXXXXXXXXXXX
aws_secret_access_key = XXXXXXXXXXXXXXXXXXXXXXXXX

# create secret with below command
kubectl create secret generic aws-creds --from-file=creds=./aws-creds.conf

# Also create ```exnaldns-values.yaml``` file
---			  
clusterDomain: <domain name>
aws:
  credentials:
    secretName: aws-creds
  region: "us-east-1"
  
# Installation of External DNS
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install external-dns bitnami/external-dns -f exnaldns-values.yaml
```

## Deploy Application using below commands

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
kubectl apply -f ingress.yaml
```

### Check pods, services and ingress details

```bash
kubectl get pods
kubectl get svc
kubectl get ingress
```

### Check Hosted zone for new records added or not
![image](https://user-images.githubusercontent.com/58024415/178982456-24ce10f3-44a0-4b3b-8ea8-946da7b3f3f5.png)
