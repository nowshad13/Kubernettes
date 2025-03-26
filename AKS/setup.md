## Script to create AKS

```bash

export RANDOM_ID="$(openssl rand -hex 3)"
export MY_RESOURCE_GROUP_NAME="myAKSResourceGroup$RANDOM_ID"
export REGION="eastus"
export MY_AKS_CLUSTER_NAME="myAKSCluster$RANDOM_ID"
export MY_DNS_LABEL="mydnslabel$RANDOM_ID"
az group create --name $MY_RESOURCE_GROUP_NAME --location $REGION
az aks create --resource-group $MY_RESOURCE_GROUP_NAME \
    --name $MY_AKS_CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --node-vm-size 'Standard_B2s'

```
```bash
az aks get-credentials --resource-group $MY_RESOURCE_GROUP_NAME --name $MY_AKS_CLUSTER_NAME
```


Resource deletion:

```powershell
# Get all resource groups except "SSH_Key"
$resourceGroups = az group list --query "[?name!='SSH_Key'].name" -o tsv

# Loop through each resource group and delete it
foreach ($rg in $resourceGroups) {
    Write-Output "Deleting resource group: $rg"
    az group delete --name $rg --yes --no-wait
}

Write-Output "Deletion process started for all resource groups except 'SSH_Key'."

```