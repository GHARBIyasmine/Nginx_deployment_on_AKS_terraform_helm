# **Deploying NGINX on Azure AKS Using Helm and Terraform**

## **Overview**
This project demonstrates the deployment of an NGINX application on an Azure Kubernetes Service (AKS) cluster. The infrastructure is provisioned using Terraform, and the application deployment is managed via Helm. The deployment is automated with a GitHub Actions pipeline, ensuring seamless integration and deployment.

---

## **Prerequisites**
Before using this project, ensure you have the following:
- **Tools:**
  - [Terraform](https://developer.hashicorp.com/terraform/tutorials) (>= 3.0)
  - [Helm](https://helm.sh/docs/)
  - [Azure CLI](https://learn.microsoft.com/en-us/cli/azure/install-azure-cli)
  - [kubectl](https://kubernetes.io/docs/tasks/tools/)
  - A GitHub account with access to create repositories and manage Actions.

- **Accounts and Permissions:**
  - Azure account with sufficient privileges to create resource groups and AKS clusters.
  - GitHub Secrets configured for authentication (see below).

- **Secrets Configuration:**
  Add the following secrets to your GitHub repository:
  - `AZURE_CREDENTIALS`: Contains the Azure service principal credentials for authentication.

---

###  Terraform Deployment
1. Initialize Terraform:


```
cd terraform
terraform init
```

2. Plan Deployment:

```
terraform plan
```

3. Apply Deployment:

```
terraform apply -auto-approve
```

This will create:

- A resource group
- An AKS cluster with a system-assigned identity and a default node pool
- Outputs: After successful deployment, Terraform outputs the resource group name and AKS cluster name.

### Helm Deployment
1. Configure AKS Credentials:
   
```
az aks get-credentials --resource-group projectrsgrp --name akscluster
```

2. Deploy NGINX with Helm:
   
```
helm upgrade --install nginx-release ./helm
```

## GitHub Actions Workflow

The pipeline automates the deployment process:

1. Terraform Job:

- Initializes, plans, and applies Terraform to provision the infrastructure.
- Outputs the AKS cluster and resource group details.
  
2. Helm Deployment Job:

- Waits for the Terraform job to complete.
- Authenticates with Azure, retrieves AKS credentials, and deploys the NGINX application using Helm.
  
### Triggering the Workflow
The pipeline can be triggered manually via GitHub’s workflow dispatch.

## Testing the Deployment
1. Access the NGINX Service: Once deployed, the NGINX service can be accessed via the public IP assigned by the Kubernetes LoadBalancer.

2. Verify Deployment:

- Check the pods:
```
kubectl get pods
```

- Check the service:
  
```
kubectl get svc
```

## Troubleshooting
1. Terraform Errors:

- Ensure Azure credentials are valid.
- Verify Terraform syntax with terraform validate.
  
2. Helm Deployment Issues:

- Ensure the AKS cluster credentials are correctly retrieved.
- Check Helm logs:
```
helm list
```

3. Pipeline Failures:

- Review logs for each job in GitHub Actions for details on errors.
