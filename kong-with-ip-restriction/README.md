# kong-ingress-controller-with-ip-restriction

## Pre-Requisites

```bash
1. EKS Cluster
2. Install Helm
3. Create Hosted zone under Route53 with our domain name
4. SetUP Kong
5. SetUP External DNS
```

## SetUP EKS Cluster

[EKS Cluster Setup](https://github.com/Naresh240/kubernetes/blob/main/eks-cluster-setup/eks-cluster-with-eksctl/README.md)

## Helm Installation with Specific Version

```bash
curl -fsSL -o get_helm.sh https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3
chmod 700 get_helm.sh
./get_helm.sh -v v3.8.2
```
## Create Hosted zone under Route53 with our domain name

1. Created Hosted zone as shown in belowImage

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
## Updated IP Range under ```kong-ip-restriction-plugin.yaml``` file

![image](https://user-images.githubusercontent.com/58024415/179220719-a4380382-1afc-43d5-83dd-684526c75393.png)

## Add Kong IP Restriction plugin under ```ingress.yaml``` file

![image](https://user-images.githubusercontent.com/58024415/179221004-0d419ed3-9f80-4f42-a746-f70c1047e925.png)

## Deploy Application using below commands

```bash
kubectl apply -f kong-ip-restriction-plugin.yaml
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

### Output of application in UI

![image](https://user-images.githubusercontent.com/58024415/179221288-0d17dcca-04e7-4d44-b4bf-ad9127a9d162.png)
