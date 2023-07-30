# Connect to Azure from GitHub actions without Secret

Step by step guide to help connect Azure from GitHub Actions without using client secret/certificate.

### Blog Post

https://iqan.medium.com/connect-to-azure-from-github-actions-without-using-client-secrets-476dae0a5600

## Set Up SPN and Federated Credentials

### Register application in AAD

```bash
az ad app create --display-name gh-connect-app
```

### Create SPN for the application registered above

```bash
appId=(az)
az ad sp create --id <id from output of above command>
```

### Assign "Contributor" role to the SPN for your subscription

```bash
$subscriptionId = "<your subscription id>"
$assigneeObjectId = "<object id for SPN created above>"
az role assignment create --role contributor \
  --subscription $subscriptionId \
  --assignee-object-id  $assigneeObjectId \
  --assignee-principal-type ServicePrincipal \
  --scope /subscriptions/$subscriptionId
```

### To get app details use

```bash
az ad app list --filter "startswith(displayname, 'gh-connect-app')" --query "[].{name:displayName, appId:appId, objectId:id}"
```

### Add federated credentials

```bash
az ad app federated-credential create --id <APPLICATION-OBJECT-ID> --parameters credential.json
```
