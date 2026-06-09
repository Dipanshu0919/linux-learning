# ☁️ Lesson 2: Azure Basics — Microsoft Cloud

---

## 📚 Learning Objectives

- Azure kya hai
- Free tier use karna
- Azure VM create karna
- Azure App Service pe Python deploy karna
- Azure CLI use karna

---

## ☁️ Azure kya hai?

```
Microsoft Azure = Cloud Computing Platform
= Servers rent karo + Pay as you go

Services:
Azure VM         = Virtual Machine (Linux server)
Azure App Service = PaaS (Platform as a Service)
Azure Container  = Docker containers
Azure Functions  = Serverless
Azure DB         = Managed databases
Azure Storage    = File/blob storage
```

---

## 🆓 Azure Free Tier

```
Free 12 months:
- Linux VM (B1s): 750 hours/month
- Windows VM: 750 hours/month
- 5 GB Blob Storage
- Azure SQL Database: 250 GB/month

Always Free:
- Azure Functions: 1M requests/month
- App Service: 10 apps (F1 tier)
- Cosmos DB: 1000 RU/s, 25 GB

Setup: portal.azure.com
```

---

## 🛠️ Azure CLI Setup

```bash
# Install:
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash

# Login:
az login
# Browser khulega, login karo

# Subscription:
az account list
az account set --subscription "My Subscription"

# Version:
az --version
```

---

## 🖥️ Azure VM Create karna

```bash
# Resource Group:
az group create \
    --name myResourceGroup \
    --location eastus

# VM Create:
az vm create \
    --resource-group myResourceGroup \
    --name myLinuxVM \
    --image Ubuntu2204 \
    --admin-username azureuser \
    --generate-ssh-keys \
    --size Standard_B1s    # Free tier!

# Output:
# {
#   "publicIpAddress": "20.45.67.89",
#   ...
# }

# SSH se connect:
ssh azureuser@20.45.67.89

# Port open karo:
az vm open-port \
    --resource-group myResourceGroup \
    --name myLinuxVM \
    --port 80

az vm open-port \
    --resource-group myResourceGroup \
    --name myLinuxVM \
    --port 443

# VM stop/start:
az vm stop --resource-group myResourceGroup --name myLinuxVM
az vm start --resource-group myResourceGroup --name myLinuxVM

# VM delete (billing stop!):
az vm delete --resource-group myResourceGroup --name myLinuxVM
az group delete --name myResourceGroup --yes  # Full cleanup
```

---

## 🚀 Azure App Service — Easy Python Deploy

```bash
# App Service Plan (Free tier):
az appservice plan create \
    --name myAppPlan \
    --resource-group myResourceGroup \
    --sku F1 \
    --is-linux

# Python Web App create:
az webapp create \
    --resource-group myResourceGroup \
    --plan myAppPlan \
    --name my-flask-app-unique123 \
    --runtime "PYTHON:3.11"

# Deploy from GitHub:
az webapp deployment source config \
    --resource-group myResourceGroup \
    --name my-flask-app-unique123 \
    --repo-url https://github.com/yourusername/myflaskapp \
    --branch main \
    --manual-integration

# Environment variables:
az webapp config appsettings set \
    --resource-group myResourceGroup \
    --name my-flask-app-unique123 \
    --settings \
        SECRET_KEY=production-secret \
        DATABASE_URL=your-db-url \
        SCM_DO_BUILD_DURING_DEPLOYMENT=true

# App URL:
az webapp show \
    --resource-group myResourceGroup \
    --name my-flask-app-unique123 \
    --query defaultHostName \
    --output tsv
# Output: my-flask-app-unique123.azurewebsites.net

# Logs:
az webapp log tail \
    --resource-group myResourceGroup \
    --name my-flask-app-unique123
```

---

## 🗄️ Azure Database for PostgreSQL

```bash
# Managed PostgreSQL (no server management!):
az postgres flexible-server create \
    --resource-group myResourceGroup \
    --name mypostgres-unique123 \
    --location eastus \
    --admin-user adminuser \
    --admin-password "SecurePass123!" \
    --sku-name Standard_B1ms \
    --tier Burstable \
    --version 15

# Connection string:
az postgres flexible-server show \
    --resource-group myResourceGroup \
    --name mypostgres-unique123 \
    --query fullyQualifiedDomainName \
    --output tsv
# Output: mypostgres-unique123.postgres.database.azure.com

# Connection string format:
# postgresql://adminuser:SecurePass123!@mypostgres-unique123.postgres.database.azure.com/mydb
```

---

## 🏋️ Practice (Azure CLI locally)

```bash
# Azure CLI check:
if command -v az &>/dev/null; then
    az --version
    echo "Azure CLI available!"

    # Login (browser will open):
    # az login

    # List subscriptions:
    # az account list --output table
else
    echo "Install: curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash"
fi
```

---

## 📖 Summary

```
Azure VM           = Linux server on Microsoft cloud
Azure App Service  = Easy app hosting (no server manage)
Azure CLI          = Command line tool
az login           = Cloud login
az group create    = Resource group
az vm create       = Virtual machine
az webapp create   = Web app
Free tier          = 750 hours VM/month
portal.azure.com   = Web GUI
```

**Next:** `09-Cloud-And-Servers/03-AWS-Basics.md` → AWS cloud! ⚡
